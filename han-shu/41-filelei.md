# 4.1 File类 {#41-file类}

File代表与平台无关的文件和目录，File类可以对文件和目录进行新建、删除、重命名等操作，但不能访问文件内容本身，访问文件内容本身需要使用输入输出流。

## 4.1.1 访问文件和目录 {#411-访问文件和目录}

File类可以使用文件路径字符串来创建File实例，

##### 访问文件名相关的方法 {#访问文件名相关的方法}

String getName\(\);返回File对象的文件名或者路径名；  
String getPath\(\);路径名；  
File getAbsoluteFile\(\);绝对路径；  
String getAbsolutePath\(\);绝对路径名；  
String getParent\(\);父目录名；  
boolean renameTo\(File newName\);重命名；

##### 文件检测相关的方法 {#文件检测相关的方法}

exists\(\);  
canWrite\(\);  
canRead\(\);  
isFile\(\);  
isDirectory\(\);  
isAbsolute\(\);判断File对象所对应的文件或目录是否绝对路径。该方法消除了不同平台的差异，可以直接判断File对象是否为绝对路径。

##### 获取常规文件信息 {#获取常规文件信息}

long lastModified\(\);返回文件的最后修改时间；  
long length\(\);返回文件内容的长度；

##### 文件操作相关的方法 {#文件操作相关的方法}

createNewFile\(\);当File对象对应的文件在磁盘上不存在时，创建新的。  
delete\(\);删除；  
static File createTempFile\(String prefix, String suffix\);在默认的临时文件目录中创建一个临时的空文件，使用给定的前缀、系统生成的随机数和给定后缀作为文件名。prefix要求至少3字节长，suffix可以为null，此时将使用默认后缀“.tmp”。  
static File createTempFile\(String prefix, String suffix, File directory\);  
void deleteOnExit\(\);注册一个删除钩子，指定当Java虚拟机退出时，删除File对象所对应的文件和目录。

##### 目录操作相关方法 {#目录操作相关方法}

mkdir\(\);创建一个File对象所对应的目录，此时File对象必须对应一个路径。  
String\[\] list\(\);列出File对象的所有子文件和路径名。  
File\[\] listFiles\(\);列出File对象的所有子文件和路径。  
static File\[\] listRoots\(\);列出系统所有的根路径。

* 注： Windows 的路径分隔符使用反斜线，在Java程序中，“\”表示转义字符，所以应当使用两条“\”，也可以使用“/”，Java程序中“/”是平台无关的路径分隔符。

## 4.1.2 文件过滤器 {#412-文件过滤器}

上面的list\(\)方法可以接收一个FilenameFilter参数，从而只列出符合条件的文件。FilenameFilter接口中有一个accept\(File dir, String name\)方法,负责对File的所有子目录或者文件进行迭代，该方法返回true，则list\(\)方法列出该子目录或者文件。

```
Flie file = new File(".");
//使用Lambda表达式（目标类型为FilenameFilter）实现文件过滤器
//如果文件名以.java结尾，或者对应一个路径，则返回true
String[] nameList = file.list((dir, name) -
>
 name.endsWith(".java") || new File(name).isDirectory);
for(String name : nameList){
  System.out.println(name);
}
```



