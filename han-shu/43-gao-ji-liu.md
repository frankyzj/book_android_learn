# 4.3 高级流 {#43-高级流}

### 处理流的用法 {#处理流的用法}

下例使用 PrintStream 处理流来包装OutputStream，使用处理流后的输出流在输出时更加方便。

```
public class PrintStreamTest{
  public static void main(String[] args){
    try(
      FileOutputStream fos = new FileOutputStream("test.txt");
      PrintStream ps = new PrintStream(fos);
    ){
      //输出字符串
      ps.println("字符串");
      //输出对象
      ps.println(new PrintStreamTest());
      //通常如果需要输出文本内容，都应该将输出流包装成PrintStream后进行输出，System.out的类型就是PrintStream。

    }catch(IOException ioe){
      ioe.printStackTrace();
    }
  }
}

```

* 注：在使用处理流包装了底层节点流之后，关闭输入输出流资源时，只要关闭上层的处理流即可。关闭最上层的处理流时，系统会自动关闭被包装的节点流。

### 输入输出流体系 {#输入输出流体系}

java.io包下的流如下表：

| 分类 | 字节输入流 | 字节输出流 | 字符输入流 | 字符输出流 |
| :--- | :--- | :--- | :--- | :--- |
| 抽象基类 | InputStream | OutputStream | Reader | Writer |
| 访问文件 | FileInputStream | FileOutputStream | FileReader | FileWriter |
| 访问数组 | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader | CharArrayWriter |
| 访问管道 | PipedInputStream | PipedOutputStream | PipedReader | PipedWriter |
| 访问字符串 |  |  | StringReader | StringWriter |
| 缓冲流 | BufferedInputStream | BufferedOutputStream | BufferedReader | BufferedWriter |
| 转换流 |  |  | InputStreamReader | OutputStreamWriter |
| 对象流 | ObjectInputStream | ObjectOutputStream |  |  |
| 抽象基类 | FilterInputStream | FilterOutputStream | FilterReader | FilterWriter |
| 打印流 |  | PrintStream |  | PrintWriter |
| 推回输入流 | PushbackInputStream |  | PushbackReader |  |
|  | 特殊流 | DataInputStream | DataOutputStream |  |

上表中访问管道的流是用于实现进程之间通信功能的。缓冲流可以提高输入输出效率，增加缓冲功能后需要调用flush\(\)。d对象流主要用于实现对象的序列化。

### 转换流 {#转换流}

InputStreamReader 将字节输入流转换成字符输入流，OutputStreamWriter 将字节输出流转换成字符输出流。 Java使用 System.in 代表标准输入，即键盘输入，但这个标准输入流是 InputStream 类的实例，而键盘输入内容都是文本，所以使用 InputStreamReader 将其转换成字符串输入流，进一步将 Reader 包装成 BufferedReader,其readLine\(\)方法可以一次读取一行内容。如下例：

```
try(
  InputStreamReader reader = new InputStreamReader(System.in);
  BufferedReader br = new BufferedReader(reader);
){
  String line = null;
  while((line = br.readLine()) != null){//一次读取一行——以换行符为标志，未读到换行符，则程序阻塞，直到读到换行符。
    //如果读到“exit”，则程序退出
    if(line.equals("exit")){
      System.exit(1);
    }
    System.out.println("输入内容为：" + line);
  }
}catch(IOException ioe){
  ioe.printStackTrace();
}
```



