# [面向接口编程详解-Java篇](https://www.cnblogs.com/iceb/p/7093884.html)



　　相信看到这篇文字的人已经不需要了解什么是接口了，我就不再过多的做介绍了，直接步入正题，接口测试如何编写。那么在这一篇里，我们用一个例子，让各位对这个重要的编程思想有个直观的印象。为充分考虑到初学者，所以这个例子非常简单，望各位高手见谅。

　　为了摆脱新手的概念，我这里也尽量不用main方法，而采用testNG编写测试用例。

**定义：**现在我们要开发一个应用，模拟移动存储设备的读写，即计算机与U盘、MP3、移动硬盘等设备进行数据交换。

**上下文（环境）：**已知要实现U盘、MP3播放器、移动硬盘三种移动存储设备，要求计算机能同这三种设备进行数据交换，并且以后可能会有新的第三方的移动存储设备，所以计算机必须有扩展性，能与目前未知而以后可能会出现的存储设备进行数据交换。各个存储设备间读、写的实现方法不同，U盘和移动硬盘只有这两个方法，MP3Player还有一个PlayMusic方法。

**名词定义：**数据交换={读，写}

**解决方案列举**

**方案一：**分别定义FlashDisk、MP3Player、MobileHardDisk三个类，实现各自的Read和Write方法。然后在Computer类中实例化上述三个类，为每个类分别写读、写方法。例如，为FlashDisk写ReadFromFlashDisk、WriteToFlashDisk两个方法。总共六个方法。

**方案二：**定义抽象类MobileStorage，在里面写虚方法Read和Write，三个存储设备继承此抽象类，并重写Read和Write方法。Computer类中包含一个类型为MobileStorage的成员变量，并为其编写get/set器，这样Computer中只需要两个方法：ReadData和WriteData，并通过多态性实现不同移动设备的读写。

**方案三：**与方案二基本相同，只是不定义抽象类，而是定义接口IMobileStorage，移动存储器类实现此接口。Computer中通过依赖接口IMobileStorage实现多态性。

**方案四：**定义接口IReadable和IWritable，两个接口分别只包含Read和Write，然后定义接口IMobileStorage接口继承自IReadable和IWritable，剩下的实现与方案三相同。

下面，我们来分析一下以上四种方案：

　　首先，方案一最直白，实现起来最简单，但是它有一个致命的弱点：可扩展性差。或者说，不符合“开放-关闭原则”（注：意为对扩展开放，对修改关闭）。当将来有了第三方扩展移动存储设备时，必须对Computer进行修改。这就如在一个真实的计算机上，为每一种移动存储设备实现一个不同的插口、并分别有各自的驱动程序。当有了一种新的移动存储设备后，我们就要将计算机大卸八块，然后增加一个新的插口，在编写一套针对此新设备的驱动程序。这种设计显然不可取。

　　此方案的另一个缺点在于，冗余代码多。如果有100种移动存储，那我们的Computer中岂不是要至少写200个方法，这是不能接受的！

　　再看 方案二和方案三，之所以将这两个方案放在一起讨论，是因为他们基本是一个方案（从思想层面上来说），只不过实现手段不同，一个是使用了抽象类，一个是使用了接口，而且最终达到的目的应该是一样的。

　　我们先来评价这种方案：首先它解决了代码冗余的问题，因为可以动态替换移动设备，并且都实现了共同的接口，所以不管有多少种移动设备，只要一个Read方法和一个Write方法，多态性就帮我们解决问题了。而对第一个问题，由于可以运行时动态替换，而不必将移动存储类硬编码在Computer中，所以有了新的第三方设备，完全可以替换进去运行。这就是所谓的“依赖接口，而不是依赖与具体类”，不信你看看，Computer类只有一个MobileStorage类型或IMobileStorage类型的成员变量，至于这个变量具体是什么类型，它并不知道，这取决于我们在运行时给这个变量的赋值。如此一来，Computer和移动存储器类的耦合度大大下降。

　　那么 这里该选抽象类还是接口呢？还记得第一篇文章我对抽象类和接口选择的建议吗？看动机。这里，我们的动机显然是实现多态性而不是为了代码复用，所以当然要用接口。

　　最后 我们再来看一看方案四，它和方案三很类似，只是将“可读”和“可写”两个规则分别抽象成了接口，然后让IMobileStorage再继承它们。这样做，显然进一步提高了灵活性，但是，这有没有设计过度的嫌疑呢？我的观点是：这要看具体情况。如果我们的应用中可能会出现一些类，这些类只实现读方法或只实现写方法，如只读光盘，那么这样做也是可以的。如果我们知道以后出现的东西都是能读又能写的，那这两个接口就没有必要了。其实如果将只读设备的Write方法留空或抛出异常，也可以不要这两个接口。总之一句话：理论是死的，人是活的，一切从现实需要来，防止设计不足，也要防止设计过度。

　　在这里，我们姑且认为以后的移动存储都是能读又能写的，所以我们选方案三。

 

**实现**

下面，我们要将解决方案加以实现。我选择的语言是Java，所以使用其他语言的朋友一样可以参考。

首先编写IMobileStorage接口：

Code：IMobileStorage

```
1 public interface IMobileStorage {
2 
3     void Read();                    // 读取数据
4     void Write();                   // 写入数据
5 
6 }
```

　　代码比较简单，只有两个方法，没什么好说的，接下来是三个移动存储设备的具体实现代码：

 

U盘

Code：FlashDisk

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 public class FlashDisk implements IMobileStorage{
 2     @Override
 3     public void Read() {
 4         System.out.println("Reading from FlashDisk……");
 5         System.out.println("Read finished!");
 6     }
 7 
 8     @Override
 9     public void Write() {
10         System.out.println("Writing to FlashDisk……");
11         System.out.println("Write finished!");
12     }
13 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

MP3

Code：MP3Player

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class MP3Player implements IMobileStorage{
    @Override
    public void Read() {
        System.out.println("Reading from MP3Player……");
        System.out.println("Read finished!");
    }

    @Override
    public void Write() {
        System.out.println("Writing to MP3Player……");
        System.out.println("Write finished!");
    }

    public void PlayMusic(){
        System.out.println("Music is playing……");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

移动硬盘

Code：MobileHardDisk

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class MobileHardDisk implements IMobileStorage{

    @Override
    public void Read() {
        System.out.println("Reading from MobileHardDisk……");
        System.out.println("Read finished!");
    }

    @Override
    public void Write() {
        System.out.println("Writing to MobileHardDisk……");
        System.out.println("Write finished!");
    }

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　可以看到，它们都实现了IMobileStorage接口，并重写了各自不同的Read和Write方法。下面，我们来写Computer：

Code：Computer

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class Computer {
    private IMobileStorage _usbDrive;

    public IMobileStorage get_usbDrive() {
        return _usbDrive;
    }

    public void set_usbDrive(IMobileStorage _usbDrive) {
        this._usbDrive = _usbDrive;
    }

    public Computer(){}

    public Computer(IMobileStorage _usbDrive) {
        this._usbDrive = _usbDrive;
    }

    public void ReadData(){
        this._usbDrive.Read();
    }

    public void WriteData(){
        this._usbDrive.Write();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　其中的UsbDrive就是可替换的移动存储设备，之所以用这个名字，是为了让大家觉得直观，就像我们平常使用电脑上的USB插口插拔设备一样。

OK！下面我们来[测试](http://lib.csdn.net/base/softwaretest)我们的“电脑”和“移动存储设备”是否工作正常。我是用的Java控制台程序打印结果，具体代码如下：

Code：测试代码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class ToTest {
    @Test
    public void program1(){
        Computer computer = new Computer();
        IMobileStorage mp3Player = new MP3Player();
        IMobileStorage flashDisk = new FlashDisk();
        IMobileStorage moblieHardDisk = new MobileHardDisk();

        System.out.println("I inserted my MP3 Player into my computer and copy some music to it:");
        computer.set_usbDrive(mp3Player);
        computer.WriteData();
        System.out.println("====================");

        System.out.println("Well,I also want to copy a great movie to my computer from a mobile hard disk:");
        computer.set_usbDrive(moblieHardDisk);
        computer.ReadData();
        System.out.println("====================");

        System.out.println("OK!I have to read some files from my flash disk and copy another file to it:");
        computer.set_usbDrive(flashDisk);
        computer.ReadData();
        computer.WriteData();
        System.out.println();
    }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

运行结果如下：

![图2.1 各种移动存储设备测试结果](https://images2015.cnblogs.com/blog/958154/201706/958154-20170629123618180-45483511.png)

　　　　　　　　　　　　　　图2.1 各种移动存储设备测试结果

 

　　好的，看来我们的系统工作良好。

　　**后来……**

 

　　刚过了一个星期，就有人送来了新的移动存储设备NewMobileStorage，让我测试能不能用，我微微一笑，心想这不是小菜一碟，让我们看看面向接口编程的威力吧！将测试程序修改成如下：

 （NewMobileStorage的类请参照u盘、移动硬盘等类编写……也可以自创）

测试代码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
    @Test
    public void program2(){
        Computer computer = new Computer();
        IMobileStorage newMobileStorage = new NewMoblieStorage();
        computer.set_usbDrive(newMobileStorage);
        newMobileStorage.Write();
        newMobileStorage.Read();

    }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

运行结果：

![img](https://images2015.cnblogs.com/blog/958154/201706/958154-20170629124034930-1619824093.png)

　　　　　　　　　　　　　　图2.2 新设备扩展测试结果

 

　　又过了几天，有人通知我说又有一个叫SuperStorage的移动设备要接到我们的Computer上，我心想来吧，管你是“超级存储”还是“特级存储”，我的“面向接口编程大法”把你们统统搞定。

　　但是，当设备真的送来，我傻眼了，开发这个新设备的团队没有拿到我们的IMobileStorage接口，自然也没有遵照这个约定。这个设备的读、写方法不叫Read和Write，而是叫rd和wt，这下完了……不符合接口啊，插不上。但是，不要着急，我们回到现实来找找解决的办法。我们一起想想：如果你的Computer上只有USB接口，而有人拿来一个PS/2的鼠标要插上用，你该怎么办？想起来了吧，是不是有一种叫“PS/2-USB”转换器的东西？也叫适配器，可以进行不同接口的转换。对了！程序中也有转换器。

　　这里，我要引入一个设计模式，叫“Adapter”。它的作用就如现实中的适配器一样，把接口不一致的两个插件接合起来。由于本篇不是讲设计模式的，而且Adapter设计模式很好理解，所以我就不细讲了，先来看我设计的类图吧：

　　如图所示，虽然SuperStorage没有实现IMobileStorage，但我们定义了一个实现IMobileStorage的SuperStorageAdapter，它聚合了一个SuperStorage，并将rd和wt适配为Read和Write，SuperStorageAdapter（这里注意自行编写SuperStorage的类和他用到的接口）

![img](https://images2015.cnblogs.com/blog/958154/201706/958154-20170629131737993-1282839506.png)

　　　　　　　　　　　　图2.3 Adapter模式应用示意

　　

具体代码如下：

Code：SuperStorageAdapter

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 public class SuperStorageAdapter implements IMobileStorage {
 2     private SuperStorage _superStorage;
 3 
 4     public SuperStorage get_superStorage() {
 5         return _superStorage;
 6     }
 7 
 8     public void set_superStorage(SuperStorage _superStorage) {
 9         this._superStorage = _superStorage;
10     }
11 
12     @Override
13     public void Read(){
14         this._superStorage.rd();
15     }
16 
17     @Override
18     public void Write() {
19         this._superStorage.wt();
20     }
21 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

好，现在我们来测试适配过的新设备，测试代码如下：

Code：测试代码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
  @Test
    public void program3(){
        Computer computer = new Computer();
        SuperStorageAdapter superStorageAdapter = new SuperStorageAdapter();
        SuperStorage superStorage = new SuperStorage();
        superStorageAdapter.set_superStorage(superStorage);

        System.out.println("Now,I am testing the new super storage with adapter:");
        computer.set_usbDrive(superStorageAdapter);
        computer.ReadData();
        computer.WriteData();
        System.out.println();
    }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

运行结果：

![img](https://images2015.cnblogs.com/blog/958154/201706/958154-20170629141212430-1795530180.png)

　　　　　　　　　　图2.4 利用Adapter模式运行新设备测试结果

 

 

　　OK！虽然遇到了一些困难，不过在设计模式的帮助下，我们还是在没有修改Computer任何代码的情况下实现了新设备的运行。希望各位朋友结合第一篇的理论和这个例子，仔细思考面向接口的问题。当然，不要忘了结合现实。

 

原文是C#的语言，在这里做了java的转换编写，感谢原作者，转自C站的一小平民：http://blog.csdn.net/boer521314/article/details/40378151

(原文地址)[https://www.cnblogs.com/iceb/p/7093884.html]

