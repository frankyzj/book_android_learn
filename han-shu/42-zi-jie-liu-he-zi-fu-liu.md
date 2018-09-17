# 4.2 字节流和字符流 {#42-字节流和字符流}

### InputStream 和 Reader {#inputstream-和-reader}

所有输入流的抽象基类。

| InputStream | Reader | 解释 |
| :--- | :--- | :--- |
| int read\(\) | int read\(\) | 从输入流中读取单个字节/字符，返回读取的字节/字符数据 |
| int read\(byte\[\] b\) | int read\(char\[\] cbuf\) | 从输入流中读取最多 b.length / cbuf.length 个字节/字符的数据，并将其存储在字节b/字符数组cbuf中，返回实际读取的字节/字符数 |
| int read\(byte\[\] b, int off, int len\) | int read\(char\[\] cbuf, int off, int len\) | 从输入流中读取最多 len个字节/字符的数据，并将其存储在字节b/字符数组cbuf中，放入数组中时，并不是从数组起点开始，而是从off位置开始，返回实际读取的字节/字符数 |

InputStream 和 Reader 分别有一个读取文件的输入流：FileInputStream 和 FileReader，它们都是节点流——会直接和指定文件关联。

```
FileInputStream fis = new FileInputStream("test.txt");
byte[] bbuf = new byte[1024];//GBK编码方式下，中文字符占2字节，read()方法有可能只读到半个中文字符，这将导致乱码。
int hasRead = 0;
while((hasRead = fis.read(bbuf)) 
>
 0){
  System.out.print(new String(bbuf, 0, hasRead));
}
fis.close();//程序里打开的文件IO资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以需要显式关闭。

```

```
try(FileReader fr = new FileReader("FileReader.txt")){//Java7改写了所有的IO资源类，它们都实现了AntoCloseable接口，可通过自动关闭资源的try语句来关闭这些IO流。
  char[] cbuf = new char[32];
  int hasRead = 0;
  while((hasRead = fr.read(cbuf)) 
>
 0){
    System.out.print(new String(cbuf, 0, hasRead));
  }
}catch(IOException ex){
  ex.printStackTrace();
}

```

### OutputStream 和 Writer {#outputstream-和-writer}

都有如上read\(\)方法的write\(\)方法。 下例使用 FileInputStream 和 FileOutputStream 实现复制文件的功能。

```
try(
  FileInputStream fis = new FileInputStream("test.txt");
  FileOutputStream fos = new FileOutputStream("newTest.txt");
){
  byte[] bbuf = new byte[32];
  int hasRead = 0;
  while((hasRead = fis.read(bbuf)) 
>
 0){
    fos.write(bbuf, 0, hasRead);
  }
}catch(IOException ioe){
  ioe.printStackTrace();
}

```

* 注： 同样，使用try语句，保证输出流先调用flush\(\)方法把输出流缓冲区的数据flush到物理节点，然后调用close\(\)方法关闭流。
  ```
  try(FileWriter fw = new FileWriter("poem.txt")){
  fw.write("一二三四五\r\n");//windows平台使用“\r\n”作为换行符，UNIX/Linux/BSD等平台，则使用“\n”作为换行符。
  fw.write("上山打老虎\r\n");
  }catch(IOException ioe){
  ioe.printStackTraces();
  }
  ```



