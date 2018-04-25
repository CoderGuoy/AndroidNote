# Android开发规范之强制篇（阿里巴巴Android开发手册）

## Android基本组件

### 1.Activity间的数据通信，对于数据量比较大的，避免使用Intent+Parcelable的方式，可以考虑EventBus等代替方案，避免造成TransactionTooLargeException

### 2.Activity间通过隐式Intent的跳转，在发出Intent之前必须通过resolveActivity检查，避免找不到合适的调用组件，造成ActivityNotFoundException的异常
正例：
```java
  public void viewUrl(String url,String mimeType){
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setDataAndType(Uri.parse(url),mimeType);
    if(getPackageManager().resolveActivity(intent,PackageManager.MATCH_DEFAULT_ONLY)!=null){
      try{
        startActivity(intent);
        }catch(ActivityNotFoundException e){
          if(Config.LOGD){
          Log.d(LOGTAG,"activity notfound for"+ mimeType +"over" +Uri.parse(url).getScheme(),e);
          }
        }
      }
    } 
```
反例：
```java
  Intent intent = new Intent();
  intent.setAction("com.great.activity_intent.Intent_Demo1_Result3");
```

### 3.避免在Service#onStartCommand()/onBind()方法中执行耗时操作，如果确实有需求，应改用IntentService或采用其它异步机制完成
正例：
```java
  public class MainActivity extends Activity
    {
      @Override
      public void onCreate(Bundle savedInstanceState)
        {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.main);
        }
        
      public void startIntentService(View source)
        {
        Intent intent = new Intent(this,MyIntentService.class);
        startService(intent);
        }
        
      public class MyIntentService extends IntentService
        {
          public MyintentService()
            {
              super("MyintentService");
            }
            
          @Override
          protected void onHandleIntent(Intent intent)
            {
              synchronized(this){
                try{
                  ...
                }catch(Exception e){
                }
              }
            }
         }
```

### 4.避免在BroadcastReceiver#onReceive()中执行好使操作，如果有耗时工作，应该创建IntentService完成，而不应该在BroadcastReceiver内创建子线程去做
#### 说明：
#### 由于该方法是在主线程执行，如果执行耗时操作会导致UI不流畅，可以使用IntentService、创建HandlerThread或者调用Context#registerReceiver(BroadcastReceiver，IntentFilter，String，Handler)方法等方式，在其他Wroker线程执行onReceive方法。BroadcastReceiver#onReceive()方法耗时超过10秒钟，可能会被系统杀死。
正例：
```java
  IntentFilter filter = new IntentFilter();
  filteer.addAction(LOGIN_SUCCESS);
  this.registerReceiver(mBroadcastReceiver,filter);
  mBroadcastReceiver = new BroadcastReceiver(){
    @Override
    public void onReceive(Context context, Intent intent){
      Intent userHomeIntent = new Intent();
      UserHomeIntent.setClass(this,UseHomeActivity.class);
      this.startActivity(userHomeIntent);
    }
  };
```
反例：
```java
  mBroadcastReceiver = new BroadcastReceiver(){
    @Override
    public void onReceive(Context context,Intent intent){
      MyDatabaseHelper myDB = new MyDatabaseHelper(context);
      myDB.initData();
      //have more database operation here
    }
  };
```

### 5.避免使用隐私Intent广播敏感信息，信息可能被其他注册了对应BroadcastReceiver的APP接收
#### 说明：
#### 通过Context#sendBroadcase()发送的隐式广播会被所感兴趣的receiver接收，恶意应用注册监听该广播的receiver可能会获取到Intent中传递的敏感信息，并进行其他危险操作。如果发送的广播为使用Context#sendOrderedBroadcast()方法发送的有序广播，优先级较高的恶意receiver可能直接丢弃改广播，造成服务不可用，或者向广播结束塞入恶意数据。
#### 如果广播仅限应用内，则可以使用LocalBroadcastManager#sendBroadcast()实现，避免敏感信息外泄和Intent拦截的风险
正例：
```java
Intent intent = new intent("my-sensitive-event");
intent.putExtra("event","this is a test event");
LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
```
反例：
```java
Intent intent = new Intent();
v1.setAction("com.sample.action.server_running");
v1.putExtra("local_ip",v0.h);
v1.putExtra("port",v0.i);
v1.putExtra("code",V0.g);
v1.putExtra("connected",V0.s);
v1.putExtra("pwd_preddfined",v0.r);
if(!Textutils.isEmpty(v0.t)){
  v1.putExtra("connected_usr",v0.t);
}
context.sendBroadcast(v1);
```
##### 以上广播可能被其他应用的如下receiver接收导致敏感信息泄露
```java
  final class MyReceiver extends BroadcastReceiver{
    public final void onReceive(Context context,Intent intent){
      if(intent!=null&&intent.getAction()!=null){
        String s = intent.getAction();
        if(s.equals("com.sample.action.server_running"){
          String ip = intent.getStringExtra("local_ip");
          String pwd = intent.getStringExtra("code");
          String port = intent.getIntExtra("port",8888);
          boolean status = intent.getBooleanExtra("connected",false);
        }
      }
    }
  }
```

### 6.不要在Android的Application对象中缓存数据。基础组件之间的数据共享使用Intent等机制，也可以使用SharedPreferences等数据持久化机制
反例：
```java
  class MyApplication extends Application{
    String username;
    String getUsername(){
      return username;
    }
    void setUsername(String username){
      this.username = username;
    }
  }
  class SetUsernameActivity extends Activty{
    void onCreate(Bundle savedInstanceState){
      super.onCreate(savedInstanceState);
      setContentView(R.layout.set_username);
      MyApplication app = (MyApplication)getApplication();
      app.setUsername("tester1");
      startActivity(new Intent(this,GetUsernameActivity.calss));
    {
  }
  class GetUsernameActivity extends Activity{
    TextView tv;
    void onCreate(Bundle savedInstanceState){
      supter.onCreate(savedInstanceState);
      setContentView(R.layout.set_username);
      tv = (TextView)findViewById(R.id.username);
    }
    void onResume(){
      supter.onResume();
      MyApplication app = (MyApplication)getApplication();
      tv.setText("Welcome back!" + app.getUsername().tuUpperCase());
    }
  }
```

### 7.使用Adapter的时候，如果你使用了ViewHolder做缓存，在getView()的方法中无论这项convertView的每个子控件是否需要设置属性（比如某个TextView设置的文本可能为null，某个按钮的背景色为透明，某控件的颜色为透明等），都需要为其显式设置属性（Textview的文本为空也需要设置setText("")，背景透明也需要设置），否则在滑动的过程中，因为adapter item 复用的原因，会出现内容的显式错乱。
正例：
```java
@Override
public View getView(int position,View convertView,ViewGroup parent){
  ViewHolder myViews;
  if(convertView == null){
    myViews  = new ViewHolder();
    convertView = mInflater.inflate(R.layout.list_item,null);
    myViews.mUsername = (TextView)convertView.findViewById(R.id.username);
    convertView.setTag(myViews);
  }else{
    myViews = (ViewHolder)convertView.getTag();
  }
  Info p = infoList.get(position);
  String dn = p.getDisplayName;
  myViews.mUsername.setText(StringUtils.isEmpty(dn)?"":dn);
  return convertView;
 }
 static class ViewHolder{
  private TextView mUsername；
 }
```

### 8.Activity或Fragment中动态注册BroadCastReceiver时，registerReceiver()和unregisterReceiver()要成对出现
#### 说明：
#### 如果registerReceiver()和unregisterReceiver()不成对出现，则可能导致已经注册的receiver没有在合适的时机注销，导致内存泄漏，占用内存控件，加重SystemService负担
#### 部分华为的机型会对receiver进行资源管控，单个应用注册过多receiver会触发管控模块报出异常，应用直接崩溃
正例：
```java
public class MainActivity extends AppCompatActivity{
    private static MyReceiver myReceiver = new MyReceiver();
    ...
    @Override
    protected void onResume(){
      super.onResume();
      IntentFilter filter = new InterntFilteer("com.example.myservice");
      registerReceiver(myReceiver,filter);
    }
    @Override
    protected void onPause(){
      super.onPause();
      unregisterReceiveer(myReceiver);
    }
    ...
}
```
反例：
```java
public class MainActivity extends AppCompatActivity{
  private static MyReceiver myReceiver;
  @Override
  protected void onResume(){
    super.onResume();
    myReceiver = new MyReceiver();
    IntentFilter filter = new IntentFilteer("com.example.myservice");
    registerReceiver(myReceiver,filter);
  }
  @Override
  protected void onDestory(){
    super.onDestroy();
    unregisterReceiver(myReceiver);
  }
}
```

## UI与布局
### 1.布局中不得不适用ViewGroup多嵌套时，不要使用LinearLayout嵌套，改用RelativeLayout，可以有效降低嵌套数
#### 说明：
#### Android应用页面上任何一个VIew都需要经过measure、layout、draw三个步骤才能被正确的渲染。从xml layout 的顶部节点开始进行measure，每个子节点都需要向自己的父节点提供自己的尺寸来决定展示的位置，在此过程中可能还会重新measure（由此可能导致measure的时间消耗为原来的2-3倍）。节点所处位置越深，嵌套带来的measure越多，计算就会越费时。这就是为什么扁平的VIew结构性能会更好
#### 同时，页面上的View越多，measure、layout、draw所花费的时间就越久。要缩短这个时间，关键是保持View的树形结构尽量扁平，而且要移除所有不需要渲染的View。理想情况下，总共的measure，layout，draw时间应该被很好的控制在16ms以内，以保证滑动屏幕时UI的流畅。
#### 要找到那些多余的View（增加渲染延迟的view），可以用Android Studio Monitor里的Hierarachy Viewer工具，可视化查看所有的view
正例:
```java
  <?xml version="1.0" encoding="utf-8"?>
  <android.support.constraint.ConstraintLayout>
    <RelativeLayout>
    <TextView/>
    ...
    <ImageView/>
    </RelativeLayout》
  </android.support.constraint.ConstraintLayou>
```
反例：
```java
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout>
    <LinearLayout>
      <RelativeLayout>
      <TextView/>
      ...
      <ImageView/>
      </RelativeLayout>
    </LinearLayout>
  </LinearLayout>
```
多重嵌套导致measure以及layout等步骤耗时过多

### 2.禁止在非ui线程进行view相关操作

### 3.禁止在设计布局时多次设置子view和父view中间为同样的背景造成页面过度绘制，推荐将不需要显示的布局进行即时隐藏。
正例：
```java
  <?xml version="1.0" encoding="utf-8"?>
   <Linear xmlns:android="http://schemas.android.com/apk/res/android"
     android:layout_width="fill_parent"
     android:layout_height="fill_parent"
     android:orientation="vertical">
  <TextView
     android:layout_width="fill_parent"
     android:layout_height="wrap_content"
     android:text="@String/hello"/>
  <Button
     android:layout_width="fill_parent"
     android:layout_height="wrap_content"
     android:text="click it!"
     android:id="@+id/btn_mybutton"/>
  <ImageView
     android:id="@+id/img"
     android:layout_width="fill_parent"
     android:layout_height="wrap_content"
     android:visibility="gone"
     android:src="@drawable/youttube"/>
  <TextView
     android:text="it is an example!"
     android:layout_width="fill_parent"
     android:layout_height="wrap_content"/>
  </LinearLayout>
```
反例：
```java
  @Override
  protected void onDraw(Canvas canvas){
    super.onDraw(canvas);
    int width = getWidth();
    int height = getHeight();
    mPaint.setColor(Color.GRAY);
    canvas.drawRect(0,0,withd,height,mPaint);
    mPaint.setColor(Color.CYAN);
    canvas.drawRect(0,height/4,withd,height,mPaint);
    mPaint.setColor(Color.DKGRAY);
    canvas.drawRect(0,height/3,withd,height,mPaint);
    mPaint.setColor(Color.LTGRAY);
    canvas.drawRect(0,height/2,withd,height,mPaint);
  }
```

### 4.不能使用ScrollView包裹ListVIew/GridView/ExpandableListView;因为这样会把ListView的所有Item都加在到内存中，要消耗巨大的内存和CPU去绘制图面
#### 说明：
#### ScrollView中件套List或RecyclerView的做法官方明确禁止。除了开发过程中遇到的各种视觉和交互问题，这种做法对性能也有较大的损耗。ListView等UI组件自身有垂直滚动的功能，也没必要在嵌套一层ScrollView。目前为了较好的UI体验，更贴近Material Design 的设计，推荐使用NestedScrollView
正例：
```java
  <?xml version="1.0" encoding="utf-8"?>
  <Linearayout>
    <android.support.v4.widget.NestedScrollView>
      <LinearLayout>
        <ImageView/>
        ...
        <android.support.7.widget.RecyclerView/>
      </LinearLayout>
    </android.support.v4.widget.NestedScrollView>
  </LinearLayout>
```
反例：
```java
  <ScrollView>
    <LinearLayout>
      <TextView/>
      ...
      <ListView/>
      <TextView/>
    </LinearLayout>
  </ScrollView>
```

## 进程、线程与消息通信
### 1.不要通过Intent在Android基础组件之间传递大数据（binder transaction缓存为1MB）,可能导致OOM
### 2.在Application的业务初始化代码加入进程判断，确保只在自己需要的进程初始化，特别是后台进程减少不必要的业务初始化
正例：
```java
  public class MyApplication extends Application{
    @Override
    public void onCreate(){
      //在所有进程中初始化
      ...
      //仅在主进程中初始化
      if(mainProcess){
        ...
      }
      //仅在后台进程中初始化
      if(bgProcess){
        ...
      }
    }
  }
 ```
### 3.新建线程时，必须通过线程池提供（AsyncTask或者ThreadPoolExecutor或者其他形式自定义的线程池），不允许在应用中自行显示创建线程
#### 说明：
#### 使用线程池的好处就是减少在创建和销毁线程上所花的时间以及系统资源的开销，解决资源不足的问题。如果不适用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过渡切换”的问题。另外创建匿名线程不便于后续的资源使用分析，对性能分析等会造成困扰
正例：
```java
  int UNMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
  int KEEP_ALIVE_TIME = 1;
  TimeUnit KEEP_ALIVE_TIME_UNIT = TimeUnit.SECONDS;
  BlockingQueue<Runnable> taskQueue = new LinkedBlockingQueue<Runnable>();
  ExecutorService executorService = new TreadPoolExecutor(NUMBER_OF_CORES,NUMBER_OF_CORES*2,
  KEEP_ALIVE_TIME,KEEP_ALIVE_TIME_UNIT,taskQueue,new BackgroundThredFactory(),new DefaultRejectedExecutionHandler());
  //执行任务
  executorService.execute(new Runnnable(){
    ...
  });
```
反例：
```java
  new Thread(new Runnable(){
    @Override
    public void run(){
      //操作语句
      ...
    }
  }).start();
```

### 4.线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
#### 说明：
#### Executors返回的线程池对象的弊端如下：
#### 1）FixedThreadPool 和 SingleThreadPool：允许的请求队列长度为Integer.MAX_VALUE,可能会堆积大量的请求，从而导致OOM；
#### 2）CachedThreadPool 和 ScheduledThreadPool:允许的创建线程数量为Integer.MAX_VALUE,可能会创建大量的线程，从而导致OOM；
正例：
```java
  int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();
  int KEEP_ALIVE_TIME = 1;
  TimeUnit KEEP_ALIVE_TIME_UNIT = TimeUnit.SECONDS;
  BlockingQueue<Runnable> taskQueue = new LinkedBlockingQueue<Runnable>();
  ExecutorService executorService = new ThreadPoolExecutor(NUMBER_OF_CORES,NUMBER_OF_CORES*2,
  KEEP_ALIVE_TIME,KEEP_ALIVE_TIME_UNIT,taskQueue,new BackgroundThreadFactory(),new DefaultRejectedExecutionHandler());
```
反例：
```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
```

### 5.子线程中不能更新界面，更新界面必须在主线程中进行，网络操作不能再主线程中调用
### 6.不要再非UI线程中初始化ViewStub，否则会返回null

## 文件与数据库
### 1.任何时候不要硬编码文件路径，请使用Android文件系统API访问
#### 说明：
#### Android 应用提供内部和外部存储，分别用于存放应用自身数据以及应用产生的用户数据。可以通过相关API接口获取对应的目录，进行文件操作
android.os.Environment#getExternalStorageDirectory()
android.os.Environment#getExternalStroagePublicDirectory()
android.content.Context#getFilesDir()
android.content.Context#getCacheDir()
正例：
```java
  public File getDir(String alName){
    File file = new File(Envieonment.getExternalStoragePublicDirectory(
      Environment.DIRECTORY_PICTURES),alNmae);
    if(!file.mkdirs()){
      Log.e(LOG_TAG,"Directory not created");
    }
    return file;
  }
```
反例：
```java
  public File getDir(String alName){
    //任何时候都不要硬编码文件路径，这不仅存在安全隐患，也让app更容易出现适配问题
    File file = new File("/mnt/sdcard/Download/Album",alName);
    if(!file.mkdirs()){
      Log.e(LOG_TAG,"Directory not created");
    }
    return file;
  }
```

### 2.当时用外部存储时，必须检查外部存储的可用性
正例：
```java
  //读/写检查
  public boolean isExternalStorageState();
    String state = Environment.getExternalStorageState();
    if(Environment.MEDIA_MOUNTED.equals(state)){
     return true;
    }
    return false;
  }
  //只读检查
  public boolean isExternalStorageReadable(){
    String state = Environment.getExternalStorageState();
    if(Environment.MEDIA_MOUNTED.equals(state)||Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)){
      return true;
    }
   return false;
  }
```

### 3.应用间共享文件时，不要通过放宽文件系统权限的方式去实现，而应适用FileProvider
正例：
```java
<!--AndroidManifest.xml-->
  <manifest>
    ...
    <application>
      ...
      <provider
        android:name="android.support.v4.content.FileProvider"
        android:authorities="com.example.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resourse="@xml/provider_paths"/>
      </provider>
      ...
    </manifest>
    <!--res/xml/provider_paths.xml-->
    <paths>
      <files-path path="album/" name="myimages"/>
    </paths>
    void getAlbumImage(String imagePath){
      File image = new File(iamgePath);
      Intent getAlbumImageIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
      Uri imageUri = FileProvider.getUriForFile(this,"com.example.provider",iamge);
      getAlbumImageIntent.putExtra(MediaStore.EXTRA_OUTPUT,inmageUri);
      startActivityForResult(takePhotoIntent,REQUEST_GET_ALBUMIMAGE);
    }
```
反例：
```java
  void getAlbumImage(String iamgePaht){
    File image = new File(iamgePaht);
    Intent getAlbumImageIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    //不要使用 file:// 的URI分享文件给别的应用，包括但不限于Intent
    getAlbumImageIntent.putExtra(MediaStore.EXTRA_OUTPUT,Uri.fromFile(iamge));
    startActivityForResult(takePhotoIntent,RESQUEST_GET_ALBUMIMAGE);
  }
```

### 4.数据库Cursor必须确保使用完后关闭，避免内存泄漏
#### 说明：
#### Cursor是对数据库查询结果集管理的一个类，当查询的结果集较小时，消耗内存不易察觉。但是当结果集较大，长时间重复操作会导致内存消耗过大，需要开发者操作完成后手动关闭Cursor。
#### 数据库Cursor在创建及使用时，可能发生各种异常，无论程序是否正常结束，必须在最后确保Cursor正确关闭，以避免内存泄漏。同时，如果Cursor的使用还牵涉多线程场景，那么需要自行保证操作同步
正例：
```java
  public void handlePhotos(SQLiteDatabase db,String userId){
    Cursor cursor;
    try{
      cursor = db.query(TUserPhoto,new String[]{"userId","content"},"userId=?",new String[]{userId),null,null,null);
      while(cursor.moveToNext()){
        //TODO
      }
    }catch(Exception e){
      //TODO
    }finally{
      if(cursor!=null){
        cursor.close();
      }
    }
  }
```
反例：
```java
  public void handlePhotos(SQLiteDatabase db,String userId){
    Cursor cursor = db.query(TUserPhoto,new String[] {"userId","content"},"userId=?",new String[]{userId},null,null,null);
    while(cursor.moveToNext()){
      //TODO
    }
    //不能放任cursor不关闭
```

### 5.多线程操作写入数据库时，需要使用事物，以免出现同步问题
#### 说明：
#### Android通过SQLiteOpenHelper获取数据库SQLiteDatabase实例，Helper中会自动缓存已经打开的SQLiteDatabase实例，单个APP中应使用SQliteOpenHelper的单例模式确保数据库连接唯一。由于SQLite自身是数据库级锁，单个数据库操作是保证线程安全的（不能同时写入），transaction时一次原子操作，因此处于事务中的操作是线程安全的
#### 若同时打开多个数据库连接，并通过多线程写入数据库，会导致数据库异常，提示数据库已被锁住
正例：
```java
  public void insertUserPhoto(SQliteDatabase db,String userId,String content){
    ContentValues cv = new ContentValues();
    cv.put("userId",userId);
    cv.put("content",content);
    db.beginTransaction();
    try{
      db.insert(TUserPhoto,null,cv);
      //其他操作
      db.setTransactionSuccessful();
    }catch(Exception e){
      //TODO
    }finally{
      db.endTransaction();
    }
 }
```
反例：
```java
  public void insertUserPhoto(SQLiteDatabase db,String userId,String content){
    ContentValues cv = new ContentValues();
    cv.put("userId",userId);
    cv.put("content",content);
    db.insert(TUserPhoto,null,cv);
  }
```

### 6.执行SQL语句时，应使用SQLiteDatabase#insert()、update()、delete()，不要使用SQLiteDatabase#execSQL(),以免SQL注入风险
正例：
```java
  public int updataUserPhoto(SQLiteDatabse db,String userId,String content){
    ContentValues cv = new ContentValues();
    cv.put("content",content);
    String[] args = {String.ValueOf(userId)};
    return db.update(TuserPhoto,cv,"userId=?",args);
  }
```
反例：
```java
  public int updataUserPhoto(SQLiteDatabse db,String userId,String content){
    String sqlStmt = String.format("UPDATE % s set content=%s WHERE =%s",TUserPhoto,userId,content);
    //请提高安全意识，不要直接执行字符串作为SQL语句
    db.execSQl(sqlStmt);
  }
```

### 7.如果ContentProvider管理的数据存储在SQL数据库中，应该避免将不受信任的外部数据直接拼接在原始SQL语句中，可使用一个用于将？作为可替换参数的选择子句以及一个单独的选择参数数组，会避免SQL注入
正例：
```java
  //使用一个可替换参数
  String mSelectionClause= "var = ?";
  String[] selectionArgs = {""};
  selectionArgs[0] = mUserInput;
```
反例：
```java
  //拼接用户输入内容和列名
  String mSelectionClause = "var = " +mUserInput;
```

## Bitmap、Drawable与动画
### 1.加载大图片或者一次性加载多张图片，应该在异步线程中进行。图片的加载，涉及到IO操作，以及CPU密集操作，很可能引起卡顿
正例：
```java
   class BitmapWorkerTask extends AsyncTask<Integer,Void,Bitmap>{
    ...
    //在后台进行图片解码
    @Override
    portected Bitmap doInBackgrund(Integer...params){
      final Bitmap bitmap = BitmapFactory.decodeFile("some paht");
      return bitmap;
    }
    ...
  }
```
反例：
```java
  Button btnLoadImage = (Button)findViewById(R.id.btn);
  btnLoadImage.setOnClickListener(new OnClickListener(){
    public void onClick(View v){
      Bitmap bitmap = BitmapFactory.decodeFile("some path");
    }
  });
```

### 2.在ListView，VIewPager，RecyclerView,GridView等组件中使用图片时，应做好图片的缓存，避免始终持有图片导致内存泄漏，也避免重复创建图片，引起性能问题。建议使用Fresco、Glide
正例：
```java
  例如使用系统LruCache缓存
   LruCache<String,Bitmap>mMemoryCache;
   @Override
   protected void onCreate(Bundle savedState){
    ...
    //获取可用内存的最大值，使用内存超出这个值将抛出OutOfMemory异常。LruCache通过构造函数传入缓存值，以KB为单位。
    final int maxMemory = (int)(Runtime.getRuntime().maxMemory()/1024);
    //把最大可用内存的1/8作为缓存控件
    final int cacheSize = maxMemory/8;
    mMemoryCache = new LruCache<String,Bitmap>(cacheSize){
      @Override
      protected int sizeOf(String key,Bitmap bitmap){
        return bitmap.getByteCount()/1024;
      }
    };
    ...
  }
  public void addBitmapToMemoryCache(String key,Bitmap bitmap){
    if(getBitmapFromMermCache(key)==null){
      mMemoryCache.put(key,bitmap);
    }
  }
  public Bitmap getBitmapFromMemCache(String key){
    return mMemoryCache.get(key);
  }
  public void loadBitmap(int resId,ImageView imageview){
    final String inmageKey = String.valueOf(resId);
    final Bitmap bitmap = getBitmapFromMemCache(imageKey);
    if(bitmap!=null){
      mImageView.setImageBitmap(bitmap);
    }else{
      mImageView.setImageResource(R.drawable.image_placeholder);
      BitmapWorkerTask task = new BItmapWorkerTask(mImageView);
      task.execude(resId);
    }
 }
 class BitmapWorkerTask extends AsyncTask<Integer,Void,Bitmap>{
  ...
  //在后台进行图片解码
  @Override
  protected Bitmap doinBackground(Interger...params){
    final Bitmap bitmap = decodeSampledBitmapFromResource(getResources(),params[0],100,100));
    addBitmapToMemoryCache(String.valueOf(params[0]),bitmap);
    return bitmap:
  }
  ...
 }
```
反例：
```java
  没有存储，每次都需要解码，或者有缓存但是没有合适的淘汰机制，导致缓存效果很差，依然经常需要重新解码
```

### 3.png图片使用tinypng或者类似工具压缩，减少包体积

### 4.使用完毕的图片，应该及时回收，释放宝贵的内存
正例：
```java
  Bitmap bitmap = null;
  loadBitmapAsync(new OnResult(result){
    bitmap = result;
  });
  ...使用该bitmap...
  //使用结束，在2.3.3及以下需要调用recycle()函数，在2.3.3以上GC会自动管理，除非你明确不需要再用
  if(Build.VERSION.SDK_INT<=10){
    bitmap.recycle();
  }
  bitmap = null;
```
反例：
使用完成图片，始终不释放资源

### 5.在Activity.onPause()或Activity.onStop()回调中，关闭当前activity正在执行的动画
正例：
```java
  public class MyActivity extends Activity{
    ImageView mImageView;
    Animation mAnimation;
    Button mBtn;
  /** 首次创建activity时调用 */
    @Override
    public void onCreate(Bundle savedInstanceState){
      supter.onCreate(savedInstanceState);
      setContentView(R.layout.main);
      mImageView = (ImageView)findVIewById(R.id.ImageView01);
      mAnimation = AnimationUtils.loadAnimation(this,R.anim.anim);
      mBtn = (Button)findViewById(R.id.Button01);
      mBtn.setOnClickListener(new View.OnClickListener(){
        @Override
        public void onClick(View v){
          mImageVIew.startAnimation(mAnimation);
        }
      });
    }
    public void onPause(){
      //页面退出，及时清理动画资源
      mImageView.clearAnimation();
    }
 }
```
反例：
  页面退出时，不关闭该页面相关的动画

## 安全
### 1.使用PendingIntent时，禁止使用空intent，同时禁止使用隐式Intent
#### 说明：
#### 1）使用PendingIntent时，使用了空Intent，会导致恶意用户劫持修改Intent的内容。禁止使用一个空Intent去构造PendingIntent，构造PendingIntent的Intent一定要设置ComponentName或者action
#### 2）PendingIntent可以让其它APP中的代码像是运行自己APP中。PendingIntent的intent接收方在使用该intent时与发送方有相同的权限。在使用PendingIntent时，PendingIntent中包装的intent如果是隐式的Intent，容易遭到劫持，导致信息泄露
正例：
```java
  Intent intent = new Intent(this,SomeActivity.class);
  PendingIntent pendingIntent = PendingIntent.getActivity(this,1,intent,PendingIntent.FLAG_UPDATE_CURRENT);
  try{
    pendingIntent.send();
  }catch(PendingIntent.CanceledException e){
    e.printStackTrace();
  }
```
反例1：
```java
  Bundle addAccountOptions = new Bundle();
  mPendingIntent = PendingIntent.getBroadcast(this,0,new Intent,0);
  addAccountOptions.putParcelable(KEY_CALLER_IDENTITY,mPendingIntent);
  addAccountOptions.putBoolean(EXTRA_HAS_MULTIPLE_USERS,Utils.hasMultipleUsers(this));
  AccountManager.get(this).addAccount(accountType,null,null,addAccountOptions,null,mCallback,null);
```
反例2：
```java
  mPendingIntent是通过new Intent()构造原始Intent的，所以为“双无”Intent，这个PendingIntent最终被通过AccountManager.addAccount方法传递给了恶意APP接口
  Intent intent = new Intent("com.test.test.pushservice.action.METHOD");
  intent.addFlags(32);
  intent.putExtra("app",PendingIntent.getBroadcast(this,0,intent,0));
  如上代码PendingIntent.getBroadcast，PendingIntent中包含的Intent为隐式intent，因此当PendingIntent出发执行时，发送的intent很可能被嗅探或者劫持，导致intent内容泄漏
```

### 2.禁止使用常量初始化矢量参数构建 IvParameterSpec,建议IV通过随机方式产生
#### 说明：
#### 使用固定初始化向量，结果密码文本可预测性会高很多，容易受到字典式攻击。iv的作用主要是用于产生密文的第一个block，以使最终生成的密文产生差异（明文相同的情况下），使用密码攻击变得更为困难，除此之外iv并无其他用途。因此iv通过随机方式产生是一种十分便捷、有效的途径。
正例：
```java
  byte[] rand = new byte[16];
  SecureRandom r = new SecureRandom();
  r.nextBytes(rand);
  IvParameterSpec iv = new IvParameterSpec(rand);
```
反例：
```java
  IvParameterSpec iv = new IvParameterSpec("1234567890",getBytes());
  System.out.println(iv.getIV());
```
### 3.将android:allowbackup属性设置为flase,防止adb backup 导出数据
#### 说明：
#### 在AndroidManifest.xml文件中为了方便对程序数据的备份和恢复在Android API level 8 以后增加了android:allowbackup属性值。默认情况下这个属性值为true，故当allowBackup标志值为true时，即可通过adb backup 和 adb restore 来备份和恢复应用程序数据
正例：
```java
  <application
    android:allowBackup="false"
    android:largeHeap="true"
    android:icon="@drawable/test_launcher"
    android:lable="@string/app_name"
    android:theme="@style/AppTheme">
```

### 4.在现实的HostnameVerifier子类中，需要使用verify函数校验服务器主机名的合法性，否则会导致恶意程序利用中间人攻击绕过主机名校验。
#### 说明：
#### 在握手期间，如果RUL的主机名和服务器的标识主机名不匹配，则验证机制可以回调此接口的实现程序来确定是否应该允许此链接。如果回调内实现不恰当，默认接受所有域名，则有安全风险
反例：
```java
  HostnameVerifier hnv = new HostnameVerifier(){
  @Override
  public boolean verify(String hostname,SSLSession session){
    //总是返回true，接受任意域名服务器
    return ture;
   }
  };
  HttpsURLConnection.setDefaultHostnameVerifiler(hnv);
```
正例：
```java
  HostnameVerifier hnv = new HostnameVerifier(){
  @Override
  public boolean verify(String hostname,SSLSession session){
    //示例
    if("yourhostname",equals(hostname)){
      return true;
    }else{
      HostnameVerifier hv = HttpsURLConnection.getDefaultHostnameVerifier();
      return hv.verify(hostname,session);
    }
  }
 };
```

### 5.利用X509TrusManager子类中的checkServerTrusted函数校验服务器端证书的合法性
#### 说明：
#### 在实现的X509TrusManager子类中未对服务端的证书做检验，这样会导致不被信任的证书绕过证书校验机制
反例：
```java
  TrustManager tm = new X509TrustManager(){
    public void checkClientTrusted(X509Certificate[] chain,String authType)
    throws CeertificateException{
      //do nothing,接收任意客户端证书
    }
    public void checkServerTrusted(X509Certificate[] chain,String authType)
    throws CeertificateException{
      //do nothing,接收任意客户端证书
    }
    public X509Certificate[] getAcceptedIssuers(){
      return null;
    }
  };
  sslContext.init(null,new TrustManager[] { tm },null);
```

### 6.META-INF目录中不能包含如.apk,.odex,.so等敏感文件，该文件夹没有经过签名，容易被恶意替换

### 7.Receiver/Provider 不能在毫无权限控制的情况下，将android:export设置为true

### 8.阻止webview通过file:schema方式访问本地敏感数据

### 9.不要广播敏感信息，只能在本应用使用LocalBroadcast，避免被别的应用收到，或者setPackage做限制

### 10.不要把敏感信息打印到log中
#### 说明：
#### 在APP的开发过程中，为了我方便调试，通常会使用log函数输入一些关键流程的信息，这些信息通常会包含敏感内容，如执行流程、明文的用户名密码等，这会让攻击者更加容易的了解APP内部结构方便破解和攻击，甚至直接获取到有价值的敏感信息
反例：
```java
  String username = "log_leak";
  String password = "log_leak_pwd";
  Log.d("MY_APP","usesname"+username);
  Log.d("MY_APP","password"+password,new Throwable());
  Log.v("MY_APP","send message to server");
  以上代码使用Log.d Log.v 打印程序的执行过程的username等调试信息，日志没有关闭，攻击者可以直接从Logcat中读取这些敏感信息。所以在产品的线上版本中关闭调试接口，不要输出敏感信息。
```

### 11.对于内部使用的组件










