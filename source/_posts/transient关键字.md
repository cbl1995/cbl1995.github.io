​		java的serialization提供了一个非常棒的存储对象状态的机制，说白了serialization就是把对象的状态存储到硬盘上去，等需要的时候就可以再把它读出来使用。但是在存储对象状态时，我们有时候会需要特定的对象数据在serialization时不进行存储。这时候transient关键字就派上用场了。要关掉类的特定的数据域，可以使用transient关键字进行定义，这对于底层的java虚拟机来说，这个transient类型的变量不是一个类的永久性的状态。

demo:

```java
import java.util.*;
import java.io.*;

public class LoggingInfo implements java.io.Serializable
{
    private Date loggingDate = new Date();
    private String uid;
    private transient String pwd;

    LoggingInfo(String user, String password)
    {
        uid = user;
        pwd = password;
    }
    public String toString()
    {
        String password=null;
        if(pwd == null)
        {
        password = "NOT SET";
        }
        else
        {
            password = pwd;
        }
        return "logon info: /n   " + "user: " + uid +
            "/n   logging date : " + loggingDate.toString() +
            "/n   password: " + password;
    }

  public static void main(String args[]){
LoggingInfo logInfo = new LoggingInfo("MIKE", "MECHANICS");
System.out.println(logInfo.toString());
try
{
   ObjectOutputStream o = new ObjectOutputStream(
                new FileOutputStream("logInfo.out"));
   o.writeObject(logInfo);
   o.close();
}
catch(Exception e) {//deal with exception}
  System.out.println("hello world !");
  }

  try
{
   ObjectInputStream in =new ObjectInputStream(
                new FileInputStream("logInfo.out"));
   LoggingInfo logInfo1 = (LoggingInfo)in.readObject();
   System.out.println(logInfo1.toString());
}
catch(Exception e) {
//deal with exception
}

}
}

```

  生成了logInfo.out文件，是用来serialization进行存放类对象数据的，进行存储后又对这个文件进行读，读的内容如下为第二个logon info后面的内容。
    输出结果如下：
logon info: 
   user: MIKE
   logging date : Mon Oct 30 21:39:58 CST 2006
   password: MECHANICS
logon info: 
   user: MIKE
   logging date : Mon Oct 30 21:39:58 CST 2006
   password: NOT SET

​    我们可以看到读进去的password为MECHANICS，但是读出来的却是NOT SET，因为在serialization时，没有存储到硬盘上，因为pwd被定义为transient类型的。

​    transient关键字也会产生副作用，见如下代码：  

```java
public class GuestLoggingInfo implements java.io.Serializable 
{ 
    private Date loggingDate = new Date(); 
    private String uid; 
    private transient String pwd; 
    
    GuestLoggingInfo() 
    { 
        uid = "guest"; 
        pwd = "guest"; 
    } 
    public String toString() 
    { 
        //same as above 
     } 
} 

```

读出来的pwd还是NOT SET，也就是默认的初始化是没有作用的。
来源： https://blog.csdn.net/scruffybear/article/details/1914586