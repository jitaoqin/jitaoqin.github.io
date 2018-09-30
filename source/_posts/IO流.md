title: IO流
date: 2018-09-19 10:49:11
categories: 前端
tags:
​	-java
​	-IO
​		
  流是一组有顺序的，有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在两设备间的传输称为流，流的本质是数据传输。
​	所有的文件在硬盘或在传输时都是以字节的方式进行的，在磁盘上是先把字符编码成字节，再储存这些字节到磁盘。在读取文件（特别是文本文件）时，也是一个字节一个字节地读取以形成字节序列。而字符是只有在内存中才会形成，所以在开发中，字节流使用较为广泛。

​	Java 内用 Unicode 编码存储字符，字符流处理类负责将外部的其他编码的字符流和 java 内 Unicode 字符流之间的转换。而类 InputStreamReader 和 OutputStreamWriter 处理字符流和字节流的转换。字符流（一次可以处理一个缓冲区）一次操作比字节流（一次一个字节）效率高。字符流使用了缓冲区，而字节流没有使用缓冲区。**在字符流的操作中，所有的字符都是在内存中形成的**，在输出前会将所有的内容暂时保存在内存之中，所以使用了缓冲区暂存数据。如果想在不关闭时也可以将字符流的内容全部输出，则可以使用Writer类中的flush()方法完成。进行字符集的转化通过 InputStreamReader,OutputStreamWriter来关联，实际上是通过byte[]和String来关联在从字节流转化为字符流时，实际上就是byte[]转化为String时，public String(byte bytes[], String charsetName)有一个关键的参数字符集编码，通常我们都省略了，那系统就用操作系统的lang而在字符流转化为字节流时，实际上是String转化为byte[]时，byte[]    String.getBytes(String charsetName)。 

​	字符流处理的单元为2个字节的Unicode字符，操作字符、字符数组或字符串，字节流处理单元为1个字节，操作字节和字节数组。所以字符流是由Java虚拟机将字节转化为2个字节的Unicode字符为单位的字符而成的，如果是音频文件、图片、歌曲，就用字节流好点，如果是关系到中文（文本）的，用字符流好点。 

​	字节流提供了处理任何类型的IO操作的功能，但它不能直接处理Unicode字符，而字符流就可以。Reader类的read()方法返回类型为int ：作为整数读取的字符（占两个字节共16位），范围在 0 到 65535 之间 (0x00-0xffff)，如果已到达流的末尾，则返回 -1。inputStream的read()也返回int，但由于此类是面向字节流的，一个字节占8个位，所以返回 0 到 255 范围内的 int 字节值。如果因为已经到达流末尾而没有可用的字节，则返回值 -1。因此对于不能用0-255来表示的值就得用字符流来读取！比如说汉字。 

字节流和字符流的主要区别是什么呢？

​     （1）字节流在操作时不会用到缓冲区（内存），是直接对文件本身进行操作的。而字符流在操作时使用了缓冲区，通过缓冲区再操作文件。

​     （2）在硬盘上的所有文件都是以字节形式存在的（图片，声音，视频），而字符值在内存中才会形成。 

​           如果一个程序频繁对一个资源进行IO操作，效率会非常低。此时，通过缓冲区，先把需要操作的数据暂时放入内存中，以后直接从内存中读取数据，则可以避免多次的IO操作，提高效率 。      

​       真正存储和传输数据时都是以字节为单位的，字符只是存在与内存当中的，所以，字节流适用范围更为宽广 

字节流和字符流的区别：

|          | 字节流         | 字符流                 |
| -------- | -------------- | ---------------------- |
| 处理对象 | 所有类型       | 字符类型               |
| 操作对象 | 字节，字节数组 | 字符，字符数组，字符串 |
| 读写单位 | 1个字节        | 2个字节的Unicode字符   |
| 缓冲区   | 否             | 有                     |

   

 使用中如何选择流对象??

1. 考虑最原始的数据格式是否为纯文本？
  （1）二进制格式: InputStream, OutputStream及其所有带Stream结束的子类  
  （2）纯文本格式:Reader, Writer及其所有带Reader, Writer的子类 
2. 是输入还是输出？
    （1）输入：Reader, InputStream类型的子类 
    （2）输出：Writer, OutputStream类型的子类
3. 是否需要转换流？
   InputStreamReader, OutputStreamWriter
4. 数据来源和目的是文件，内存，网络？
  （1）文件： FileInputStream, FileOutputStream, FileReader, FileWriter  
  （2）byte[]：ByteArrayInputStream, ByteArrayOutputStream   
  （3）Char[]: CharArrayReader, CharArrayWriter  
  （4）String: StringBufferInputStream, StringReader, StringWriter  
  （5）网络：InputStream, OutputStream, Reader, Writer 
5. 是否要缓冲？
   BufferedInputStream, BufferedOutputStream, BufferedReader,BufferedWriter
6. 是否格式化输出?
  （1）输入：Reader, InputStream类型的子类  
   （2）输出：Writer, OutputStream类型的子类  

7. 是否有特殊需要？
  （1）对象输入输出：ObjectInputStream, ObjectOutputStream
  （2）程间通信：PipeInputStream,PipeOutputStream,PipeReader,PipeWriter  
  （3）合并输入：SequenceInputStream    
  （4）更特殊的需要：PushbackInputStream, PushbackReader, LineNumberInputStream,LineNumberReader

2.例子二（字符流复制文本文件）：

``` java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
要求：复制文本文件。
需求分析：
1，原始的数据格式是什么：是否为文本？
	原始数据是文本文件，所以用字符流。
2，是输入还是输出? 
	需要复制，所以需要输入和输出。
3，是否需要转换流?
	因为操作过程中只涉及字符，所以不需要转换流。
4，数据来源和目的是什么：文件？内存？网络？
	数据源是硬盘，写入目的地也是硬盘。所以使用FileReader和FileWriter。
5，是否要缓冲？
	有和没有缓冲的情况都有。
6，是否要格式化输出？
	不需要格式化输出。
 */

public class CopyFileTest {

    public static void main(String[] args) throws IOException{
        readAndWriteFile_1();
        readAndWriteFile_2();
        readAndWriteFile_3();
    }    

    public static void readAndWriteFile_1()throws IOException{
        FileReader fr = new FileReader("c:\\fr.txt");
        FileWriter fw = new FileWriter("d:\\fw1.txt");   
		 int ch = 0;  
         while ((ch=fr.read())!=-1) {  //读一个字符，写一个字符。
            fw.write(ch);
            fw.flush();   //字符流写入时，需要刷新。
        }
		// 流使用完，必须要关闭。
        fr.close();
        fw.close();
    }    

    public static void readAndWriteFile_2() throws IOException    {

        FileReader fr = new FileReader("fos.txt");
        FileWriter fw = new FileWriter("d:\\fos2.txt");       
		 char[] cha = new char[1024];        //先把字符写入到字符数组中。
        int len = 0;
        while ((len=fr.read(cha))!=-1) {
            fw.write(cha,0,len);    //再把字符数组中的元素写入目的文件。
            fw.flush();        //字符流写入时，需要刷新。
        }

		// 流使用完，必须要关闭。
        fr.close();
        fw.close();

    }

    

    public static void readAndWriteFile_3() throws IOException

    {

        FileReader fr= new FileReader("fos.txt");
        FileWriter fw = new FileWriter("d:\\fos3.txt");
			//  使用缓冲区。
        BufferedReader bufr = new BufferedReader(fr);
        BufferedWriter bufw = new BufferedWriter(fw);       
        String len = null;
        while ((len=bufr.readLine())!=null) {  //每次把文本中的一行读进缓冲区（不含换行符）。
            bufw.write(len);    //再把一行数据写入目的文件（不含换行符）。
            bufw.newLine();      //换行方法。
            bufw.flush();        //字符流需要刷新。
        }

		// 流使用完，必须要关闭。
        bufr.close();
        bufw.close();
    }
}
```
​    前面有提过，字符流本身实际有使用缓冲技术，所以写入的时候需要手动刷新，让缓冲区的数据（内存）刷新到目的地。 

​    3.例子三（转换流，将键盘录入数据保存为文本文件）

 ```java

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStreamReader;
/*

要求：录入键盘数据，保存到文本文件。
需求分析：
1，原始的数据格式是什么：是否为文本？
	源数据是字节流。目的数据是字符流。
2，是输入还是输出? 
	需要读取和写入，所以需要输入和输出。
3，是否需要转换流?
	因为操作过程中有字节和字符，需要转换流。
4，数据来源和目的是什么：文件？内存？网络？
	数据源是键盘录入，写入目的地是硬盘。所以使用InputStreamReader和FileWriter。
5，是否要缓冲？
	需要。使用BufferedReadr和BufferedWriter。
6，是否要格式化输出？
	不需要格式化输出。
*/

public class TransStream { 
    public static void main(String[] args) {
        System.out.println("请输入要保存到文件中的内容（输入over结束）：");
		// System.in录入键盘数据得到的是字节流，如要用字符流的方式读取，必须要先将字节流转成字符流。
        InputStreamReader isr = new InputStreamReader(System.in);
        //        使用缓冲区读写更加效率。
        BufferedReader bufr = new BufferedReader(isr);
        BufferedWriter bufw = null;
        try {
        		// 也可以通过OutputStreamWriter来指定编码表。
        
	// bufw = new BufferedWriter(new OutputStreamWriter("c:\\systemin.txt","UTF-8"));  

            bufw = new BufferedWriter(new FileWriter("c:\\systemin.txt"));
            String str = null;
            while ((str=bufr.readLine())!=null) {
                if ("over".equals(str))
                    System.exit(0);        //如果键盘录入over，则结束。
                bufw.write(str);
                bufw.newLine();
                bufw.flush(); 
            }
        } catch (IOException e) {
        	throw new RuntimeException("找不到目标文件或写入数据失败");
        } finally {
            try {
                if (bufw!=null) {
                    bufr.close();
                    bufw.close();
                }
            } catch (IOException e) {  
            throw new RuntimeException("关闭资源失败");
            }
        }
    }
}
```

​    从以上例子可以看出，转换流是字符流和字节流之间的桥梁，我们实际也可以利用转换流把文本文件中的内容打印到控制台上。也就是说可以通过转换流让字符流和字节流相互转换。 

​    另外要注意的是，将字节转换成字符的时，想按照指定的码表来转换，也可以通过OutputStreamWriter（OutputStream out，CharsetEncoder enc）的方式指定，其中enc即代表码表的名字，如"UTF-8","ISO 8859-1"等。 

 4.例子四（利用打印流把键盘录入数据保存到文件）： 
``` java

import java.io.BufferedReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
/*
要求：录入键盘数据，保存到文本文件。
需求分析：
1，原始的数据格式是什么：是否为文本？
	源数据是字节流。目的数据是字符流。
2，是输入还是输出? 
	需要读取和写入，所以需要输入和输出。
3，是否需要转换流?
	因为操作过程中有字节和字符，需要转换流。
4，数据来源和目的是什么：文件？内存？网络？
	数据源是键盘录入，写入目的地是硬盘。所以使用InputStreamReader和FileWriter。
5，是否要缓冲？
	需要。使用BufferedReadr。
6，是否要格式化输出？
	需要格式化输出。使用PrintWriter。

*/

public class PrintStream {
​    public static void main(String[] args) throws IOException{
​        BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
//        PrintWriter可以设置刷新模式，如果刷新模式为true，那么PrintWriter部分方法输出是会自动刷新。
​      	PrintWriter out = new PrintWriter(new FileWriter("c:\\a.txt"),true);
​        String line = null;
​        while ((line=bufr.readLine())!=null) {
//            如果键盘输入over，则结束程序。
​            if("over".equals(line))
​                break;
//            因为PrintWriter设置有刷新模式，不需再手动刷新。
​            out.println(line.toUpperCase());
​        }
​        bufr.close();
​        out.close();
​    }
}
```
​    看完例子，在看看打印流的特点
​    打印流：   该流提供了打印方法，可以将各种数据类型的数据都原样打印。
​    字节打印流：  PrintStream
​    构造函数可以接收的参数类型：1）file对象。File（2）字符串路径。String（3）字节输出流。OutputStream

​    字符打印流：   PrintWriter
构造函数可以接收的参数类型：   （1）file对象。File   （2）字符串路径。String
（3）字节输出流。OutputStream（4）字符输出流。Writer

 ​    5.例子五（LineNumberReader）：
``` java
import java.io.FileReader;
import java.io.IOException;
import java.io.LineNumberReader; 

public class LineNumberReaderDemo {
​    public static void main(String[] args)throws IOException {
​        FileReader fr = new FileReader("PersonDemo.java");
​        LineNumberReader lnr = new LineNumberReader(fr);
​        String line = null;
​        lnr.setLineNumber(100);
​        while((line=lnr.readLine())!=null)
​            System.out.println(lnr.getLineNumber()+":"+line);
​        lnr.close();
​    }
}
```
​    从 以上例子可以看出，LineNumberReader属于过滤流，而它实际的功能也是在字符写入流的基础上，加上获取行号（getLineNumber()），设置行号（setLineNumber()）的功能。LineNumberOutputStream同理，这里不再做阐述。 

​    6.例子六（数据流）：
``` java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class DataStreamDemo {

​    public static void main(String[] args) throws IOException{
​        writeData();
​        readData();
​    }

​    public static void readData()throws IOException{
​        DataInputStream dis = new DataInputStream(new FileInputStream("data.txt"));
​        int num = dis.readInt();
​        boolean b = dis.readBoolean();
​        double d = dis.readDouble();

​        System.out.println("num="+num);
​        System.out.println("b="+b);
​        System.out.println("d="+d);

​        dis.close();
​    }

​    public static void writeData()throws IOException{
​        DataOutputStream dos = new DataOutputStream(new FileOutputStream("data.txt"));
​        dos.writeInt(234);
​        dos.writeBoolean(true);
​        dos.writeDouble(9887.543);
​        dos.close();
​    }
} 
​    以上运行结果是：
​    234
​    true
​    9887.543
```

​    从例子可以看出数据流的特点：
​    DataInputStream和DataOutputStream，经常用来处理基本数据类型的数据，好处就是可以按类型来处理数据。 

​    7.例子七（数组流）：


``` java

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;

public class ByteArrayStream {
​    public static void main(String[] args) {
​        //数据源。
​        ByteArrayInputStream bis = new ByteArrayInputStream("ABCDEFD".getBytes());
​        //数据目的，数组输出流输出的数据目的地是内存。
​        ByteArrayOutputStream bos = new ByteArrayOutputStream();

​        int by = 0;
​        while((by=bis.read())!=-1)
​            bos.write(by);

​        System.out.println(bos.size());
​        System.out.println(bos.toString());
//        最后内存中流的数据可以通过writeTo方法输出到其他节点流中，将数据保存到目的地。
​    //    bos.writeTo(new FileOutputStream("a.txt"));
​    }
}
​    运行结果为：
​    7
​    ABCDEFD
```
​    数组流是用于操作字节数组的流对象，它是使用流的思想来操作数据的体现。
​    ByteArrayInputStream ：在构造的时候，需要接收数据源，而且数据源是一个字节数组。
​    ByteArrayOutputStream：在构造的时候，不用定义数据目的，因为该对象中已经内部封装了可变长度的字节数组，这就是数据目的地（在内存中）。
​    因为这两个流对象都操作的数组，并没有使用系统资源。
​    所以，不用进行close关闭。

​    8.例子八（RandomAccessFile）：
``` java
import java.io.IOException;
import java.io.RandomAccessFile;

public class RandomAccessFileDemo {
​    public static void main(String[] args) throws IOException{
​        writeFile();
​        writeFile_2();
​        readFile();
​    }

​    public static void readFile()throws IOException{
​        RandomAccessFile raf = new RandomAccessFile("ran.txt","r");
​        //调整对象中指针，以8为单位是因为两个中文字4个字节加上int数据的4个字节。
​        //raf.seek(8*1);
​        //跳过指定的字节数
​        raf.skipBytes(8);
​        byte[] buf = new byte[4];
​        raf.read(buf);
​        String name = new String(buf);
​        int age = raf.readInt();
​        System.out.println("name="+name);    //结果为周七。
​        System.out.println("age="+age);
​        raf.close();
​    }
//    也能修改指定指针上的数据。
​    public static void writeFile_2()throws IOException{
​        RandomAccessFile raf = new RandomAccessFile("ran.txt","rw");
​        raf.seek(8*0);
​        raf.write("周七".getBytes());
​        raf.writeInt(103);
​        raf.close();
​    } 

​    public static void writeFile()throws IOException{
​        RandomAccessFile raf = new RandomAccessFile("ran.txt","rw");
​        raf.write("李四".getBytes());
​        raf.writeInt(97);
​        raf.write("王五".getBytes());
​        raf.writeInt(99);
​        raf.close();
​    }
}

​    运行结果为：
​    周七
​    103
```


​    从前面的派生图可以看出，RandomAccessFile不算是IO体系中子类，而是直接继承自Object。但是它是IO包中成员，因为它具备读和写功能。

​    实际上RandomAccessFile内部封装了一个数组，而且通过指针对数组的元素进行操作。
​    从例子可以看出通过getFilePointer获取指针位置，同时通过seek改变指针的位置。
​    至于RandomAccessFile能够同时实现读和写的功能的原因，其实是是其在内部封装了字节输入流和输出流。
​    通过查询API中构造函数可以看出，该类只能操作文件，而且操作文件还有模式：只读r，，读写rw等。
​    如果模式为只读 r。不会创建文件。会去读取一个已存在文件，如果该文件不存在，则会出现异常。
​    如果模式rw。操作的文件不存在，会自动创建。如果存则不会覆盖。

​    9.例子九（管道流）：

 ``` java
import java.io.IOException;
import java.io.PipedInputStream;
import java.io.PipedOutputStream;

class Read implements Runnable {
​    private PipedInputStream in;
​    Read(PipedInputStream in){
​        this.in = in;
​    }

​    public void run() {
​        try {

​            byte[] buf = new byte[1024];
​            System.out.println("读取前。。没有数据阻塞");
​            int len = in.read(buf);
​            System.out.println("读到数据。。阻塞结束");

​            String s= new String(buf,0,len);
​            System.out.println(s);
​            in.close();
​        }  catch (IOException e) {
​            throw new RuntimeException("管道读取流失败");
​        }
​    }
} 

class Write implements Runnable {
​    private PipedOutputStream out;
​    Write(PipedOutputStream out) {
​        this.out = out;
​    }

​    public void run() {
​        try {
​            System.out.println("开始写入数据，等待6秒后。");
​            Thread.sleep(6000);
​            out.write("piped lai la".getBytes());
​            out.close();
​        }  catch (Exception e) {
​            throw new RuntimeException("管道输出流失败");
​        }
​    }
}

 

public class  PipedStreamDemo {

​    public static void main(String[] args) throws IOException {
​        PipedInputStream in = new PipedInputStream();
​        PipedOutputStream out = new PipedOutputStream();
//        管道输入流连接到管道输出流。
​        in.connect(out);
​        Read r = new Read(in);
​        Write w = new Write(out);
​        new Thread(r).start();
​        new Thread(w).start();
​    }

}
```

​    运行结果为：
​    读取前。。没有数据阻塞
​    开始写入数据，等待6秒后。
​    读到数据。。阻塞结束
​    piped lai la
 
​    看完例子，再来看看管道流的特点：
​    PipedInputStream类与PipedOutputStream类用于在应用程序中创建管道通信，多用于多线程中。
​    一 个PipedInputStream实例对象必须和一个PipedOutputStream实例对象进行连接而产生一个通信管道。 PipedOutputStream可以向管道中写入数据,PipedIntputStream可以读取PipedOutputStream向管道中写入的数据。
​    这两个类主要用来完成线程之间的通信。一个线程的PipedInputStream对象能够从另外一个线程的PipedOutputStream对象中读取数据。
​    所以，我们使用管道流时，必须要同时构造管道输入流和管道输出流。 

​    10.例子十（合并流）： 
``` java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.SequenceInputStream;
import java.util.ArrayList;
import java.util.Enumeration;
import java.util.Iterator; 

public class MergeFile {

​    public static void main(String[] args) throws IOException {
​        merge();
​    }

 

​    public static void merge() throws IOException {
//        创建集合，泛型为文件字节输入流。
​        ArrayList<FileInputStream> al = new ArrayList<FileInputStream>();
​        for(int x=1; x<=3; x++)
//            将输入流添加进集合中。
​            al.add(new FileInputStream("c:\\splitfiles\\"+x+".part"));
​        final Iterator<FileInputStream> it = al.iterator();
//        因为前面为了更加让程序效率使用了ArrayList，所以这里只能通过匿名内部类自定义Enumeration，实现目的。
//        如果前面使用的是Vetor集合，则不需再定义这一步。
​        Enumeration<FileInputStream> en = new Enumeration<FileInputStream>(){
​            public boolean hasMoreElements() {
​                return it.hasNext();
​            }

​            public FileInputStream nextElement() {
​                return it.next();
​            }
​        }; 

​        SequenceInputStream sis = new SequenceInputStream(en);
​        FileOutputStream fos = new FileOutputStream("c:\\splitfiles\\0.bmp");
​        byte[] buf = new byte[1024];
​        int len = 0;
​        while((len=sis.read(buf))!=-1)
​            fos.write(buf,0,len);
​        fos.close();
​        sis.close();
​    }
}

```

​    合并流，SequenceInputStream表示其他输入流的逻辑串联。它从输入流的有序集合开始，并从第一个输入流开始读取，直到到达文件末尾，接着从第二个输入流读取，依次类推，直到到达包含的最后一个输入流的文件末尾为止。总而言之，合并流就是用来合并多个字节输出流变成一个字节输出流。


​    11.例子十一（分割文件）：

``` java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class SplitFile {
​    public static void main(String[] args) throws IOException {
​        splitFile();
​    } 

​    public static void splitFile()throws IOException {
​        FileInputStream fis =  new FileInputStream("c:\\1.bmp");
​        FileOutputStream fos = null;
​        byte[] buf = new byte[1024*1024];
​        int len = 0;
​        int count = 1;
​        while((len=fis.read(buf))!=-1) {
​            fos = new FileOutputStream("c:\\splitfiles\\"+(count++)+".part");
​            fos.write(buf,0,len);
​            fos.close();
​        }  
   fis.close();     
​    }
}

```

​    可以看出，java中并没有可以直接分割流的对象。分割流的方法很多，以上例子主要是通过计数器，和作为缓冲区的数组来控制分割流，然后输出到不同的文件上。