## 1. Java中字节流和字符流

字节（Byte）和字符（Character）的大小：

- 1 byte = 8bit[^1] 
- 1 char = 2byte = 16 bit[^2]

[^1]: 就是常用的文件大小单位B
[^2]: Java默认UTF-16编码

虽然1 bit才是数据真正的最小单位，但1 bit 的信息量太少了。要表示一个有用的信息，需要好几个bit一起表示。所以除了硬件层面存在1个比特位的寄存器，大多数情况下，字节是最小的基本单位。我们熟知的基本类型的大小都是8 bit（1字节）的整数倍：

- boolean: 1 byte
- short: 2 byte
- int: 4 byte
- float: 4 byte
- long: 8 byte
- double: 8 byte

**在计算机中，一切都是字节流，其实没有字符流这个东西。字符只是根据编码集对字节流翻译之后的产物。**

Java库有两个支系：

- 面向字节流的InputStream和OutputStream
- 面向字符流的Reader和Writer

字节流的InputStream和OutputStream是一切的基础。实际总线中流动的只有字节流。需要对字节流做特殊解码才能得到字符流。Java中负责从字节流向字符流解码的桥梁是：

- **InputStreamReader**
- **InputStreamWriter**


---

**下图是InputStreamReader和OutputStreamWriter的结构图**

![InputStreamReader.jpg](https://upload-images.jianshu.io/upload_images/10465938-2925a888bd6c3579.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![OutputStreamWriter.jpg](https://upload-images.jianshu.io/upload_images/10465938-02bf0d1f9343852f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

## 2. Java中IO的使用

**如前所述，一个流被定义为一个数据序列。输入流用于从源读取数据，输出流用于向目标写数据。**

**下图是描述输入流和输出流的类层次图：**

![IO类层次图](https://upload-images.jianshu.io/upload_images/10465938-dbc0e4f80886ec3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### 1. 输入字节流InputStream：

- `InputStream` 是所有的输入字节流的父类，它是一个抽象类。
- `ByteArrayInputStream`、`StringBufferInputStream`、`FileInputStream` 是三种基本的介质流，它们分别从`Byte 数组`、`StringBuffer`、和`本地文件`中读取数据。
- `PipedInputStream` 是从与其它线程共用的管道中读取数据
- `ObjectInputStream` 和所有`FilterInputStream` 的子类都是装饰流（装饰器模式的主角）。

#### 2. 输出字节流 OutputStream

- `OutputStream` 是所有的输出字节流的父类，它是一个抽象类。
- `ByteArrayOutputStream`、`FileOutputStream` 是两种基本的介质流，它们分别向`Byte 数组`、和`本地文件`中写入数据。
- `PipedOutputStream` 是向与其它线程共用的管道中写入数据。
- `ObjectOutputStream` 和所有`FilterOutputStream` 的子类都是装饰流。

**总结：**

- 输入流：InputStream或者Reader：从文件中读到程序中；
- 输出流：OutputStream或者Writer：从程序中输出到文件中；

#### 3. 节点流

节点流：直接与数据源相连，读入或读出。
直接使用节点流，读写不方便，为了更快的读写文件，才有了处理流。
![节点流](https://upload-images.jianshu.io/upload_images/10465938-999e2141f1cc20f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**常用的节点流：**

- 父　类 ：`InputStream` 、`OutputStream`、 `Reader`、 `Writer`
- 文　件 ：`FileInputStream` 、 `FileOutputStrean` 、`FileReader` 、`FileWriter` 文件进行处理的节点流
- 数　组 ：`ByteArrayInputStream`、 `ByteArrayOutputStream`、 `CharArrayReader` 、`CharArrayWriter` 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）
- 字符串 ：`StringReader`、 `StringWriter` 对字符串进行处理的节点流
- 管　道 ：`PipedInputStream` 、`PipedOutputStream` 、`PipedReader` 、`PipedWriter` 对管道进行处理的节点流

#### 4. 处理流

处理流和节点流一块使用，在节点流的基础上，再套接一层，套接在节点流上的就是处理流。如`BufferedReader`.处理流的构造方法总是要带一个其他的流对象做参数。一个流对象经过其他流的多次包装，称为流的链接。

![处理流](https://upload-images.jianshu.io/upload_images/10465938-43bb94c3fead84ab.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**常用的处理流**

- 缓冲流：`BufferedInputStrean` 、`BufferedOutputStream`、 `BufferedReader`、 `BufferedWriter` 增加缓冲功能，避免频繁读写硬盘。
- 转换流：`InputStreamReader` 、`OutputStreamReader`实现字节流和字符流之间的转换。
- 数据流： `DataInputStream` 、`DataOutputStream` 等-提供将基础数据类型写入到文件中，或者读取出来。

**转换流**

`InputStreamReader` 、`OutputStreamWriter` 要`InputStream`或`OutputStream`作为参数，实现从字节流到字符流的转换。

| 构造函数                                          | 说明                                                  |
| ------------------------------------------------- | ----------------------------------------------------- |
| `InputStreamReader(InputStream)`                  | 通过该构造函数初始化，使用的是系统默认的编码表GBK。   |
| `InputStreamReader(InputStream,String charSet)`   | 通过该构造函数初始化，可以指定编码表。                |
| `OutputStreamWriter(OutputStream)`                | 通过该构造函数初始化，使用的是本系统默认的编码表GBK。 |
| `OutputStreamwriter(OutputStream,String charSet)` | 通过该构造函数初始化，可以指定编码表。                |



#### 5. 如何使用

##### 1. Io体系的基类文件流的使用(FileInputStream/FileReader ，FileOutputStream/FileWriter)

**前面说过InputStream和Reader都是抽象类，本身不能创建实例，但它们分别有一个用于读取文件的输入流：FileInputStream和FileReader，它们都是节点流——会直接和指定文件关联。下面程序示范使用FileInputStream和FileReader。** 
**使用FileInputStream读取文件：**

```
public class MyClass {
  public  static void main(String[] args)throws IOException{
      FileInputStream fis=null;
      try {
          //创建字节输入流
          fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
          //创建一个长度为1024的竹筒
          byte[] b=new byte[1024];
          //用于保存的实际字节数
          int hasRead=0;
          //使用循环来重复取水的过程
          while((hasRead=fis.read(b))>0){
              //取出竹筒中的水滴（字节），将字节数组转换成字符串进行输出
            System.out.print(new String(b,0,hasRead));
          }
      }catch (IOException e){
        e.printStackTrace();
      }finally {
          fis.close();
      }
  }
}12345678910111213141516171819202122
```

> 注：上面程序最后使用了fis.close()来关闭该文件的输入流，与JDBC编程一样，程序里面打开的文件IO资源不属于内存的资源，垃圾回收机制无法回收该资源，所以应该显示的关闭打开的IO资源。Java 7改写了所有的IO资源类，它们都实现了AntoCloseable接口，因此都可以通过自动关闭资源的try语句来关闭这些Io流。

使用FileReader读取文件：

```
public class FileReaderTest {
    public  static void main(String[] args)throws IOException{
        FileReader fis=null;
        try {
            //创建字节输入流
            fis=new FileReader("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建一个长度为1024的竹筒
            char[] b=new char[1024];
            //用于保存的实际字节数
            int hasRead=0;
            //使用循环来重复取水的过程
            while((hasRead=fis.read(b))>0){
                //取出竹筒中的水滴（字节），将字节数组转换成字符串进行输出
                System.out.print(new String(b,0,hasRead));
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            fis.close();
        }
    }
}12345678910111213141516171819202122
```

可以看出使用FileInputStream和FileReader进行文件的读写并没有什么区别，只是操作单元不同而且。

FileOutputStream/FileWriter是Io中的文件输出流，下面介绍这两个类的用法。

FileOutputStream的用法：

```
public class FileOutputStreamTest {
    public  static void main(String[] args)throws IOException {
        FileInputStream fis=null;
        FileOutputStream fos=null;
        try {
            //创建字节输入流
            fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建字节输出流
            fos=new FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\newTest.txt");

            byte[] b=new byte[1024];
            int hasRead=0;

            //循环从输入流中取出数据
            while((hasRead=fis.read(b))>0){
                //每读取一次，即写入文件输入流，读了多少，就写多少。
                fos.write(b,0,hasRead);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            fis.close();
            fos.close();
        }
    }
}1234567891011121314151617181920212223242526
```

运行程序可以看到输出流指定的目录下多了一个文件：newTest.txt, 该文件的内容和Test.txt文件的内容完全相同。FileWriter的使用方式和FileOutputStream基本类似，这里就带过。

> 注： 使用java的io流执行输出时，不要忘记关闭输出流，关闭输出流除了可以保证流的物理资源被回收之外，可能还可以将输出流缓冲区中的数据flush到物理节点中里（因为在执行close（）方法之前，自动执行输出流的flush（）方法）。java很多输出流默认都提供了缓存功能，其实我们没有必要刻意去记忆哪些流有缓存功能，哪些流没有，只有正常关闭所有的输出流即可保证程序正常。

##### 2. 缓冲流的使用（BufferedInputStream/BufferedReader, BufferedOutputStream/BufferedWriter）： 

下面介绍字节缓存流的用法（字符缓存流的用法和字节缓存流一致就不介绍了）：

```
public class BufferedStreamTest {
    public  static void main(String[] args)throws IOException {
        FileInputStream fis=null;
        FileOutputStream fos=null;
        BufferedInputStream bis=null;
        BufferedOutputStream bos=null;
        try {
            //创建字节输入流
            fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建字节输出流
            fos=new FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\newTest.txt");
            //创建字节缓存输入流
            bis=new BufferedInputStream(fis);
            //创建字节缓存输出流
            bos=new BufferedOutputStream(fos);

            byte[] b=new byte[1024];
            int hasRead=0;
            //循环从缓存流中读取数据
            while((hasRead=bis.read(b))>0){
                //向缓存流中写入数据，读取多少写入多少
                bos.write(b,0,hasRead);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            bis.close();
            bos.close();
        }
    }
}
1234567891011121314151617181920212223242526272829303132
```

可以看到使用字节缓存流读取和写入数据的方式和文件流（FileInputStream,FileOutputStream）并没有什么不同，只是把处理流套接到文件流上进行读写。缓存流的原理下节介绍。

> 上面代码中我们使用了缓存流和文件流，但是我们只关闭了缓存流。这个需要注意一下，当我们使用处理流套接到节点流上的使用的时候，只需要关闭最上层的处理就可以了。java会自动帮我们关闭下层的节点流。

##### 3.  转换流的使用（InputStreamReader/OutputStreamWriter）:

   下面以获取键盘输入为例来介绍转换流的用法。java使用System.in代表输入。即键盘输入，但这个标准输入流是InputStream类的实例，使用不太方便，而且键盘输入内容都是文本内容，所以可以使用InputStreamReader将其包装成BufferedReader,利用BufferedReader的readLine()方法可以一次读取一行内容，如下代码所示:

```
public class InputStreamReaderTest {
    public  static void main(String[] args)throws IOException {
        try {
            // 将System.in对象转化为Reader对象
            InputStreamReader reader=new InputStreamReader(System.in);
            //将普通的Reader包装成BufferedReader
            BufferedReader bufferedReader=new BufferedReader(reader);
           String buffer=null;
           while ((buffer=bufferedReader.readLine())!=null){
            // 如果读取到的字符串为“exit”,则程序退出
               if(buffer.equals("exit")){
                   System.exit(1);
               }
               //打印读取的内容
               System.out.print("输入内容："+buffer);
           }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
        }
    }
}
1234567891011121314151617181920212223
```

上面程序将System.in包装成BufferedReader,BufferedReader流具有缓存功能，它可以一次读取一行文本——以换行符为标志，如果它没有读到换行符，则程序堵塞。等到读到换行符为止。运行上面程序可以发现这个特征，当我们在控制台执行输入时，只有按下回车键，程序才会打印出刚刚输入的内容。

##### 4. 对象流的使用（ObjectInputStream/ObjectOutputStream）的使用：

写入对象：

```
    public static void writeObject(){
        OutputStream outputStream=null;
        BufferedOutputStream buf=null;
        ObjectOutputStream obj=null;
        try {
            //序列化文件輸出流
            outputStream=new FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\myfile.tmp");
            //构建缓冲流
            buf=new BufferedOutputStream(outputStream);
            //构建字符输出的对象流
            obj=new ObjectOutputStream(buf);
            //序列化数据写入
            obj.writeObject(new Person("A", 21));//Person对象
            //关闭流
            obj.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }12345678910111213141516171819202122
```

读取对象：

```
    /**
     * 读取对象
     */
    public static void readObject() throws IOException {
        try {
            InputStream inputStream=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\myfile.tmp");
            //构建缓冲流
            BufferedInputStream buf=new BufferedInputStream(inputStream);
            //构建字符输入的对象流
            ObjectInputStream obj=new ObjectInputStream(buf);
            Person tempPerson=(Person)obj.readObject();
            System.out.println("Person对象为："+tempPerson);
            //关闭流
            obj.close();
            buf.close();
            inputStream.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }123456789101112131415161718192021222324
```

   使用对象流的一些注意事项 
1.读取顺序和写入顺序一定要一致，不然会读取出错。 
2.在对象属性前面加transient关键字，则该对象的属性不会被序列化。

[GitHub](https://github.com/thornshell)

