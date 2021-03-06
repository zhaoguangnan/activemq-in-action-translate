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

     * JMSMessageID - 

     * JMSReplyTo - 被用作指定一个发送的destination。这个header通常被request/reply模式的消息传递。设置这个header期望一个相应，
     但这不是必须的。client决定响应或者不响应。
     
     * JMSType - 被用作标记message的类型。这个header被供应商使用，对于JAVA message承载数据是没有意义的。
     
provider 设置的可选头部选项：
     
     * JMSRedelivered - 表示之前可能交付一个消息，但是没有ack。这种情况是可能发生的比如：consumer没有确认交付或者由于provider
     抛出了异常导致provider拒绝接受了ack。

##### JMS MESSAGE PROPERTIES

properties可以被简单的添加到一个消息的header中。JMS provides可以通过通用的方法自定义headers。方法对headers的值提供了许多JAVA原始类型
包括：boolen,byte,short,int,long,float,double,String,Object类型。下列展示的Message接口的一些例子:

列表2.3  JMS Message interface

    * public interface Message {
      ...
      boolean getBooleanProperty(String name) throws JMSException;
      byte getByteProperty(String name) throws JMSException;
      short getShortProperty(String name) throws JMSException;
      int getIntProperty(String name) throws JMSException;
      long getLongProperty(String name) throws JMSException;
      float getFloatProperty(String name) throws JMSException;
      double getDoubleProperty(String name) throws JMSException;
      String getStringProperty(String name) throws JMSException;
      Object getObjectProperty(String name) throws JMSException;
      ...
      Enumeration getPropertyNames() throws JMSException;
      boolean propertyExists(String name) throws JMSException;
      ...
      void setBooleanProperty(String name, boolean value) throws JMSException;
      void setByteProperty(String name, byte value) throws JMSException;
      void setShortProperty(String name, short value) throws JMSException;
      void setIntProperty(String name, int value) throws JMSException;
      void setLongProperty(String name, long value) throws JMSException;
      void setFloatProperty(String name, float value) throws JMSException;
      void setDoubleProperty(String name, double value) throws JMSException;
      void setStringProperty(String name, String value) throws JMSException;
      void setObjectProperty(String name, Object value) throws JMSException;
      .. }

getPropertyNames()和propertyExists()方法是两个常用操作properties的方法。getPropertyNames()方法通过迭代获取properties Enumeration。
propertyExists()方法可以测试给定的message是否存在指定的property。注意特定的JMS headers包含不通用的properties，不能通过getPropertyNames()
方法获取到。
有三种类型的properties：自定义properties，JMS定义的properties，特定的provider properties。

##### CUSTOM PROPERTIES(自定义properties)

自定义properties由于JMS应用随意定义的。JMS应用的开发者可以自由定义任何JAVA类型的属性，通过通用的方法（上一小节中展示的那些方法：getBooleanProperty()/
setBooleanProperty() ,  getStringProperty()/setStringProperty()等等）。

##### JMS-DEFINED PROPERTIES(JMS定义的properties)

JMS规范对于JMS-defined properties包含了JMSX属性名前缀，并且支持如下属性选项：

    * JMSXAppID - 标识应用发送的message。
    * JMSXConsumerTXID - 表示被消费需要事物message的事物标识。
    * JMSXDeliveryCount - 表示message交付次数。
    * JMSXGroupID - 表示message所属于的分组。
    * JMSXGroupSeq - 表示一个组内的顺序编号。
    * JMSXProducerTXID - 表示事物消息被生产出来的事物标识。
    * JMSXRcvTimestamp  - 表示JMS provider交付消息给consumer的时间。
    * JMSXState  - 被用作定义指定provider的状态。
    * JMSXUserID  - 标识发送消息的用户。

JMS规范对这些properties唯一的建议就是JMSXGroupID和JMSXGroupSeq，当组内消息或者组内消息排序的时候在客户端这两个属性必须被使用。

##### PROVIDER-SPECIFIC PROPERTIES(provider特有的properties)

JMS规范涵盖了对于具体provider properties特有的属性名前缀(JMS_<vendor-name>)。每个provider定义自己的名字替换<vendor-name>。
最典型的使用是在具体的non-JMS clients中，并且不应该使用在JMS-to-JMS的消息传递中。

现在jms headrs和properties如何准确的使用它们已经介绍结束了。headers和properties在client 订阅一个destination过滤接收到的消息的时候是非常重要的。

####2.4.6 Message selectors

当client订阅一个destination成倍的消息，但是可以按message类型过滤收到的消息(这就是headers和properties使用的地方)。比如：一个consumer注册从一个queue中获取
消息仅仅是关心特定的股票代码，每个消息都包含一个属性标识感兴趣的股票代码是非常容易的。JMS client可以利用JMS message selectors告诉JMS provider它只接收包含指定propert值
的消息。

Message selectors允许JMS client通过基于message headers从destination接收它想要的接收的messages。selectors是SQL92表达式的子集。使用boolen表达式，
message selectors使用message headers和properties作为简单boolen比较的标准。messages不匹配这些表达式就不能被交付到client。Message selectors不能
用message payload作为参照，只能使用headers和properties。

selectors对已经通过的selectors使用条件表达式作为字符串参数使用javax.jms.Session对象创建的方法。直接从SQL92解析这些表达式使用各种标示符，文字和运算符。表格2.1
中定义：

表格2.1 JMS selector syntax

| Item | Values          |
| ------------- | ----------- |
| Literals      | boolen值 true/false;数字比如：5,-10,+34;小数或者科学计数比如： 43.3E7, +10.5239|
| Identifiers     | 一个header或者property的字段     |
| Operators     | 比较运算符  AND, OR, LIKE, BETWEEN, =, <>, <, >, <=, =>, +, -, *, /, IS NULL, IS NOT NULL    |

表格2.1展示针对message headers和properties创建queries。思考下列定义的message。在接下来的例子中定义了两个将要被使用作为message过滤的properties。


列表2.4 自定义JMS message

    * public void sendStockMessage(Session session,
          MessageProducer producer,
          Destination destination,
          String payload,
          String symbol,
          double price)
          throws JMSException {
          TextMessage textMessage = session.createTextMessage();
          textMessage.setText(payload);
          textMessage.setStringProperty("SYMBOL", symbol);
          textMessage.setDoubleProperty("PRICE", price);
          producer.send(destination, textMessage);
      }

现在让我们看看使用上面的message通过message selectors过滤message的例子。

列表2.5 使用SYMBOL header过滤message

    * ...
      String selector = "SYMBOL = 'AAPL'";
      MessageConsumer consumer =
      session.createConsumer(destination, selector);
      ...

列表2.5定义一个selector苹果公司的message。这个consumer只接收匹配定义到selector的message。

列表2.6 使用SYMBOL和PRICE header过滤message

    * ...
      String selector = "SYMBOL = 'AAPL' AND PRICE > "
      + getPreviousPrice();
      MessageConsumer consumer =
      session.createConsumer(destination, selector);
      ...

上面是一个只匹配苹果公司message的selector。谁的价格比上一个价格要大。这个selector将展示股票信息中谁的价格在变大。除了股票和代码你想要知道什么股票的及时性信息？
下面的例子是你需要关心的。

列表2.7 使用headers过滤messages

    * ...
      String selector = "SYMBOL IN ('AAPL', 'CSCO') AND PRICE > "
      + getPreviousPrice() + " AND PE_RATIO < "
      + getCurrentAcceptedPriceToEarningsRatioThreshold();
      MessageConsumer consumer =
      session.createConsumer(destination, selector);
      ...

在2.7列表中定义了更加复杂的selector匹配苹果公司和思科公司的message(哪家的股票在涨和哪家收益率小于公认的阀值)。
这些例子已经足够你使用message selectors的了。但是如果你想深入可以参考JMS message的Javadoc。

##### MESSAGE BODY

JMS定义了六种类型的message body也就是指payload。通过使用这些objects，数据和信息可以通过messsage payload发送出去。

    * Message - 最基础的消息类型。被使用在没有payload，只有headers和properties的消息中。典型的使用就是事件通知。
    * TextMessage - message payload是字符串。通常使用其发送文本和xml数据。
    * MapMessage - 使用name/value集合作为payload。names是String类型的，values是JAVA原始类型。
    * BytesMessage - 包含一个bytes数组作为payload。
    * StreamMessage - message中包含JAVA类型的stream可以连续的填充和读取。
    * ObjectMessage - 使用序列化的JAVA Object作为payload。通常对于复杂的java objects使用，也支持java collections类。

####2.4.7 JMS domains

之前提到过，JMS的创建是组织的努力，group包含消息实现的厂商。由于现存的实现导致JMS定义了两种类型的消息传递方式point-to-point和publish/subscribe。大多数的MOMs已经实现了这两种消息
传递方式。所以很自然JMS API也支持两种方式。让我们讨论一下两种消息传递的方式以便于让我们更好的理解他们。

##### THE POINT-TO-POINT DOMAIN

point-to-point(PTP)使用的destinations就是我们说的queues。message使用queues通过同步或者异步的方式接收和发送。每一个从queue中message只能被交付给一个consumer一次。
这种方式类似于person-to-person email的方式。consumers接收消息通过MessageConsumer.receive()方法同步接收消息或者通过MessageConsumer.setMessage.Listener()
方法注册一个MessageListener实现异步接收message。queue将存储所有消息直到message被交付或者过期。

图2.6展示了许多consumers可以注册到同一个queue上，但是只有一个consumer可以收到这个消息然后consumer将会确认收到这个消息。注意图2.6展示了从一个productor发送消息交付给了
一个consumer而不是所有的consumers。就像前面提到的，JMS provider确保消息交付一次而且仅此一次到可用的注册在这个queue上的consumer。在这点上，JMS provider以循环的方式
访问所有的注册在queue上的consumers分配messsage。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.6.png)

点对点的消息传递模式(图2.6)

##### THE PUBLISH/SUBSCRIBE DOMAIN

publish/subscribe (pub/sub)消息传递方式就是所说的topics。发布者发送message到topic，订阅者从topic注册并接收message。任何发送到topic中的消息会被自动交付到所有的订阅者中。
这种消息传递模式就像是订阅邮件列表一样在one-to-many模式中，所有的消息将发送给所有邮件列表中的订阅者。在图2.7中描述的是pub/sub模式。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.7.png)

发布/订阅使用one-to-many消息传递模式(图2.7)

大致像上一节中提及到的PTP模式，订阅者在topic注册通过使用MessageConsumer.receive()同步接收message，或者注册一个MessageListener的实现使用MessageConsumer.setMessageListener()
方法异步的接收消息。topics不会保存消息除非明确指定要保存，但是通过durable subscription(持久化订阅)可以做到这点。当一个订阅者从JMS provider中断开时候使用持久化订阅。为订阅者提供消息持久化
是provider的责任。当重新接连后，订阅者将从JMS provider中收到所有没有过期的消息。持久化订阅允许订阅者断开链接但是不会丢失任何消息。

##### DISTINGUISHING MESSAGE DURABILITY FROM MESSAGE PERSISTENCE

message durability和message persistence这两个概念经常混淆。虽然他们很相似，但是他们在语意上有各自的目的。Message durability只能用在pub/sub方式中。当client链接到一个topic时，
可以使用持久化订阅或者非持久化订阅。思考两者之间的区别：

    * Durable subscription - 持久化订阅意味着空间是极大的。订阅topic就是告诉JMS provider当subscriber断连的时候保存订阅状态。
    JMS provider将存储所有的message直至subscriber重新链接或者subscriber明确指定取消了对这个topic的订阅。
    * Nondurable subscription - 非持久化订阅空间吃有限的。阅topic就是告诉JMS provider当subscriber断连的时候不保存订阅状态。
    JMS provider在subscriber断开链接的期间将不保存任何消息。

Message persistence是独立的message domain。Message persistence是服务质量属性，表明在JMS provider failure的时候处理丢失消息的能力。正如前面讨论的，这个服务质量是在message
producter的setDeliveryMode(JMSDeliveryMode class的PERSISTENT和NON-PERSISTENT properties作为参数)指定的。

##### 在JMS应用中Request/reply消息传递

虽然JMS规范没有定义request/reply messaging当做明确的messaging domain，但是它提供了message header和一组便利的class处理基于request/reply消息传递。Request/reply消息传递
是往返的异步回话模式(通过JMSReplyTo，JMSCorrelationID message header和临时destinations，利用PTP domain或者pub/sub domain实现)。JMSReplyTo指定reply被发送到哪个destination，
JMSCorrelationID回复指定JMSMessageID request message。这些headers连接repaly messages和request messages。临时destinations有长链接创建，并且只能被这个链接消费。由于这些限制
使得临时destinations对request/reply模式非常有用。

QueueRequestor和TopicRequestor这两个类方便处理基于request/reply模式的消息传递。这些classess提供了request()方法发送request message并且通过创建的temporary destination(只回应一个request)
等待一个replay message。如图2.8只有一个replay回应一个request。这些类对于最基本的request/reply形式的消息传递是非常有用的。

![](https://github.com/zhaoguangnan/activemq-in-action-translate/blob/master/images/2-F2.8.png)

基本的request/reply消息传递模式(图2.8)

图2.8描述了两点之间基本的request/reply消息传递模式。通常使用JMSReplyTo message header和temporary queue(临时queue就是由receiver发送响应消息，由requestor消费)就可以实现。前面讲述过QueueRequestor
和TopicRequestor可以处理基本的request/reply，但是不支持更复杂的request/reply cases。比如：一个请求从多个recirves返回多个响应。复杂的应用场景需要自定义开发JMS应用。

####2.4.8 Administered objects

Administered objects包含具体的JMS provider配置信息并且假设是由JMS administrator创建的。Administered objects由JMS clients创建。它们屏蔽了特定JMS provider详细信息并且抽象了JMS provider管理工作。
通常通过JNDI查找受管理的对象，但是这不是必须的。当JMS provider存在于JAVAEE容器中的时候这种方式是最通常的用法。JMS规范定义了两种受管理的对象:ConnectionFactory和Destination。

##### CONNECTIONFACTORY(链接工厂)

JMS clients使用ConnectionFactory对象创建到JMS provider的链接。链接工厂代表在client和provider中间打开一个TCP socket，所以链接成本是巨大的。如果可能实现一个连接池是一个很好的注意。链接到JMS provider类似于
JDBC链接到数据库。JMS clients创建javax.jms.Session objects，使用JMS connections链接到JMS provider。

##### DESTINATION

Destination对象封装具体JMS provider的地址(消息被发送到的和从中消费的地址)。destinations是由Session object创建的，他们生命周期的开始于session的创建。
Temporary destinations由独立的链接创建。它们的生命周期和创建他们的链接一样长，只有创建他们的链接才能创建消费者消费他们。正如上面提到的，temporary destinations通常用在request/reply请求响应模式的message传递中。

###2.5 使用JMS APIs创建JMS应用

JMS应用可以非常简单，也可以适合于商业应用的复杂需求。类似于JDBC,JNDI,EJBs等APIs，JMS APIs抽象了通用的使用，所以商业代码中不会混杂JMS的代码。这个模式就不在这演示了，因为演示会篇幅很大。
下面有一些简单的例子演示最基础的JMS APIs最简单的使用。

####2.5.1 一个简单的JMS应用

这个JMS应用使用JAVA语言编写，包含了使用JMS的多个方面。下面的部分我们将复习一下在2.3小节中讲解的JMS构件。一个简单的JMS应用将利用以下步骤构建：

    * 获取JMS connection factory。
    * 使用connection factory创建一个链接。
    * 开启JMS链接。
    * 从链接中创建一个JMS session。
    * 获取一个JMS destination。
    * 创建一个JMS producer并且创建一个JMS mesasge发送到destination。
    * 创建一个JMS consumer并且注册一个JMS message listener。
    * 发送和接收JMS message。
    * 关闭所有JMS resources(connection, session, producer, consumer等)。

这些抽象出来的步骤展示了JMS简单的使用步骤。下面的列表展示了创建一个JMS producer并且发送messsage。

列表2.8 发送JMS message

    * public class MyMessageProducer {
          ...
          ConnectionFactory connectionFactory;
          Connection connection;
          Session session;
          Destination destination;
          MessageProducer producer;
          Message message;
          boolean useTransaction = false;
          try {
              Context ctx = new InitialContext();
              connectionFactory =
              (ConnectionFactory) ctx.lookup("ConnectionFactoryName");
              connection = connectionFactory.createConnection();
              connection.start();
              session = connection.createSession(useTransaction,
              Session.AUTO_ACKNOWLEDGE);
              destination = session.createQueue("TEST.QUEUE");
              producer = session.createProducer(destination);
              message = session.createTextMessage("this is a test");
              producer.send(message);
          } catch (JMSException jmsEx) {
          ...
          } finally {
              producer.close();
              session.close();
              connection.close();
          }
      }
