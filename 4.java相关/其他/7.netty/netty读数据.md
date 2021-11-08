## netty读数据
![技巧](pic2/0.jpg)
![流程](pic2/1.jpg)

#### nioeventloop

我们的读数据从eventloop开始,由workernio线程发起(之前介绍的bossnio线程的读是创建连接)

NioEventLoop.processSelectedKey(SelectionKey, AbstractNioChannel)方法中

	// Also check for readOps of 0 to workaround possible JDK bug which may otherwise lead
    // to a spin loop
    if ((readyOps & (SelectionKey.OP_READ | SelectionKey.OP_ACCEPT)) != 0 || readyOps == 0) {
         unsafe.read();
    }

这次他的实现和NioServerSocketChannel的不一样了,serverSocketChannel的是AbstractNioMessageChannel.NioMessageUnsafe#read,而socketChannel的是AbstractNioByteChannel.NioByteUnsafe.read()

代码如下:

        @Override
        public final void read() {
            final ChannelConfig config = config();
            if (shouldBreakReadReady(config)) {
                clearReadPending();
                return;
            }
            final ChannelPipeline pipeline = pipeline();
			//获取ByteBuf的分配器
            final ByteBufAllocator allocator = config.getAllocator();
			//动态决定分配多少内存的handler:: AdaptiveRecvByteBufAllocator
            final RecvByteBufAllocator.Handle allocHandle = recvBufAllocHandle();
            allocHandle.reset(config);

            ByteBuf byteBuf = null;
            boolean close = false;
            try {
                do {
					//第一次读默认分配1024
                    byteBuf = allocHandle.allocate(allocator);
					//记录每次读的大小
                    allocHandle.lastBytesRead(doReadBytes(byteBuf));
                    if (allocHandle.lastBytesRead() <= 0) {
                        // nothing was read. release the buffer.
                        byteBuf.release();
                        byteBuf = null;
                        close = allocHandle.lastBytesRead() < 0;
                        if (close) {
                            // There is nothing left to read as we received an EOF.
                            readPending = false;
                        }
                        break;
                    }
					//记录读了多少次
                    allocHandle.incMessagesRead(1);
                    readPending = false;
					//将读到的数据传递出去,这时netty的业务读的核心流程
                    pipeline.fireChannelRead(byteBuf);
                    byteBuf = null;
				//判断是不是继续读的关键逻辑
				//1.上次读的>0
				//2.读的次数没有满16次
				//3.上次读把bytebuff读满了
                } while (allocHandle.continueReading());
				//记录这次读事件总共读了多少数据,计算下次分配大小
                allocHandle.readComplete();
				//传播完成读事件
                pipeline.fireChannelReadComplete();

                if (close) {
                    closeOnRead(pipeline);
                }
            } catch (Throwable t) {
                handleReadException(pipeline, byteBuf, t, close, allocHandle);
            } finally {
                // Check if there is a readPending which was not processed yet.
                // This could be for two reasons:
                // * The user called Channel.read() or ChannelHandlerContext.read() in channelRead(...) method
                // * The user called Channel.read() or ChannelHandlerContext.read() in channelReadComplete(...) method
                //
                // See https://github.com/netty/netty/issues/2254
                if (!readPending && !config.isAutoRead()) {
                    removeReadOp();
                }
            }
        }
    }

查看扩容,缩容逻辑,发现2次才会缩,每次都会扩

        private void record(int actualReadBytes) {
            if (actualReadBytes <= SIZE_TABLE[max(0, index - INDEX_DECREMENT)]) {
                if (decreaseNow) {
                    index = max(index - INDEX_DECREMENT, minIndex);
                    nextReceiveBufferSize = SIZE_TABLE[index];
                    decreaseNow = false;
                } else {
                    decreaseNow = true;
                }
            } else if (actualReadBytes >= nextReceiveBufferSize) {
                index = min(index + INDEX_INCREMENT, maxIndex);
                nextReceiveBufferSize = SIZE_TABLE[index];
                decreaseNow = false;
            }

注意:读取数据流程由nio线程触发,并传播到流水线,读是一个inbound的过程,由pipeline流水线的head->tail.

outbound是从tail-> head

在DefaultChennelPipeline中有headhandler和tailhandler

从读的流程是从head-> 用户定义的handler -> tailerhandler,要是msg到tailhandler还没被处理的话,tailhandler会抛出异常!

相反,ctx.write操作和ctx.writeAndFlush的代码见DefaultChannelPipeline

	    @Override
	    public final ChannelFuture write(Object msg, ChannelPromise promise) {
	        return tail.write(msg, promise);
	    }
	
	    @Override
	    public final ChannelFuture writeAndFlush(Object msg, ChannelPromise promise) {
	        return tail.writeAndFlush(msg, promise);
	    }
可以发现,所有的写操作都是由tailhandler -> 用户handler-> headhandler,

先来看tailHanler:

![tail](pic2/2.jpg)

我们发现在tailhandler里面会判断调用方是否是nioworker线程,不是的话会打包成task切换到nio线程去执行write();



到了headhandler了以后呢?

        @Override
        public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) {
            unsafe.write(msg, promise);
        }

我们发现DefaultChannelPipeline.HeadContext.write(ChannelHandlerContext, Object, ChannelPromise)实际上调用unsafe.write
