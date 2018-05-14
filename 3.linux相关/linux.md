# **学习linux**
简单的操作省略~~

* **免密登录**  
通过RSA秘钥进行免密登录,比如我需要通过SSH发送文件,或执行命令,使用ssh命令
![](pic/1.png)
![](pic/2.png)  

		ssh ip -p SSH端口,默认22  
在输入对应的密码即可.使用
	 	exit  
退出,或者使用  
![](pic/3.png)

		scp [-r] [-P端口]  本机文件 root@ip:/远程目录  
关于RSA非对称秘钥,使用私钥签名,公钥验签,一个公私钥是一对.如果我有2台linux服务器A登录B,则AB要互相交换公钥才能相互免密登录.  
	* **如何生成公私钥对??**  
	![](pic/4.png)
		 
			ssh-keygen  
	生成一对公私钥,进入/root/.ssh/下其中pubkey为公钥,使用
			
			ssh-copy-id -p 22 ip  
	来copy rsa公钥至目标主机,反之同理.
	![](pic/5.png)