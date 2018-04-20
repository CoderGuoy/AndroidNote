# Android开发规范之性能篇

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
由于该方法是在主线程执行，如果执行耗时操作会导致UI不流畅，可以使用IntentService、创建HandlerThread或者调用Context#registerReceiver(BroadcastReceiver，IntentFilter，String，Handler)方法等方式，在
其他Wroker线程执行onReceive方法。BroadcastReceiver#onReceive()方法耗时超过10秒钟，可能会被系统杀死。
正例：
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







