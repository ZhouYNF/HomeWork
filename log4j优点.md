
## log4j
Log4j是Apache的一个开放源代码项目，通过使用Log4j，我们可以控制日志信息输送地；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

---

### 1. log4j的好处
>首先是简单，在log4j的安装，使用都是很便捷的，只需要一个架包便可以解决。
>
>其次在精确控制上可以通过写配置文件来控制日志输出的级别。在系统开发阶段可以打印详细的log信息以跟踪系统运行情况,而在系统稳定后可以关闭log输出,从而在能跟踪系统运行情况的同时,又减少了垃圾代码(System.out.println(......)等)。
>
>log4j非常灵活强大，我们可以控制它输出的位置，可以在控制台，也可以在数据库，还可以生成一个日志文件来保存，方便查找。
>
>在可扩展性上也非常出色，我们可以通过面向接口编程继承log4j的接口来定义自己的输出模式。

### 2. 配置文件

>Log4j由三个重要的组件构成：日志信息的优先级，日志信息的输出目的地，日志信息的输出格式。日志信息的优先级从高到低有FATAL、ERROR、WARN、INFO、DEBUG、TRACE、ALL，分别用来指定这条日志信息的重要程度；日志信息的输出目的地指定了日志将打印到控制台还是文件中；而输出格式则控制了日志信息的显示内容。

##### 2.1 日志信息的优先级Log4j建议只使用四个级别，优先级从高到低分别是ERROR、WARN、INFO、DEBUG。通过在这里定义的级别，您可以控制到应用程序中相应级别的日志信息的开关。如在这里定义了INFO级别，则应用程序中所有低于INFO级别的日志信息将不被打印出来。


##### 2.2 输出源的使用有选择的能用或者禁用日志请求仅仅是Log4j的一部分功能。Log4j允许日志请求被输出到多个输出源。用Log4j的话说，一个输出源被称做一个Appender。

>Appender包括console（控制台）, files（文件）, GUI components（图形的组件）, remote socket servers（socket 服务）, JMS（java信息服务）, NT Event Loggers（NT的事件日志）, remote UNIX Syslog daemons（远程UNIX的后台日志服务）。它也可以做到异步记录。 一个logger可以设置超过一个的appender。 用addAppender方法添加一个appender到一个给定的logger。对于一个给定的logger它每个生效的日志请求都被转发到该logger所有的appender上和该logger的父辈
 logger的appender上。

##### 2.2.1 ConsoleAppender如果使用ConsoleAppender，那么log信息将写到Console。效果等同于直接把信息打印到System.out上了。

##### 2.2.2 FileAppender使用FileAppender，那么log信息将写到指定的文件中。这应该是比较经常使用到的情况。 相应地，在配置文件中应该指定log输出的文件名。如下配置指定了log文件名为log.txt。

>log4j.appender.appendername.File=log.txt 注意将appendername替换为具体配置中Appender的别名。

>注意：指定的log文件路径问题

##### 2.2.3 DailyRollingAppender使用FileAppender可以将log信息输出到文件中，但是如果文件太大了读起来就不方便了。这时就可以使用DailyRollingAppender。DailyRollingAppender可以把Log信息输出到按照日期来区分的文件中。配置文件就会每天（时间可以设定）产生一个log文件，每个log文件只记录当天的log信息：

	log4j.appender.appendername=org.apache.log4j.DailyRollingFileAppender  l
	og4j.appender.Aappendername.file=log  
	log4j.appender.appendername.DatePattern='.'yyyy-MM-dd  
	log4j.appender.appendername.layout=org.apache.log4j.PatternLayout  
	log4j.appender.appendername.layout.ConversionPattern= %5r %-5p %c{2} - %m%n 



##### 2.2.4 RollingFileAppender

>文件大小到达指定尺寸的时候产生一个新的文件。

	og4j.appender.appendername=org.apache.log4j.RollingFileAppender  
	log4j.appender.appendername.File= ../logs/rlog.log  
	# Control the maximum log file size  
	log4j.appender.appendername.MaxFileSize=100KB  
	# Archive log files (one backup file here)  
	log4j.appender.appendername.MaxBackupIndex=1

	log4j.appender.appendername.layout=org.apache.log4j.PatternLayout  
	log4j.appender.appendername.layout.ConversionPattern=%p %t %c - %m%n 

>这个配置文件指定了输出源appendername，是一个轮转日志文件。最大的文件是100KB，当一个日志文件达到最大尺寸时，Log4J会自动把rlog.log重命名为rlog.log.1，然后重建一个新的rlog.log文件，依次轮转。2.2.5 WriterAppender将日志信息以流格式发送到任意指定的地方。



##### 2.3 Layout的配置Layout指定了log信息输出的样式。

##### 2.3.1 布局样式

	org.apache.log4j.HTMLLayout（以HTML表格形式布局），  
	org.apache.log4j.PatternLayout（可以灵活地指定布局模式），  
	org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），  
	org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）

	%m 输出代码中指定的消息  
	%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL  
	%r 输出自应用启动到输出该log信息耗费的毫秒数  
	%c 输出所属的类目，通常就是所在类的全名  
	%t 输出产生该日志事件的线程名  
	%n 输出一个回车换行符，Windows平台为"rn"，Unix平台为"n" 
	%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2018年10月18日 22：10：28，921  
	%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(Test Log4.java:10) 



### 3.为不同的Appender设置日志输出级别通过在配置中修改Appender的Threshold即能实现，比如下面的例子：



配置文件：log4j.rootLogger = debug,A,B,C

##### 输出到控制台
	log4j.appender.A = org.apache.log4j.ConsoleAppender
	log4j.appender.A.Target = System.out
	log4j.appender.A.layout = org.apache.log4j.PatternLayout
	log4j.appender.A.layout.ConversionPattern = %p %t %c - %m%n

##### 输出到日志文件
	log4j.appender.B = org.apache.log4j.DailyRollingFileAppender
	log4j.appender.B.File = logs/log.log
	log4j.appender.B.Append = true
	log4j.appender.B.Threshold = DEBUG # 输出EBUG级别以上的日志
	log4j.appender.B.layout = org.apache.log4j.PatternLayout
	log4j.appender.B.layout.ConversionPattern = %p %t %c - %m%n

#### 保存异常信息到单独文件
	log4j.appender.C = org.apache.log4j.DailyRollingFileAppender
	log4j.appender.C.File = logs/error.log # 异常日志文件名
	log4j.appender.C.Append = true
	log4j.appender.C.Threshold = ERROR #只输出ERROR级别以上的日志
	log4j.appender.C.layout = org.apache.log4j.PatternLayout
	log4j.appender.C.layout.ConversionPattern = %p %t %c - %m%n


	public class TestLog4j{  
		public static void main(String[] args){    
	    		PropertyConfigurator.configure("D:/Code/conf/log4j.properties");     
	   		Logger logger = Logger.getLogger(TestLog4j.class);   
	    		logger.debug("debug");  
			logger.error("error"); 
		}
	}

