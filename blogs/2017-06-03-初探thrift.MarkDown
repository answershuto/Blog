## 什么是thrift？

thrift是一个软件框架，用来进行可扩展且跨语言的服务的开发。它结合了功能强大的软件堆栈和代码生成引擎，以构建在 C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and OCaml 这些编程语言间无缝结合的、高效的服务。

[thrift介绍](http://baike.baidu.com/item/thrift?fr=aladdin)

thrift只构建系统中的RPC层。

![img](https://ooo.0o0.ooo/2017/06/06/59368c1e1f17f.png)

---

## 安装thrift

在mac下安装thrift，只需要执行

```bash
    brew install thrift
```

---

## 如何使用

建立一个后缀为.thrift的文件，用于让thrift代码引擎知道你需要生成哪些服务、错误类型以及rpc接口等。
这里我们先创建一个test.thrift，内容如下。

```thrift
/**
 * 错误类型
 */
enum AirExceptionType {
    PASSWD_EXCEPTION = 0;
	
	USER_DIONLINE = 1;
}

/**
 * 调用错误
 */
exception AirException {
    /** 错误类型 */
    1: required AirExceptionType type;

    /** 错误码 */
    2: required i64 errorCode;

    /** 错误消息 */
    3: required string errorMessage;

    /** 调试信息 */
    4: required string debugMessage;
}

/*一个thrift的服务，里面包含一些rpc接口*/
service thriftTest {
    	/**
     * 连接服务器,返回Session
     */
	 string connectServer (
		/** 设备编码(加密) */
        1: string strDevCode;	

    ) throws (
        /** 抛出异常 */
        1: AirException ex;
    );

	/**
     * 与服务器断开连接
     */
	 void disconnectServer (
		/** 用户Session */
        1: string Session;	

    ) throws (
        /** 抛出异常 */
        1: AirException ex;
    );
	
	
	/**
     * 保活
     */
	 void keepAlive (
		/** 用户Session */
        1: string Session;	

    ) throws (
        /** 抛出异常 */
        1: AirException ex;
    );

    /**
     * 输入密码
     */
    void inputPasswd (
        /** 用户Session */
        1: string Session;

		/** 锁类型 */
        2: i32 iLockType;
		
        /** 密码 */
        3: string strPassword;

    ) throws (
        /** 抛出异常 */
        1: AirException ex;
    );
}

```

接着我们执行thrift语句，比如我们需要生成一个cpp的代码。

```bash
    thrift -gen cpp test.thrift
```

这时候我们可以发现，在同级目录下已经出现了一个gen-cpp的文件夹。

```bash
    ranmodeMacBook-Pro:thriftDemo ranmo$ thrift -gen cpp test.thrift
    ranmodeMacBook-Pro:thriftDemo ranmo$ ls
    gen-cpp		test.thrift
```

文件夹里是一些自动生成的.cpp文件以及.h头文件。

```bash
    ranmodeMacBook-Pro:thriftDemo ranmo$ cd gen-cpp/
    ranmodeMacBook-Pro:gen-cpp ranmo$ ls
    test_constants.cpp		test_types.cpp			thriftTest.cpp			thriftTest_server.skeleton.cpp
    test_constants.h		test_types.h			thriftTest.h
    ranmodeMacBook-Pro:gen-cpp ranmo$
```

同样，如果是c#语言，只需要执行

```bash
    thrift -gen csharp test.thrift
```

下面以C#语言为例，来说一下这些文件的作用以及如何使用这些产生的文件。

---

### 产生的文件

可以看到运行命令以后产生了三个.cs文件

```bash
    ranmodeMacBook-Pro:gen-csharp ranmo$ ls
    AirException.cs		AirExceptionType.cs	thriftTest.cs
```

AirException.cs是抛出错误的文件
AirExceptionType.cs是定义错误类型的文件
thriftTest.cs是一个类，里面提供了一个interface Iface用来作为服务端的基类，继承后实现其接口作为RPC接口。class Client是一个客户端类，用来创建客户端调用服务端接口的实例。

---

### 服务端

首先导入thrift相关模块

```c#
    /*Thrift相关*/
    using Thrift.Collections;
    using Thrift.Protocol;
    using Thrift.Server;
    using Thrift.Transport;

    /*线程相关*/
    using System.Threading;

    /*IO相关*/
    using System.IO.Ports;

```

初始化thrift服务端

```c#
    const int THRIFT_MAIN_SERVER_PORT = 7911;//thrift服务端口号   

    TServerSocket serverTransport = new TServerSocket(THRIFT_MAIN_SERVER_PORT, 0, false);
    passwordLock.Processor processor = new passwordLock.Processor(new CThrift());
    //TServer server = new TSimpleServer(processor, serverTransport);
    TThreadedServer server = new TThreadedServer(processor, serverTransport);//使用多线程方法启动thrift服务

    /*thrift服务启动*/
    server.Serve();
```

注意，这边开启thrift进程的时候，传入了一个CThrift的实例，这个实例就是具体的服务端接口实现。将基类中的相关方法继承并且实现，形成RPC接口。

```c#
    /*Thrift实现类*/
        public class CThrift : passwordLock.Iface
        {

            public CThrift()
            {
                //...实现
            }

            //...继承后rpc接口实现
        }
```

---

### 客户端

客户端需要用到thrift文件中的一个client类，用它创建实例来完成接口调用工作。

```c#
    TTransport transport = new TSocket(strIP, iPort);
    TProtocol Potocol = new TBinaryProtocol(transport);
    passwordLock.Client client = new passwordLock.Client(Potocol);
    transport.Open();
```

初始化好客户端段client实例后，就可以直接用这个实例的方法来调用服务端的接口了。

```c#
    client.connectServer("devCode");
```

---

## Demo

这是我写的一个[Demo](https://github.com/answershuto/PasswordLockByThrift)，希望可以给你带来帮助。