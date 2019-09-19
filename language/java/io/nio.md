# NIO
## 引言
Java NIO(New IO)是一个可以替代标准Java IO API的IO API（从Java 1.4开始)，Java NIO提供了与标准IO不同的IO工作方式。

### Java NIO: Channels and Buffers（通道和缓冲区）
标准的IO基于字节流和字符流进行操作的，而NIO是基于通道（Channel）和缓冲区（Buffer）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。

### Java NIO: Non-blocking IO（非阻塞IO）
Java NIO可以让你非阻塞的使用IO，例如：当线程从通道读取数据到缓冲区时，线程还是可以进行其他事情。当数据被写入到缓冲区时，线程可以继续处理它。从缓冲区写入通道也类似。

### Java NIO: Selectors（选择器）
Java NIO引入了选择器的概念，选择器用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个的线程可以监听多个数据通道。

## 概述
Java NIO包含以下核心组件：
- Channels通道
- Buffers缓冲区
- Selectors选择器
事实上，Java NIO不仅仅包含了以上的类和组件，但是Channel, Buffer 和 Selector构成了API的核心。其余的组件，如Pipe和FileLock，仅仅是与三个核心组件一起使用的实用工具类。因此，我猛将在本NIO概述中重点介绍这三个组件。在本教程的其他地方，其他组件将在它们自己的文本中解释。

### 概述——通道和缓冲区
通常，NIO中的所有IO都开始于通道。通道有点像流。数据可以从通道读取到缓冲区，也可以从缓冲区写入通道。以下是这方面的一个例子：

![avatar](./images/nio-overview-channels-buffers.png)

Java中有几种通道和缓冲区类型。以下是JavaNIO中主要通道的实现：
- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel
  
如您所见，这些通道包括UDP+TCP网络IO和文件IO。

还有一些有趣的接口，但为了简单起见，我将它们排除在JavaNIO概述之外。但将在本JavaNIO教程的其他文本中解释它们。

以下是JavaNIO中缓冲区的核心的实现：
- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer

这些缓冲区涵盖了您可以通过IO发送的基本数据类型：byte, short, int, long, float, double 以及 characters。

JavaNIO还有一个MempdByteBuffer，它与内存映射文件一起使用。不过，我将把这个缓冲区类排除在这个概述之外。

### 概述——选择器
Selector允许一个线程处理多个通道。如果应用程序打开了多个连接(通道)，但是每个连接的流量都很低，这是非常方便的。例如，在聊天服务器中。

下面是一个使用Selector处理3个通道的线程的示例：

![avatar](./images/nio-overview-selectors.png)

要使用选择器，请在通道上注册选择器。然后调用它的select()方法。此方法将被阻塞，直到触发已注册的通道已经ready的事件。一旦方法返回，线程就可以处理这些事件。常见的事件有连接进入、接收到数据等。

## Java NIO Channel
Java NIO Channels跟流stream非常类似，但有以下不同：
- 你可以读写一个通道。流通常是单向的(读或写)。
- 通道可以异步读写。
- 通道总是读取或写入缓冲区。

如前所述，您将数据从通道读取到缓冲区，并将数据从缓冲区写入通道。以下是这方面的一个例子：

![avatar](./images/nio-overview-channels-buffers.png)

### 通道的实现
以下是JavaNIO中最主要的通道实现：
- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

FileChannel从文件读数据或往文件写数据。

DatagramChannel 可以通过UDP在网络上读写数据。

SocketChannel可以通过TCP通过网络读写数据。

ServerSocketChannel允许您侦听传入的TCP连接，就像Web服务器一样。对于每个进入的连接，将创建一个SocketChannel。

### 例子
下面是一个使用FileChannel将一些数据读入缓冲区的基础示例：
```
RandomAccessFile aFile = new RandomAccessFile("data/nio-data.txt", "rw");
FileChannel inChannel = aFile.getChannel();

ByteBuffer buf = ByteBuffer.allocate(48);

int bytesRead = inChannel.read(buf);
while (bytesRead != -1) {

    System.out.println("Read " + bytesRead);
    buf.flip();

    while(buf.hasRemaining()){
        System.out.print((char) buf.get());
    }

    buf.clear();
    bytesRead = inChannel.read(buf);
}
aFile.close();
```
注意buf.flip()调用。首先，您将数据读入缓冲区。然后你把缓冲区翻过来。然后你从缓冲区读出数据来。

## Java NIO Buffer
在与NIO通道交互时需要使用Java NIO缓冲区。数据从通道读取到缓冲区，从缓冲区写入通道。缓冲区本质上是一个内存块，您可以将数据写入其中，然后再读取数据。这个内存块被包装在一个NIO缓冲区对象中，它提供了一组方法，使得使用内存块变得更加容易。

### 缓冲区基本用法
使用缓冲区读取和写入数据通常遵循以下四个步骤：
1. 将数据写入缓冲区
2. 调用buffer.flip()
3. 从缓冲区读取数据
4. 调用buffer.clear() 或者 buffer.compact()

当您将数据写入缓冲区时，缓冲区将跟踪您写入的数据的size。一旦需要读取数据，就需要使用flip()方法调用将缓冲区从写入模式转换为读取模式。在读取模式中，缓冲区允许读取先前写入缓冲区的所有数据。

一旦读取了所有数据，就需要清除缓冲区，以便为再次写入做好准备。您可以通过两种方式来做到这一点：调用clear()或调用compact()。clear()方法清除整个缓冲区。compact()方法只清除您已经读取的数据。任何未读取的数据都会移动到缓冲区的开头，数据将在未读数据之后写入缓冲区。

下面是一个简单的缓冲区使用示例：
```
RandomAccessFile aFile = new RandomAccessFile("data/nio-data.txt", "rw");
FileChannel inChannel = aFile.getChannel();

//create buffer with capacity of 48 bytes
ByteBuffer buf = ByteBuffer.allocate(48);

int bytesRead = inChannel.read(buf); //read into buffer.
while (bytesRead != -1) {

  buf.flip();  //make buffer ready for read

  while(buf.hasRemaining()){
      System.out.print((char) buf.get()); // read 1 byte at a time
  }

  buf.clear(); //make buffer ready for writing
  bytesRead = inChannel.read(buf);
}
aFile.close();
```

### 缓冲区 Capacity, Position and Limit
缓冲区本质上是一个内存块，您可以将数据写入其中，然后再读取数据。这个内存块被包装在一个NIO缓冲区对象中，它提供了一组方法，使得使用内存块变得更加容易。

缓冲区具有需要熟悉的三个属性，以便了解缓冲区是如何工作的。它们是：
- capacity 容量
- position 位置
- limit 界限

position和limit的含义取决于缓冲区是在读模式还是写模式。无论缓冲区模式如何，capacity总是意味着相同的。

这里是一个写和读模式下capacity，position和limit的说明。

![avatar](./images/nio-buffers-modes.png)

#### Capacity
作为内存块，缓冲区具有一定的固定大小，也称为“Capacity”。只能将Capacity大小的字节、长字节、字符等写入缓冲区。一旦缓冲区满了，您需要清空它(读取数据，或清除它)，然后才能将更多的数据写入缓冲区。

#### Position
当您将数据写入缓冲区时，实际上这个行为是发生在某个position。最初的position是0。当char、Long等被写入缓冲区时，position将被提前指向缓冲区中的下一个单元以插入数据。position最大值为capacity-1。

当您从缓冲区读取数据时，您也会从给定的position读取数据。将缓冲区从写入模式翻转到读取模式时，position将重置为0。当您从缓冲区读取数据时，您将从position处读取数据，并将position提升到要读取的下一个位置。

#### Limit
在写模式中，缓冲区的Limit是您可以写入缓冲区的数据数量的限制。在写模式下，Limit等于缓冲区的Capacity。

当将缓冲区翻转到读模式时，Limit意味着可以从数据读取多少数据。因此，当将缓冲区翻转到读模式时，Limit设置为写入模式的写入position。换句话说，您可以读取写入时一样多的字节数(将Limit设置为写入的字节数，即position)。

### 缓冲区类型
Java NIO有以下常见缓冲区类型：
- ByteBuffer
- MappedByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer

如您所见，这些缓冲区类型代表着不同的数据类型。换句话说，它们允许您使用缓冲区中的字节作为char、Short、int、long、Float或Double。

### 分配一个缓冲区
要获得缓冲区对象，必须首先分配它。每个缓冲区类都有一个执行此操作的allocate()方法。下面是一个字节容量为48个字节的ByteBuffer的分配的示例：
```
ByteBuffer buf = ByteBuffer.allocate(48);
```
下面是一个1024个字符的CharBuffer示例：

```
CharBuffer buf = CharBuffer.allocate(1024);
```
### 写数据到缓冲区
您可以通过两种方式将数据写入缓冲区：
- 将数据从通道写入缓冲区
- 通过缓冲区的put()方法，自己将数据写入缓冲区。

下面是一个示例，展示了通道如何将数据写入缓冲区：

```
int bytesRead = inChannel.read(buf); //read into buffer.
```
下面是一个通过put()方法将数据写入缓冲区的示例：

```
buf.put(127);
```
PUT()方法还有许多其他版本，允许您以多种不同的方式将数据写入缓冲区。例如，在特定位置写入或将字节数组写入缓冲区。有关具体缓冲区实现的详细信息，请参阅JavaDoc。

### flip()
flip()方法将缓冲区从写入模式切换到读取模式。调用flip()将position设置为0，并将limit设置为刚才position所在的位置。

换句话说，现在position标记读取位置，limit标记写入缓冲区的byte、char等的数量，即可以读取的byte、char等的限制。

### 从缓冲区读数据
从缓冲区读取数据有两种方法：
- 将数据从缓冲区读入通道
- 使用get()方法，自己从缓冲区读取数据。

下面是如何将数据从缓冲区读取到通道的示例：
```
//read from buffer into channel.
int bytesWritten = inChannel.write(buf);
```

下面是一个使用get()方法从缓冲区读取数据的示例：
```
byte aByte = buf.get(); 
```

GET()方法还有许多其他版本，允许您以多种不同的方式从缓冲区读取数据。例如，在特定位置读取，或从缓冲区读取字节数组。有关具体缓冲区实现的详细信息，请参阅JavaDoc。

### rewind()
Buffer.rewind()将position 设置为0，这样就可以重新读取缓冲区中的所有数据。limit仍然未被触及，因此仍然标记有多少元素(bytes,chars )。可以从缓冲区中读取。

### clear() 和 compact()
一旦完成了从缓冲区中读取数据的工作，您就必须让缓冲区为再次写入做好准备。您可以通过调用clear()或调用compact()来做到这一点。

如果调用clear()，则将position设置为0，并将limit设置为容量。换句话说，缓冲区被清除了。缓冲区中的数据未被清除。只有指示可以将数据写入缓冲区的位置的标记被清除了。

如果当调用clear()时缓冲区中有任何未读数据，那么那些数据将被“遗忘”，这意味着您不再有任何标记来指示已读取的数据和未读取的数据。

如果缓冲区中仍然有未读取的数据，并且希望稍后读取它，但是您需要先进行一些写入，则调用compact()而不是clear()。

compact()将所有未读数据复制到缓冲区的开头。然后将position 设置为上最后一个未读元素之后的位置。limit属性仍然设置为capacity，就像clear()一样。现在缓冲区可以写入了，但是您不会覆盖未读数据。

### mark() 和 reset()
您可以通过调用Buffer.mark()方法来标记缓冲区中的给定位置position 。然后，通过调用Buffer.Reset()方法，可以将该位置position 重置回标记的位置。以下是一个例子：

```
buffer.mark();
//call buffer.get() a couple of times, e.g. during parsing.
buffer.reset();  //set position back to mark.    
```

### equals()和compareTo()
可以使用equals()和compareto()比较两个缓冲区。

### equals()
两个缓冲区是相等的，如果：
1. 它们的类型相同(byte、char、int等)
2. 在缓冲区中有相同数量的剩余字节、字符等。
3. 所有剩余的字节、字符等都是相等的。

正如您所看到的，equals只能比较缓冲区的一部分，而不是它中的每个元素。实际上，它只是比较缓冲区中的剩余的（remaining）元素。

### compareTo()
compareto()方法比较缓冲区中的剩余元素(字节、字符等)，可用于例如排序例程。在下列情况下，一个缓冲区被认为比另一个缓冲区“小”：
- The first element which is equal to the corresponding element in the other buffer, is smaller than that in the other buffer.
- All elements are equal, but the first buffer runs out of elements before the second buffer does (it has fewer elements).

## Java NIO Selector
JavaNIO选择器是一个可以检查一个或多个JavaNIO通道实例，并确定哪些通道可以读或写的组件。这样，单个线程就可以管理多个通道，从而管理多个网络连接。

### 为什么使用选择器
只使用一个线程来处理多个通道的优点是，您需要更少的线程来处理这些通道。实际上，您可以只使用一个线程来处理所有的通道。对于操作系统来说，线程之间的切换非常昂贵，每个线程也占用操作系统中的一些资源(内存)。因此，使用的线程越少，理论上也越好。

不过，请记住，现代操作系统和CPU在多任务处理方面变得越来越好，因此多线程的开销随着时间的推移变得越来越小。事实上，如果一个CPU有多个核心，可能会因为不处理多个任务而浪费CPU的能力。无论如何，这一讨论并不在本文范畴。但可以说，我们可以使用Selector处理单个线程的多个通道。

下面是一个使用Selector处理3个通道的线程的示例：

![avatar](./images/nio-overview-selectors.png)

### 创建Selector
通过调用Selector.open()方法创建Selector，如下所示：

```
Selector selector = Selector.open();

```

### 在通道上注册选择器
要使用带有选择器的通道，必须向通道注册选择器。这是通过使用SelectableChannel.Registry()方法完成的，如下所示：
```
channel.configureBlocking(false);

SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
```

通道必须处于非阻塞模式，才能与选择器一起使用.这意味着您不能将FileChannel与选择器一起使用，因为FileChannel不能切换到非阻塞模式。但是套接字通道可以正常工作。

注意register()方法的第二个参数。这是一个“兴趣集（interest set）”，也就是通过选择器监听通道时所感兴趣的事件。有四个不同的监听事件：
- Connect
- Accept
- Read
- Write

通道里的“事件触发”也被称为“为该事件做好准备”。因此，成功连接到另一台服务器的通道也就是“连接就绪connect ready”。一个接受到入连接的服务器套接字通道也就是“连接”就绪。具有可读取数据的通道是“Read”就绪。一个准备好向其写入数据的通道是“写”就绪。

这四个事件由四个SelectionKey常量表示：

- SelectionKey.OP_CONNECT
- SelectionKey.OP_ACCEPT
- SelectionKey.OP_READ
- SelectionKey.OP_WRITE

如果您对多个事件感兴趣，可将常量OR到一起，如下所示：
```
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;    
```

#### SelectionKey
正如您在上一节中所看到的，当往通道上注册一个Selector时，register()方法将返回一个SelectionKey对象。这个SelectionKey对象包含一些有趣的属性：
- 兴趣集
- 就绪集
- 通道
- 选择器
- 附加对象 (可选)

### 通过选择器选择通道
一旦您向Selector注册了一个或多个通道，您就可以调用其中一个select()方法。这些方法返回感兴趣的事件已经“Ready”的通道(connect, accept, read or write)。换句话说，如果通道已经读就绪，您将从select()方法中接收准备好读取的通道。

下面是select()方法：

- int select()
- int select(long timeout)
- int selectNow()

#### selectedKeys()
一旦您调用了select()方法之一，并且它的返回值指示一个或多个通道已经就绪，您就可以通过调用selectors selectedKeys()方法来访问就绪通道。情况如下：

```
Set<SelectionKey> selectedKeys = selector.selectedKeys();    
```
在向通道注册Selector时，Channel.registry()方法将返回SelectionKey对象。此key 代表着使用该选择器向通道的注册。这些键可以通过selectedKeySet()方法访问。

您可以迭代此选定的密钥集以访问就绪通道。情况如下：
```
Set<SelectionKey> selectedKeys = selector.selectedKeys();

Iterator<SelectionKey> keyIterator = selectedKeys.iterator();

while(keyIterator.hasNext()) {
    
    SelectionKey key = keyIterator.next();

    if(key.isAcceptable()) {
        // a connection was accepted by a ServerSocketChannel.

    } else if (key.isConnectable()) {
        // a connection was established with a remote server.

    } else if (key.isReadable()) {
        // a channel is ready for reading

    } else if (key.isWritable()) {
        // a channel is ready for writing
    }

    keyIterator.remove();
}
```

#### wakeUp()
一个已经调用了select()方法而阻塞住的线程，可以通过在另一个线程调用Selector.wakeUp()方法来离开sleect（）方法，即便还没有通道出于就绪状态。这里的Selector是第一个调用select()而锁住的线程用到的Selector。

如果另一个线程调用wakeup()，并且当前没有线程在select()中被阻塞，那么调用select()的下一个线程将立即“唤醒”。

#### close()
完成Selector之后，调用它的Close()方法。这将关闭Selector并使在此Selector中注册的所有SelectionKey实例无效。此时通道本身并没有关闭。


### 完整的选择器示例
下面是一个完整的示例，它打开一个Selector，将它注册到通道(通道实例化被排除在外)，并继续监视Selector以了解这四个事件(accept, connect, read, write)的“准备状态”。


```
Selector selector = Selector.open();

channel.configureBlocking(false);

SelectionKey key = channel.register(selector, SelectionKey.OP_READ);


while(true) {

  int readyChannels = selector.selectNow();

  if(readyChannels == 0) continue;


  Set<SelectionKey> selectedKeys = selector.selectedKeys();

  Iterator<SelectionKey> keyIterator = selectedKeys.iterator();

  while(keyIterator.hasNext()) {

    SelectionKey key = keyIterator.next();

    if(key.isAcceptable()) {
        // a connection was accepted by a ServerSocketChannel.

    } else if (key.isConnectable()) {
        // a connection was established with a remote server.

    } else if (key.isReadable()) {
        // a channel is ready for reading

    } else if (key.isWritable()) {
        // a channel is ready for writing
    }

    keyIterator.remove();
  }
}
```


## 参考
- [1] [Java NIO 系列教程](https://ifeve.com/java-nio-all/)
- [2] [Java NIO Tutorial](http://tutorials.jenkov.com/java-nio/index.html)