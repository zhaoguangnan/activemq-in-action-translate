理解面向消息中间件和JMS的架构
===========================
这章节包括:

    * 企业级消息传递和面向消息中间件的架构。
    * 理解JMS（Java Message Service）服务。
    * 使用JMS APIs发送和接收数据。
    * 介绍一个消息驱动的例子。

****

了解通用企业级消息的背景和历史有助于你理解ActiveMQ背后的思想。ActiveMQ不仅仅是JMS broker。ActiveMQ提供了广泛的联通选项。介绍完企业级消息架构后将会有
一些JMS使用的例子。这章节的目的主要是复习一下企业级消息架构和JMS规范。如果你比较熟悉这部分内容，可以直接跳到下一章节阅读。

许多软件开发者需要在应用之间通信传输数据。有各种解决方案，但是依赖各自的约束和需要，决定如何做有很大的困难。商业系统需要考虑许多影响比如:性能，可扩展，可靠性
等等。每天使用的许多应用需要加强上述问题包括：ATMs，航空预定系统，信用卡系统，代销点系统，通信行业等。哪个是能离开我们日常生活的？
他们提供可靠的安全的服务。在这些服务的背后，就是因为他们是许多应用组成的，使用分布式，通过事件或消息通信。甚至许多复杂的金融交易系统集成方式，都是使用消息传递
实现的。许多productors提供消息传递为了各种各样的目的。需求是发明的母亲，消息中间件就是这么产生的。我们非常需要一种更简单的软件，它易于管理，跨平台通信，
不同的数据格式传输数据，不同的传输协议，甚至跨很多语言。提供复杂的消息路由和传输，这个系统就是所说的消息中间件。

ActiveMQ就是消息中间件，对商业系统提供异步的消息传输。通过实现JMS协议的消息中间件，ActiveMQ促进了应用架构可靠性和扩展性。

###2.1 介绍企业级消息传递

直到今天上面提到的大部分系统都在使用大型主机。所以如何保证这些应用的可靠性呢？让我们探索一下企业级消息传递和这些解决方案背后的历史。

从1960年开始，大型机构在关键应用使用了很多大型主机促进数据处理，财务处理，统计分析等等。大型主机提供了没有不间断的服务包括：高可用，冗余，可靠性，扩展性和许多
商业应用需要的其他关键特性。虽然这些系统非常的强大，但是接入这些系统是受限的，只有一小部分接入选项。系统之间的连通还没有被发明，意味着并行处理还是不可能的。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/terminal-mainframe.png)

终端使用单一协议连接到大型主机(图2.1)

图2.1展示多个终端是如何连接到大型主机的。在1970年，用户开始使用终端访问大型主机，可以有成千上万的人同时使用系统。在这期间计算机网络被发明，连接大型主机变得可能。
到了1980年，不仅图形终端可以使用，而且PCs被发明，终端仿真软件被很快变得常用。互联变得更重要，因为在个人电脑和工作站上开发的应用程序需要访问大型主机。图2.2展示了
各种各样的协议连接到大型主机。注意这样的扩展引入了额外的平台和协议同时也带来的一些问题。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/many-protocols2mainframe.png)

独立的终端使用许多协议连接到大型主机(图2.2)

连接一个开源系统和目标是系统是不容易的，因为他们有各自的数据格式，硬件兼容性，各自不同的适配协议。随着适配列表的增加不同版本的维护变得非常困难。很快维护适配的成本比系统本身
的比重都大(这是企业级消息进入的场景了)。

企业级消息传递的目的就是在异构的系统之间传输数据。这些年有许多的消息传递方式比如：

    * RPC解决方案比如:COM,CORBA,DCE和EJB。
    * 事件通知解决方案,进程间通信，在进入操作系统消息排队，比如：FIFO buffer,message queues,pipes,signals,sockets等等。
    * 消息中间件解决方案提供异步，可靠消息排队。比如：IBM WebSphere MQ,Sonic MQ,TIBCO Rendezvous,Apache Active MQ。
    一般应用于企业级集成的目的。
    
我们将在这讨论最新的消息中间件产品，什么事正确的面相消息的中间件呢？

###2.2 什么是面向消息的中间件呢？

消息中间件(MOM)是异步通信，松耦合，可靠，可扩展最好的诠释在分布式系统中。MOM是非常重要的概念在分布式计算中。应用之间的通信使用的APIs使用各自厂商提供的，并且在分布式系统领域
处理了很多问题。
整体的概述就是MOM在消息发送者和接受者之间扮演一个媒介。这个媒介提供了一个全新的松耦合形式。在2.3中将介绍MOM是如何使用的，消息传递不仅是应用和大型主机之间，也可以在应用和应用之间
传递。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.3.png)

消息中间件是链接大型主机和保证消息可达的媒介(图2.3)

消息是通过MOM在一个应用到另一个应用之间传递的业务信息单元。应用通过MOM发送消息和接收消息使用约定好的destinations。消息发送和交付到接受者通过连接或者订阅的destinations。
通过这种机制允许发送者和接受者之间松耦合，而且没有要求发送者和消费者同时连接到MOM上。发送者不需要知道接受者的任何事情，接受者也不需要知道发送者的任何事情。这就是所说的异步消息
传递。

MOMs对于企业级消息传递的新特性的是开放的，特性包括：消息持久化，在缓慢或者不可靠的链接中的健壮，复杂的消息路由，消息转发等等。消息持久化有助于减缓因为发送者或者接收者造成的
慢的或者不可靠的链接。当消费者down了不会影响发送者。复杂的消息路由包括的特性：发送一条消息到很多的接收者，消息路由基于属性文件或者消息内容等等。消息转换允许两个应用不处理一样
的消息格式，在自定义消息格式(动态转换)。

另外，当前许多的MOMs提供许多链接协议。一些通用的协议支持包括：HTTP/S，多路广播，SSL，TCP/IP，UDP等等。一些供应商甚至提供多语言，降低使用MOMs的成本在各种环境中。ActiveMQ
正好提供了这些特性甚至更多。

此外，ActiveMQ是典型的MOM，提供提供消息发送和接收的API并且相互影响。很多年，MOM的供应商提供他们特有的APIs还有他们选择的语言直到JMS的出现。

###2.3 什么是JMS？

JMS已中立的方式使用JAVA语言提供了发送和接收消息的标准API。JMS API最小化影响企业消息开发（需要开发复杂消息应用的开发者），同时增加了可移植性。

JMS本身并不是一个MOM。JMS是一个在消息客户端和MOMs相互作用的抽象API，类似于JDBC抽象与关系数据库通信的方式。在2.4小节将展示JMS API如何提供的消息客户端和MOM特性之间
的相互作用的高级功能。JMS为创建企业级消息应用提供了低的门槛，也很容移植到其他的消息提供者。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.4.png)

JMS允许一个客户端很容易链接到许多的JMS提供者(图2.4)

最初JMS是和一些在企业消息产业公司创建的，在1998年第一个版本的JMS规范出现。最新版本的规范在2002年制定出来并且提供了一些必要的改变。JMS 1.1 统一了两种APIs(使用在两个消息域的APIs)。
所以需要使用两个消息域现在仅仅需要一个通用的API就可以了。这是令人激动的APIs改进。向后兼容老的APIs。

在标准化API，JMS正式定义了许多概念和从来没有过的消息：

    * JMS client - 使用JAVA语言编写的发送和接收消息的应用。
    * None-JMS client - 使用JMS provider 提供的本地API替代JMS发送和接收的消息。
    * JMS producer - 创建和发送JMS消息的客户端应用。
    * JMS consumer - 接收和处理JMS消息的客户端应用。
    * JMS provider - 使用java语言编写的实现JMS接口。
    * JMS message - JMS最基础的概念，被JMS发送和接收的单元。
    * JMS domains - 两种消息传递方式，点对点和发布订阅。
    * Administered objects - 预置的JMS对象，包括提供客户端使用特定的配置数据。这些对象是典型的侵入模式通过JNDI的方式。
    * Connection factory - 客户端通过使用连接工厂创建到JMS provider之间的链接。
    * Destination - messages接收和发送的地址。

除了上面提及到的概念其他也很重要。接下来的几个章节将深入这些概念，重点介绍构成JMS的部分。

###2.4 JMS规范

正如前面所说的，JMS规范定义了两种客户端类型(JMS客户端和非JMS客户端)。接下来将要简要介绍一下这两种方式的价值。

####2.4.1 JMS client

JMS client利用JMS API和JMS provider相互作用。概念类似于使用JDBC访问关系数据库，JMS client使用JMS API访问消息服务。许多JMS providers(包括ActiveMQ)有许多超远JMS规范的特性。
JMS client 100%使用JMS APIs和避免使用扩展特性是有价值的。但是选择一个特殊的JMS providers经常是因为它提供的扩展功能。如果使用了扩展特性，如果不重构代码很难迁移到另一个JMS provider。

JMS使用MessageProducer和MessageConsumer接口。JMS provider提供这些接口的实现是它的责任。JMS client发送消息叫做productor，JMS client接收消息叫做consumer。JMS client同时提供
消息发送和接收是可能的。

##### JMS productors

JMS clients使用JMS MessageProducer类发送消息到destination。当使用Session.createProducer()方法创建productor的时候，默认的destination就被设置了。但是当通过MessageProducer.send()
发送某个消息的时候可以被覆盖。下面将展示MessageProducer接口。

列表2.1  MessageProducer interface

    * public interface MessageProducer {
              void setDisableMessageID(boolean value) throws JMSException;
              boolean getDisableMessageID() throws JMSException;
              void setDisableMessageTimestamp(boolean value) throws JMSException;
              boolean getDisableMessageTimestamp() throws JMSException;
              void setDeliveryMode(int deliveryMode) throws JMSException;
              int getDeliveryMode() throws JMSException;
              void setPriority(int defaultPriority) throws JMSException;
              int getPriority() throws JMSException;
              void setTimeToLive(long timeToLive) throws JMSException;
              long getTimeToLive() throws JMSException;
              Destination getDestination() throws JMSException;
              void close() throws JMSException;
              void send(Message message) throws JMSException;
              void send(Message message, int deliveryMode, int priority,
              long timeToLive)
              throws JMSException;
              void send(Destination destination, Message message)
              throws JMSException;
              void send(
              Destination destination,
              Message message,
              int deliveryMode,
              int priority,
              long timeToLive)
              throws JMSException;
      }

MessageProducer不仅提供了发送消息的方式也提供了设置各种消息头部（包括：JMSD eliveryMode,JMSPriority,JMSE xpiration）通过get/setTimeToLive()方法。而且可以利用send()
方法可以立刻设置前面提到的三个设置。消息头部将在2.4.5中讨论。

##### JMS consumers

JMS clients使用JMS MessageConsumer消费destination中的消息。MessageConsumer可以使用receive()方法同步消费消息，也可以使用MessageListener实现异步消费消息。当特定的destination
消息到达时候调用MessageListener.onMessage()方法。下面展示MessageConsumer接口。

列表2.1  JMS  MessageConsumer interface

    * public interface MessageConsumer {
              String getMessageSelector() throws JMSException;
              MessageListener getMessageListener() throws JMSException;
              void setMessageListener(MessageListener listener) throws JMSException;
              Message receive() throws JMSException;
              Message receive(long timeout) throws JMSException;
              Message receiveNoWait() throws JMSException;
              void close() throws JMSException;
      }

在MessageConsumer没有方法能设置destination，当使用 Session.createConsumer()创建consumer的时候设置destination。

####2.4.2 Non-JMS clients

正如前面所说的，non-JMS client使用JMS provider本地客户端API替代使用JMS API。native client API可能比JMS client API提供一些不一样的特性，这是他们之间的重要区别。所以non-JMS APIs
CORBA IIOP协议或者其他本地协议而不仅仅是JAVA RMI协议了。消息providers通常会提供native API，但是许多消息providers也会提供 non-JMS client API。

####2.4.3 JMS provider

JMS provider是实现JMS API的具体MOM。所以实现通过标准的JMS API访问MOM(类似于JDBC)。

####2.4.4 JMS message

在JMS规范中JMS message是最重要的概念。每个JMS规范的的建立都是为了处理JMS message，因为它就是解决如何传递业务数据和事件的。JMS message允许任何形式数据作为消息的的一部分被发送，包括文本，二进制，
还有头部信息。在图2.5中介绍JMS message包括两部分headers和payload。headers描述的是JMS clients和JMS providers使用消息的元数据。payload是真实的消息体并且通过各种类型的消息承载文本和二进制数据。
JMS message被设计成容易理解而且灵活的。JMS message中复杂的的内容都在headers中。

####2.4.5 JMS message的内部结构

正如上文提到的，JMS message中复杂的部分都定义在headers中。实际上有两种类型的headers，它们主要的概念相同但是他们的语义不同。JMS规范提供了标准的头部和方法列表。properties基于原始的JAVA类型被设计成容易订制的。

##### JMS MESSAGE HEADERS

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.5.png)

JMS message(图2.5)

正如图2.5中展示的那样，JMS messsages提供了标准头部列表，JMS APIs提供了使用的方法。许多headers信息是自动添加的。接着我们将介绍这些headers和他们是如何被定义的。

当调用client的 send()方法时候头部是自动被设置的:

    * JMSDestination -  destination表示的是消息被发送到哪个地址。这个值是提供给消费消息的clients消费的。
    * JMSDeliveryMode -  JMS支持两种类型的消息交付模式：持久化模式和非持久化模式。持久化是默认的模式。
      每种模式有他自己的优缺点，意味着不同的可靠性级别。

        持久化—如果要保证消息不丢失JMS provider需要开启持久化。JMS provider必须保证持久化消息的交付。
        也就是说，如果JMS provider down了，消息不会丢失并且不会重复投递。持久化消息模式为了持久化存储和可靠性能
        提出了更好的挑战。

        非持久化—如果设置JMS provider非持久化消息。JMS provider必须保证仅有一次非持久化消息的投递。换句话说，
        如果JMS provider down了，消息可能会丢失，但是也不会重复投递的。非持久化消息实现复杂度低而且性能优异。

      在JMS producer设置交付模式并且应用于所有从这个producer中发送的消息。但是个别的消息也可以覆盖交付模式的设置。

    * JMSExpiration - 消息的过期时间。这个头部设置是了防止过期的消息投递。JMS producer可以通过
    MessageProducer.setTimeToLive()方法设置所有发送的消息全局过期时间，或者使用MessageProducer.send()方法
    设置个别被发送的消息过期时间。虽然通过MessageProducer.send()方法设置个别消息的过期时间，但是调用这些方法设置默认
    的时间毫秒数还需要深思。

     JMSExpiration message header是通过增加当前的GMT时间计算的。默认的消息过期时间是零，意味着消息永远都不会过期。
     如果明确指定过期时间为零，消息一样永久不会过期。

     这个头部设置对时间敏感型的消息是非常有价值的。JMS provider不投递过期的消息，JMS client也被实现为不处理过期的消息。

     * JMSMessageID - 由JMS provider分配的唯一标识一个消息的字符串。message ID应用于消息的处理，历史消息的存储。由于
     message IDs导致JMS provider一些问题，所以productor通过MessageProducer.setDisableMessageID()方法设置header
     可以建议JMS provider使的JMS应用忽略这个header。如果JMS provider接受了这个建议，那么message ID必须设置为null，
     JMS provider将忽略这个头部，并且分配一个message ID。
     
     * JMSPriority - 用于分配message的重要级别。这个header也是被producter设置的。一旦设置了message的优先级，将应用
     所有从producter发送出去的消息。也可以设置个别消息的优先级。JMS定义了10种优先级从0到9。
     
        优先级0-4 - 表示普通的优先级。
        优先级5-9 - 表示快速的优先级。
        
     JMS providers不需要实现消息排序（但是大多数还是实现了）。优先级的目的只是先交付高优先级的消息。
     
     * JMSTimestamp - 这个header表示的是从producter发送出去的时间戳。这个头部使用的是标准的JAVA毫秒值。类似于 JMSMessageID
      header，producter可以通过Producer.setDisableMessageTimestamp()建议JMS provider JMSTimestamp header不是必须的。
     如果JMS provider接受了建议JMSTimestamp header必须设置为0。 
     
client 可选的头部选项：
     
     * JMSCorrelationID - 被用作关联当前消息和上一个消息。这个header通常用作关联请求消息和相应消息。JMSCorrelationID的值可以是
     下列任何一个：
        
        一个provider特定的message ID。
        一个特定应用的字符串。 
        一个本地provider byte[]数组值。
     




