## Android 性能优化之 内存泄漏

### 1.什么是内存泄漏

内存不在GC的掌控之内了，当一个对象已经不需要再使用了，本该被回收时，而有另外一个正在使用的对象持有它的引用从而就导致对象不能被回收。这种导致了本该被回收的对象不能被回收而停留在堆内存中，就产生了内存泄漏

GC回收机制：某对象不再有任何的引用的时候才会被回收

### 2.内存分配的集中策略

#### 1.静态的

静态的存储区：内存在程序编译的时候就已经分配好，这块的内存在程序的整个编译期间都一直存在
它主要存放静态数据、全局的static和一些常量

#### 2.栈

在执行函数（方法）时，函数一些内部变量的存储都可以放在栈上面创建，函数执行结束的时候这些存储单元就会被自动释放掉
栈内存运算速度很快，因为内置在处理器里面，但是容量有限

#### 3.堆

也叫动态内存分配，有的时候可以用malloc(注解1)或者new来申请分配一个内存
在C/C++可能需要自己负责释放（java里面直接依赖GC机制），java在这一块貌似程序员没有很好的方法自己去解决垃圾内存，需要的是编程的时候就要注意自己良好的编程习惯
注解1：malloc函数是一种分配长度为num_bytes字节的内存块的函数，可以向系统申请分配指定size个字节的内存空间。malloc的全称是memory allocation，中文叫动态内存分配，当无法知道内存具体位置的时候，想要绑定真正的内存空间，就需要用到动态的分配内存

- 区别：
1.
> 堆是不连续的内存区域，堆空间比较灵活也特别大

> 栈式一块连续的内存区域，大小是有操作系统觉决定的

2.
> 堆管理比较麻烦，频繁地new/remove会造成大量的内存碎片，这样就会慢慢导致效率低下

> 对于栈的话，他先进后出，进出完全不会产生碎片，运行效率高且稳定

3.
> 成员变量全部存储在堆中（包括基本数据类型，引用及引用对象实体）---因为他们属于类，类对象最终还是要被new出来的；

> 局部变量的基本数据类型和引用数据类型存储在栈中，引用的对象实体存储在堆中 ---因为他们属于方法中的变量，生命周期会随着方法一起结束

我们所讨论内存泄露，主要讨论堆内存，他存放的就是引用指向的对象实体。

有时候确实会有一种情况：当需要的时候可以访问，当不需要的时候可以被回收也可以被暂时保存以备重复使用。

比如：ListView或者GridView、REcyclerView加载大量数据或者图片的时候，
	图片非常占用内存，一定要管理好内存，不然很容易内存溢出。
	滑出去的图片就回收，节省内存。看ListView的源码----回收对象，还会重用ConvertView。
	如果用户反复滑动或者下面还有同样的图片，就会造成多次重复IO（很耗时），
	那么需要缓存---平衡好内存大小和IO，算法和一些特殊的java类。
	算法：lrucache(最近最少使用先回收)
	特殊的java类：利于回收，StrongReference，SoftReference，WeakReference，PhatomReference
		
StrongReference强引用
	回收时机：从不回收 使用：对象的一般保存  生命周期：JVM停止的时候才会终止
  
SoftReference，软引用
	回收时机：当内存不足的时候；使用：SoftReference<String>结合ReferenceQueue构造有效期短；生命周期：内存不足时终止
  
WeakReference，弱引用
	回收时机：在垃圾回收的时候；使用：同软引用； 生命周期：GC后终止
  
PhatomReference 虚引用
	回收时机：在垃圾回收的时候；使用：合ReferenceQueue来跟踪对象呗垃圾回收期回收的活动； 生命周期:GC后终止

开发时，为了防止内存溢出，处理一些比较占用内存大并且生命周期长的对象的时候，可以尽量使用软引用和弱引用。
软引用比LRU算法更加任性，回收量是比较大的，你无法控制回收哪些对象。

内存泄漏的例子：

- 静态变量引起的内存泄露
	当调用getInstance时，如果传入的context是Activity的context。只要这个单利没有被释放，那么这个
	Activity也不会被释放一直到进程退出才会释放
	```xml
	public class CommUtil {
	    private static CommUtil instance;
	    private Context context;
	    private CommUtil(Context context){
		this.context = context;
	    }

	    public static CommUtil getInstance(Context mcontext){
		if(instance == null){
		    instance = new CommUtil(mcontext);
		}
		return instance;
	    }
	```
	单例模式导致内存对象无法释放而导致内存泄漏 —— 传入的上下文引用生命周期与单例的声明周期不一样，用Application的context就用Application的
	
- 非静态内部类引起内存泄露
	(包括匿名内部类)
	错误的示范：
	```xml
	    public void loadData(){//隐士持有MainActivity实例。MainActivity.this.a
		new Thread(new Runnable() {
		    @Override
		    public void run() {
			while(true){
			    try {
				//int b=a;
				Thread.sleep(1000);
			    } catch (InterruptedException e) {
				e.printStackTrace();
			    }
			}
		    }
		}).start();
	    }
	```
	解决方案：
	将非静态内部类修改为静态内部类。
	（静态内部类不会隐士持有外部类）

- 不需要用的监听未移除会发生内存泄露
	例子1：
        tv.setOnClickListener();//监听执行完回收对象
        //add监听，放到集合里面
        tv.getViewTreeObserver().addOnWindowFocusChangeListener(new ViewTreeObserver.OnWindowFocusChangeListener() {
            @Override
            public void onWindowFocusChanged(boolean b) {
                //监听view的加载，view加载出来的时候，计算他的宽高等。
                //计算完后，一定要移除这个监听
                tv.getViewTreeObserver().removeOnWindowFocusChangeListener(this);
            }
        });

	例子2：
	SensorManager sensorManager = getSystemService(SENSOR_SERVICE);
        Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ALL);
        sensorManager.registerListener(this,sensor,SensorManager.SENSOR_DELAY_FASTEST);
        //不需要用的时候记得移除监听
        sensorManager.unregisterListener(listener);
	
- 资源未关闭引起的内存泄露情况
	比如：BroadCastReceiver、Cursor、Bitmap、IO流、自定义属性attribute attr.recycle()回收
	当不需要使用的时候，要记得及时释放资源。否则就会内存泄露

- 无限循环动画
	没有在onDestroy中停止动画，否则Activity就会变成泄露对象。
	比如：轮播图效果

优化两个情况：
	1.主动；平时
	2.被动，很卡的时候  出现问题的时候。

如果我们不知道代码内存泄露的情况，如何判断我们的项目里面有哪些是有内存泄露情况的？

凭借工具结合自己的经验来判断。往往我们的app在某个时候或者某个操作以后会出现很卡的现象。

看(读代码和猜)他们的生命周期是否一致(可以通过快照对比)，如果生命周期一致了肯定不是元凶

## Thank you [动脑学院](https://www.dongnaoedu.com/)

[GitHub](https://github.com/CoderGuoy/Coder)

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:coderguoy@gmail.com
