介绍ActiveMQ
===========================
这章节包括:

    * 高度概括ActiveMQ的特性和使用
    * 下载安装ActiveMQ
    * 了解ActiveMQ的目录结构
    * 运行一个ActiveMQ例子

****

自从1980年就有了企业消息软件，不仅仅是应用之间通信方式，也是一种集成方式。因此消息传递实现需要双端通知及应用之间的相互操作。但是开源的实现最近10年才出现。Apache ActiveMQ就是其中一种开源实现，
提供了应用之间异步通信的能力和松耦合的方式。这章节将介绍ActiveMQ。

ActiveMQ是遵循JMS1.1规范，隶属于Apache基金会的消息中间件。提供高可用，高性能，可扩展，高可靠性，安全的企业级消息中间件。ActiveMQ使用Apache开源许可。因为使用Apache许可任何人都可以使用更改ActiveMQ。
在后续的第2章将介绍中间件在应用之间传递消息和事件，确保消息可靠性。中间件最重要的就是高可用，可靠性，可扩展。

ActiveMQ提供跨语言和平台消息传递。ActiveMQ基于JMS规范，在规范上提供一些新特性。新特性将在本书中介绍和讨论。
第一步就是成功的使用ActiveMQ到你的工作中去。作为一个初学者你可能会畏惧，作为老手他是非常容易理解的。这一章节将告诉你如果简单的使用ActiveMQ，你可能不太理解ActiveMQ的所有特性，但是你可以思考一下在哪里，为什么使用ActiveMQ。
你准备好安装和使用ActiveMQ了吗？

###1.1 ActiveMQ的特性

经过了很多年ActiveMQ提供了很多的特性，本书意在讲解这些特性。以下将是要被讲解的特性：

    * JMS规范-最好的开始点就是JMS规范，因为ActiveMQ就是基于JMS1.1规范实现的。
    JMS提供最重要的功能就是保证同步或者异步消息的可达，点对点的消息传递，持久化订阅及其他许多特性。

    * 连通性-ActiveMQ提供广泛的连接选项，协议支持HTTP/HTTPS,IP多路广播，
    SSL,STOMP,TCP,UDP,XMPP等等。虽然连通性很重要，但是集成到其他容器中去也很重要。
    第4章将介绍传输连接和网络连接。

    * 插件和安全-ActiveMQ提供多种持久化方式。ActiveMQ可以完全自定义安全类型。例如：ActiveMQ提供KahaDB持久化方式，
    也提供标注的JDBC持久化方式。ActiveMQ不但提供基于属性文件的授权方式也提供JAAS的登录模块。这两种方式将在第5章和第6章介绍。

    * 使用JAVA构建消息应用-JAVA应用最通用的消息路由方式是发送和接收消息，使用JMS APIs会在第7章介绍。

    * 应用服务集成-第8章提供一些流行的应用servers的例子，包括：Apache Tomcat，Jetty，Apache Geronimo和JBoss。

    * 客户端APIs-除了JAVA语言ActiveMQ提供很多语言的APIs，包括：  C / C ++, . NET , Perl,  PHP , Python, Ruby等等。
    所以ActiveMQ可以在JAVA世界之外其他地方使用。通过他们的APIs其他语言也能使用ActiveMQ的所有的特性。当然，ActiveMQ的broker
    还是要运行在jvm上，但是客户端可以使用其他被支持的语言。客户端联通ActiveMQ将在第9章介绍。

    * broker集群-为了达到可扩展的目的brokers通过网络连接成集群。broker支持不同的网络拓扑结构，这部分内容将在第10章介绍。

    * 先进的broker特性和客户端选项-ActiveMQ提供对broker和客户端连接broker许多高端的特性。ActiveMQ也提供了XML方式的配置风格。
    这些特性将在第11章和第12章介绍。

    * 简单的管理方式-ActiveMQ是为开发者设计的。同样他不需要一个专门的管理员，因为他提供了非常简单易用，并且功能强大的管理特性。
    提供了许多监控ActiveMQ的方式，包括JMX的工具Jconsole或者ActiveMQ的web管理。也可以使用命令行或者多种类型的日志监控方式。
    将在第14章介绍这部分。

这些只是ActiveMQ提供的功能的初体验，这些内容都将在本书中介绍。第3章将列举一些例子，已经介绍了很多特性，我们确信你关于为什么使用ActiveMQ
有了一些疑问了。

###1.2 为什么，何时使用ActiveMQ

2003年左右，开源开发者们聚集起来。他们发现有使用BSD开源协议的消息代理。他们想要实现一个JMS规范的兼容Java EE的消息代理。商业的消息中间件很多，但是有些公司负担不起
商业消息中间件费用。所以大神们开发了一个开源的消息中间件。就是现在的Apache协议的ActiveMQ。
ActiveMQ使用JMS规范，在分布式系统之间通信。

####1.2.1 松耦合和ActiveMQ

ActiveMQ提供了松耦合的系统架构。松耦合的架构是相对于紧耦合而言的（类似于RPC方式）。松耦设计被认为是异步的，一个应用调用另个应用是没有关系的。没有时间上必须的相互依赖。
应用通过依赖ActiveMQ的消息保证通信。因为这一点，通常说应用发送消息是即发即弃。应用发送消息到ActiveMQ,他们不需要关心消息何时如何到达的。同样消费者应用不需要关心小时
是谁发送，是如何发送到ActiveMQ的。这种方式非常强大的在异构环境中，允许客户端使用不同的语言甚至不同的协议。ActiveMQ作为消息中间件，允许异构系统集成和异步的方式。更多
介绍如下。

当思考分布式应用设计的时候，耦合是非常重要的。耦合是指两个或多个应用系统之间互相依赖。耦合最简单的思考就是改变影响其他应用系统：改变一个应用难道要改变其他所有涉及到的应用？
如果是，那么这些系统就紧紧的耦合到一起了。但是如果一个应用改变不影响其他应用，那么应用之间就是松耦合的。紧耦合是的应用比松耦合的应用是非常难以维护的。另外弱耦合的应用非常容易
处理不可预见的改变。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/coupled.png)

远程过程调用的紧耦合的例子（图1.1）

在第2章介绍的技术( COM ,  CORBA ,  DCE , and  EJB )使用RPC时紧耦合的。当使用RPC一个应用调用另一个应用的时候，调用者会阻塞直到控制权返回到调用者。图1.1描述的就是这个问题。

调用者在图1.1阻塞直到被调用者返回。许多系统使用RPC是成功的，但是那有许多的劣势，比如说紧耦合：高的维护成本，甚至一个小的改变都会波及到整个系统架构。恰当的调速在系统之间是必要的。
两个应用在同一时刻都必须是可用的，当一个应用发出请求(1)，另一个应用处理并返回(2)。这个要求是笨重的，而且系统极易崩溃。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/loosely-coupled-recive.png)

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/loosely-coupled-send.png)

松耦合的例子使用JMS通信（图1.2）

在这个例子中，无论是应用A实例还是应用B实例，一个被移走了不影响另一个。事实上多个ActiveMQ实例应用网络集群配置。这样一些ActiveMQ被一除掉后不会影响应用A或者应用B。这就意味着架构的任意
一部分被移除下来维护都不会影响整体系统的可用性。详细内容会在di10章介绍。
所以ActiveMQ提供了难以置信的灵活性，是弱耦合架构成为可能。如果在实际应用场景中不适合使用异步，ActiveMQ是支持request/reply模式的。那么何时使用ActiveMQ呢？

####1.2.2 何时使用ActiveMQ

在系统架构中ActiveMQ和异步的消息有很多使用场景。这有一些使用场景的例子：

    * 异构的系统集成-ActiveMQ的broker是使用JAVA语言编写，自然JAVA的API客户端是支持的。但是ActiveMQ也支持 C/C++, . NET ,
     Perl,PHP , Python, Ruby等其他语言的客户端。当你考虑到使用不同语言不同平台集成的时候这是巨大的优势。ActiveMQ提供的跨语言，
     帮助应用解耦应用不用去使用RPC了。

    * RPC方式的替代-应用使用RPC同步调用时普遍的。RPC的使用还是比较广泛的比如： ATMs，大多数的Web应用，信用卡系统，
    定点售货系统等等。及时提到的RPC系统是成功的，不用放弃可靠的响应，异步的消息可以带来巨大的好处。
    系统依赖同步请求限制了扩展性，因为请求要有返回因此那会拖慢系统的。相对于RPC的慢，使用异步的消息，添加消费者是非常容易的，
    所以消息可以被及时消费，并且处理更快。当然，要求你的应用是可以被解耦的。

    * 应用之间解耦-我们已经讨论过强耦合的架构有很多问题，尤其是在分布式应用中。松耦合的架构则不一样了。也展示了较小的依赖，
    在遇到不可预见问题的时候是非常好的。不仅将来对一个组件的改变不会波及到整个系统，并且组件之间的交互非常容易。
    使用同步的方式组件之间通信(一个方法调用另一个，调用者等待被调用者响应)，
    组件使用异步通信（只是简单的发送一个消息不用等待响应-即发即弃）这样的松耦合贯穿系统，这就是事件驱动架构。

    * 作为事件驱动的核心-在前面解耦和异步讲解中了解到broker自己是可扩展的，所以可以处理很多客户端的链接。
    增加内存（垂直扩展），增加broker的节点数量处理更多的客户端（水平扩展）。考虑到高交付商业站点例如亚马逊。当用户在亚马逊上购买时，
    需要几个分开的阶：订单配置，发票创建，支付处理，订单实施，运输等等。但是当用户提交一个订单，用户立马看到了一个页面“谢谢你的订单”。
    不仅这样没有延迟，用户还收到了一封邮件订单已被接受。亚马逊的订单处理是一个很好的异步处理的例子。每个阶段处理被分散到阶段服务上。
    当一个用户提交一个订单，同步调用提交订单，全部的订单处理没有后于浏览器同步访问。如果使用异步，问题发生了可以通过邮件通知用户。
    所以异步处理给予了高可用和高扩展性。

    * 改善应用扩展性-许多应用使用事件驱动架构在订单系统中，高的扩展性比如：商业领域，政府，制造业，在线游戏，数不胜数。单一功能设计，
    面向服务架构(SOA)。每个服务提供单一的功能，并且只有这个功能。应用由这些服务组成，使用异步的消息和最终一致在服务之间通信。
    这种方式提出一个新的概念叫做复合事件处理(CEP)。使用CEP在系统中不同组件交互是更深层次的跟踪。
    在系统中多个组件之间添加一层异步消息是可能的。

现在我们已经提供你一些列子在哪里使用ActiveMQ。现在安装ActiveMQ开始使用吧。




###1.3 开始使用AcitveMQ

开始使用ActiveMQ是不难的。你只需要保证broker启动并且可以保证接受链接和发送消息。接下来的一些例子可以帮助你，但是首先你需要安装JAVA和ActiveMQ。
在这一小节，你将下载安装JAVA SE，下载安装AcitveMQ，检查ActiveMQ的目录，启动ActiveMQ。

####1.3.1 下载安装JAVA SE

ActiveMQ最少需要JAVA1.5如果是JAVA1.6更好。必须首先安装JAVA。 如果你没有安装sun的J2SE，你可以使用 Linux, Solaris, 或者 Windows，从
[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
下载并安装。
如果你使用MAC，你可能已经安装了。但是如果没有安装请从这[http://developer.apple.com/java/download/](http://developer.apple.com/java/download/)下载并安装。
一旦你安装上了JAVA SE，你应该测试是否安装正确。打开终端并输入如下命令：

```Bash
[~]$ java version "1.6.0_20"
Java(TM) SE Runtime Environment (build 1.6.0_20-b02-279-10M3065)
Java HotSpot(TM) 64-Bit Server VM (build 16.3-b01-279, mixed mode)
```

你的输出可能有一些细微的差别，因为依赖你使用的操作系统。最重要的是输出了JAVA SE。这条命令告诉我们两个信息，我们正确安装了JAVA SE并且是JAVA 1.6。如果不是你将要修改一下重新安装。

~~Ant安装介绍~~

####1.3.2 下载ActiveMQ

ActiveMQ从这个: [http://activemq.apache.org/download.html](http://activemq.apache.org/download.html)下载
点击5.4.1 release，有两个压缩包，一个是tar包linx，一个是zip windows，解压缩看一眼ActiveMQ目录。

####1.3.3 检查ActiveMQ目录

输入一下命令查看目录文件

```Bash
[apache-activemq-5.4.1]$ ls -1
LICENSE
NOTICE
README.txt
WebConsole-README.txt
activemq-all-5.4.1.jar
bin
conf
data
docs
example
lib
user-guide.html
webapps
```

目录结构相当简单：

    * LICENSE-ActiveMQ所有包使用的Apache开源消息协议。
    * NOTICE-ActiveMQ版权信息。
    * README-用户使用引导文件，包括一些文档及URL。
    * WebConsole- README .txt-包括web管理的一些信息。
    * bin-二进制执行文件及一些命令脚本。
    * conf-包括所有的ActiveMQ的配置信息。
    * data-log文件及消息持久化目录。
    * docs-ActiveMQ Web文档。
    * example-ActiveMQ Web文档。
    * lib-ActiveMQ 需要的类库。
    * user-guide.html-ActiveMQ引导页面。
    * webapps-ActiveMQ web管理页面。

下一步就是启动ActiveMQ并运行一些例子。

####1.3.3 启动ActiveMQ

解压缩完成后，ActiveMQ就可以使用了，启动ActiveMQ。

```Bash
$ ./bin/activemq console
INFO: Using default configuration
(you can configure options in one of these file: /etc/default/activemq
/Users/bsnyder/.activemqrc)
INFO: Invoke the following command to create a configuration file
./bin/activemq setup [ /etc/default/activemq | /Users/bsnyder/.activemqrc ]
INFO: Using java '/System/Library/Frameworks/JavaVM.framework/Home/bin/java'
INFO: Starting in foreground, this is just for debugging purposes
(stop process by pressing CTRL+C)
Java Runtime: Apple Inc. 1.6.0_20
/System/Library/Frameworks/JavaVM.framework/Versions/1.6.0/Home
Heap sizes: current=258880k free=253105k max=258880k
JVM args: -Xms256M -Xmx256M
-Dorg.apache.activemq.UseDedicatedTaskRunner=true
-Djava.util.logging.config.file=logging.properties
-Dcom.sun.management.jmxremote
-Dactivemq.classpath=/Users/bsnyder/amq/apache-activemq-5.4.1/conf;
-Dactivemq.home=/Users/bsnyder/amq/apache-activemq-5.4.1
-Dactivemq.base=/Users/bsnyder/amq/apache-activemq-5.4.1
ACTIVEMQ_HOME: /Users/bsnyder/amq/apache-activemq-5.4.1
ACTIVEMQ_BASE: /Users/bsnyder/amq/apache-activemq-5.4.1
Loading message broker from: xbean:activemq.xml
WARN | destroyApplicationContextOnStop parameter is deprecated,
please use shutdown hooks instead
INFO | PListStore:/Users/bsnyder/amq/apache-activemq-5.4.1/data/localhost/
tmp_storage started INFO | Using Persistence Adapter:
KahaDBPersistenceAdapter[/Users/bsnyder/amq/apache-activemq-5.4.1/data/
kahadb]
INFO | KahaDB is version 2
INFO | Recovering from the journal ...
INFO | Recovery replayed 1 operations from the journal in 0.029 seconds.
INFO | ActiveMQ 5.4.1 JMS Message Broker (localhost) is starting
...
INFO | ActiveMQ Console at http://0.0.0.0:8161/admin
INFO | Initializing Spring root WebApplicationContext
INFO | Connector vm://localhost Started
INFO | Camel Console at http://0.0.0.0:8161/camel
INFO | ActiveMQ Web Demos at http://0.0.0.0:8161/demo
INFO | RESTful file access application at http://0.0.0.0:8161/fileserver
INFO | Started SelectChannelConnector@0.0.0.0:8161
```

NOTE:

    *由于文中所演示的ActiveMQ旧版本和新版本目录结构有差距。
    例子的运行都是基于MAC OS的，如果是使用windows使用如下命令：
    C:\apache-activemq-5.4.1>bin\activemq.bat start

