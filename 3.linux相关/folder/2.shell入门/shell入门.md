# **shell入门**
使用  
> pstree

来 查看进程的树结构.注意在不同的bash中定义的变量不可共享!在同一个bash中执行脚本要. ./xxx.sh  

* linux中变量分为系统变量和用户变量,可以使用set命令查看.
* 定义变量使用[变量=值]即可,注意等号2端不要有空格,双引号只会脱义空格,单引号脱义所有字符 
* 撤销变量[unset 变量名],即可,注意[readonly 变量=值]的静态变量撤销不了
* export 变量,可以吧变量提升为全局供其他shell使用
* 系统变量:$HOME,$PATH,$USER等,可在/etc/profile中定义
* 将命令的返回值赋给变量:如A=`ls -lha`或A=$(ls -lha)

		wordcount=$(wc -c helloworld.sh | cut -d " " -f1)
* 特殊变量:
	* $? 上一个命令退出的状态,命令的返回值,通常为0
	* $$ 当前进程号
	* $0 当前脚本名,在某个sh脚本中使用可获取其名
	* $n 表示n位置输入参数 如 ./test.sh hello world ,在脚本中用$1,$2获取hello和world
	* $# 表脚本输入变量个数,常用于循环获取参数
	* $* 和 $@表示可以遍历的参数list,拿到所有参数,当$*和$@在sh中不被双引号""包含时相同为一个一个,否则"$*"将全部参数作为整体,后者还是会将各个参数分开

* 运算符
expr m + n 或 $((m+n)),注意expr运算符左右空格
如计算(2+3)*4
	* echo `expr \`expr 2 + 3\` \* 4`
	* $(((2+3)*4))

* for循环
	* for N in {1..100};do echo$N;done 

* while循环
	* while expression;do command;done

* case语句
	
		case $1 in  
		start)
			echo "starting"
			;;
		stop)
			echo "stoping"
			;;
		*)
			echo "Usage:{start|stop}"
			;;
		esac
* read用于接受输入:read -p 提示语句 -n 字符数 -t 等待时间 参数(用来获取输入的值)
* if判断 

		if condition
		then
			statement
		[elif condition
		then statemennts]
		[else 
		statements]
		fi
* 判断语句:[ condition ],condition中前后有空格,非空返回true 0,反之false 1;如[ 3 -lt 2 ],返回1
	* -it:小于
	* -le:小等于
	* -eq:等于
	* -gt:大于
	* -ge:大等于
	* -ne:不等
	* -f:文件存在并为常规文件 exp: [ -f a.txt ]
	* -s:文件存在且不为空
	* -d:文件存在并为目录
	* -L:文件存在并为连接

* 自定义函数:括号不可以接受参数,调用方法:函数名 参数,同样可用S1,S@获取参数,必须先声明函数,在调用;返回结果通过$?获得,return的数值在0-255;

		[function] 方法名(){
			action;
			[return int];
		}

* 文本处理:sed,awk,cut
	* cut:

			cut -d '分割符' -f fields  可分割特定字符为数段
			cut -c '分割符' 字符区间  可排列信息
	* sort:

			sort -t '分隔符' -k 第几列
	* uniq:去重,和sort要一起用