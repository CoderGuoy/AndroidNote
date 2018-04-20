# Android开发规范之强制篇（阿里巴巴Android开发手册）

## Android基本组件

#### 1.Activity间的数据通信，对于数据量比较大的，避免使用Intent+Parcelable的方式，可以考虑EventBus等代替方案，避免造成TransactionTooLargeException

#### 2.Activity间通过隐式Intent的跳转，在发出Intent之前必须通过resolveActivity检查，避免找不到合适的调用组件，造成ActivityNotFoundException的异常
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

#### 3.避免在Service#onStartCommand()/onBind()方法中执行耗时操作，如果确实有需求，应改用IntentService或采用其它异步机制完成
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

#### 4.避免在BroadcastReceiver#onReceive()中执行好使操作，如果有耗时工作，应该创建IntentService完成，而不应该在BroadcastReceiver内创建子线程去做
说明：
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

#### 5.避免使用隐私Intent广播敏感信息，信息可能被其他注册了对应BroadcastReceiver的APP接收
说明：
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

#### 6.不要在Android的Application对象中缓存数据。基础组件之间的数据共享使用Intent等机制，也可以使用SharedPreferences等数据持久化机制
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
```






















