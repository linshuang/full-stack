# Java IO Tutorial
# 引言
Java IO 是Java附带的API，用于读取和写入数据(输入和输出)。大多数应用程序需要处理一些输入，并根据这些输入产生一些输出。例如，从文件或网络读取数据，并通过网络写入文件或写入响应。

## Java IO (java.io)包的范围
java.io包实际上并不能处理所有类型的输入和输出。例如，JavaIO包中没有涵盖来自GUI或网页的输入和输出。这些类型的输入在其他地方包括，例如Swing项目中的JFC类，或者JavaEnterpriseEdition中的servlet和HTTP包。

Java IO包主要关注对文件、网络流、内存缓冲区等的输入和输出。然而，Java IO包不包含打开网络套接字的类，而这些类是网络通信所必需的。为此，您需要使用Java网络API。但是，一旦打开了套接字(网络连接)，就可以通过Java IO的InputStream和OutputStream类向它读写数据。

## Java NIO 另一个可供替换的IO API
Java还包含另一个名为Java NIO的IO API。它包含的类与Java IO和Java网络API有很大的相同，但是Java NIO可以在非阻塞模式下工作。在某些情况下，非阻塞IO相比阻塞IO有巨大的性能提升。

## Java IO 类概述表
| 类别 | 基于字节的输入 | 基于字节的输出 | 基于字符的输入 | 基于字符的输出 |
| ---- | ---- | ---- | ---- | ---- |
| Basic | InputStream | OutputStream | InputStreamReader | OutputStreamWriter |
| Arrays | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader | CharArrayWriter |
| Files | FileInputStream/RandomAccessFile | FileOutputStream/RandomAccessFile | FileReader | FileWriter |
| Pipes | PipedInputStream | PipedOutputStream | PipedReader | PipedWriter |
| Buffering | BufferedInputStream | BufferedOutputStream | BufferedReader | BufferedWriter |
| Filtering | FilterInputStream | FilterOutputStream | FilterReader | FilterWriter |
| Parsing | PushbackInputStream/StreamTokenizer  | | PushbackReader/LineNumberReader | | 
| Strings |  |  | StringReader | StringWriter |
| Data | DataInputStream | DataOutputStream |  |  |
| Data-Formatted |  | PrintStream |  | PrintWriter |
| Objects | ObjectInputStream | ObjectOutputStream |  |  |
| Utilities | SequenceInputStream | |  |  |



## 参考
- [1] [Java IO Tutorial](http://tutorials.jenkov.com/java-io/index.html)