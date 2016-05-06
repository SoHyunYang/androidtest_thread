```
스터디 진행 : 2016년 5월 7일, 양소현
최초 작성자 : 양소현
최초 작성일 : 2016년 5월 5일
마지막 수정 : 2016년 5월 7일, 양소현
```

# Thread & Handler & Looper
여러개의 item을 중에 하나를 선택할 수 있는 위젯

ex) 리스트뷰, 그리드뷰, 스피너, 갤러리

**Thread**

**Handler**

직접 위젯에 원본데이터를 설정할 수 없다.-> 데이터 설정 및 관리를 하기 위해 어댑터를 사용

어댑터에서 만들어주는 뷰를 이용하여(getView()method 사용) 리스트뷰의 아이템을 보여준다. 

**Looper**
![selectionwidget.JPG](https://github.com/SoHyunYang/androidstudy_test/blob/master/selectionwidget.JPG?,raw=true)
# List View

##1. java의 Thread 사용 
![listView1.JPG](https://github.com/SoHyunYang/androidstudy_test/blob/master/listView1.JPG?,raw=true)

**Button 생성**
```XML
    <ListView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/listView"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true" />
```
**Thread 생성**

```JAVA
MainActivity

private static final String TAG = "MainActivity"


void onButtonClicked(View v){
Log.d(TAG,"첫번째 버튼 클릭됨.“);

RequestThread thread = new RequestThread();
thread.start();

}
class RequestThread extends Thread{

public void run(){
for(int I = 0; i<100; I++){
println("#"+i + " : 호출됨.“);

try{
Thread.sleep(500);
}catch(Exception e){
e.printStackTrace();
}
}
}
public void println(String data){
Log.d(TAG,data);

}


}


```



##2. Handler 사용
![listView2.JPG](https://github.com/SoHyunYang/androidstudy_test/blob/master/listView2.JPG?,raw=true)

**XML파일로 textView 만들기 **

```XML


```
**textView inflation**
```JAVA
MainActivity

private static final String TAG = "MainActivity"
TextView textView;

onCreate(){

textView = (TextView)findViewById(R.id.textView);
}

```

**handler 사용하지 않았을 시 오류**

```JAVA
void onButtonClicked(View v){
Log.d(TAG,"첫번째 버튼 클릭됨.“);

textView.setText(“스레드 시작함”);

RequestThread thread = new RequestThread();
thread.start();

}

```

```JAVA
public void println(String data){
Log.d(TAG,data);
TextView.setText(data);// 오류


```
**handler class정의 후 handler 객체 생성**
```JAVA
class ResponseHandler extends Handler{
handlerMessage(){

}
}


```
```JAVA
MainActivity

private static final String TAG = "MainActivity"
TextView textView;
2. //Handler 객체 생성
ResponseHandler handler = new ResponseHandler();

```

**handler를 통해 message전달**
```JAVA
public void println(String data){
Log.d(TAG,data);
//TextView.setText(data);// 오류

Message message = handler.obtainMessage();
Bundle bundle = new Bundle();
bundle.putString("data", data);
message.setData(bundle);

handler.sendMessage(message);

}

```

```JAVA
class ResponseHandler extends Handler{
handlerMessage(){
 Bundle bundle= msg.getData();
String data = bundle.getString("data");

textView.setText(data);
}
}


```


##3. Runnable 객체 사용
http://micropilot.tistory.com/1994참고

**Handler 객체 생성**
```JAVA
//ResposeHandler Class 지우고

//MAinactivity 내에 정의

Handler handler = new Handler();

```

**Thread 내에서 사용될 println 함수 내에서 Runnable객체 사용**
```JAVA
prinltln(final String data){ //final로 바꿔주기 data접근하려고

Log.d(TAG, data);

handler.post(new Runnable(){

public void run(){
TextView.setText(data);
}


});
}

```



##4. Looper 사용

http://blog.naver.com/elder815/220533768581참고

선택위젯은 보통 데이터가 많아 스크롤할 때 뷰를 재활용해야 버벅거림 발생을 방지할 수 있다.
즉, 한번 만들어진 뷰가 화면 상태에 그대로 다시 보일 수 있도록 한다.
이것은 이미 만들어진 뷰들을 그대로 사용하면서 데이터만 바꾸어 보여주는 방식으로, convertview(현재 인덱스에 해당하는 뷰 객체)를 이용한다.

**xml파일로 View 생성**

```XML
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="kr.co.mash_up.asynctask_example.MainActivity"
    android:orientation="vertical">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="mainthread에서 받은 data"
       />

   <EditText
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:id="@+id/main_text"
       />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Backgroundthread에서 받은 data"
            android:id="@+id/textView"/>

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:id="@+id/back_text"
            />
    </LinearLayout>
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="시작"
        android:id="@+id/start_btn"
        >

    </Button>
</LinearLayout>



```

**View inflation**
```JAVA
public class MainActivity extends AppCompatActivity {
    
    Button start_btn;
    EditText main_text;//mainthread에서 받은 문자열
    EditText back_text;//mainthread에서 보내져 backthread에서 받은 문자열
    
    

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        main_text =(EditText)findViewById(R.id.main_text);
        back_text =(EditText)findViewById(R.id.back_text);
        start_btn=(Button)findViewById(R.id.start_btn);
        
          start_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                
            }
        });
    }
}
```

**Mainthread에서 text받아와서 Backgroundthread 객체에 넘겨주기**
```JAVA
public class MainActivity extends AppCompatActivity {

    Button start_btn;
    EditText main_text;//mainthread에서 받은 문자열
    EditText back_text;//mainthread에서 보내져 backthread에서 받은 문자열

    BackgroundThread backgroundThread;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        main_text =(EditText)findViewById(R.id.main_text);
        back_text =(EditText)findViewById(R.id.back_text);
        start_btn=(Button)findViewById(R.id.start_btn);

      backgroundThread = new BackgroundThread();
      
 	start_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                String inStr = main_text.getText().toString();
                Message msgToSend = Message.obtain();
                msgToSend.obj = inStr;
                backgroundThread.handler.sendMessage(msgToSend);
            
            }
        });

        backgroundThread.start();
               
    }

	class BackgroundThread extends Thread{}
}

  
```

**background thread Class & Handler 정의**


```JAVA
  class BackgroundThread extends Thread{
        BackgroundHandler handler;
        public BackgroundThread() {
            handler= new BackgroundHandler();
        }
        
        public void run(){
            Looper.prepare();
            Looper.loop();
        }
    }
    
    class BackgroundHandler extends Handler {

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            Message resultMsg = Message.obtain();
            resultMsg.obj = "Hello"+msg.obj ;
            
            mainHandler.sendMessage(resultMsg);
        }
    }

```

```JAVA
public class MainActivity extends AppCompatActivity {

    Button start_btn;
    EditText main_text;//mainthread에서 받은 문자열
    EditText back_text;//mainthread에서 보내져 backthread에서 받은 문자열

    BackgroundThread backgroundThread;
    MainHandler mainHandler; //mainHandler객체 정의
```

**Mainthread Handler 정의**

```JAVA

   protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        main_text =(EditText)findViewById(R.id.main_text);
        back_text =(EditText)findViewById(R.id.back_text);
        start_btn=(Button)findViewById(R.id.start_btn);
        mainHandler= new MainHandler();//mainhandler 객체 생성
```

```JAVA
 class MainHandler extends Handler{
        @Override
        public void handleMessage(Message msg){
            String str=(String)msg.obj;
            back_text.setText(str);

        }
    }

```

# AsyncTask
테이블 형태와 유사하게 격자 모양으로 아이템 배치를 해주는 위젯

![gridview.JPG](https://github.com/SoHyunYang/androidstudy_test/blob/master/gridview.JPG?,raw=true)

**xml파일로 View 생성**
```XML
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="kr.co.mash_up.asynctask_example.MainActivity"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="전송상태"
        android:id="@+id/textView"/>

    <ProgressBar
        android:layout_width="match_parent"
        android:layout_height="30dp"
        android:id="@+id/progressBar"
        style="?android:attr/progressBarStyleHorizontal"

    />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="시작"
        android:id="@+id/start_btn"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="중지"
        android:id="@+id/end_btn"/>

</LinearLayout>
   
```
**View inflation**

```JAVA
public class MainActivity extends AppCompatActivity {

    TextView textView;
    ProgressBar progressBar;
    Button start_btn;
    Button end_btn;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        textView=(TextView)findViewById(R.id.textView);
        progressBar=(ProgressBar)findViewById(R.id.progressBar);
        start_btn=(Button)findViewById(R.id.start_btn);
        
        start_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                
            }
        });
        end_btn=(Button)findViewById(R.id.end_btn);
        
        end_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                
            }
        });
        
        
    }
}
```
**inner class로 BackgroundThread 생성**
```JAVA
int value =0 ; //변수설정

```
```JAVA
  class BackgroundTask extends AsyncTask<Integer, Integer, Integer> {

        @Override
        protected void onPreExecute() {

            value = 0;
            progressBar.setProgress(value);
        }

        @Override
        protected void onPostExecute(Integer integer) {

            value = 0;
            progressBar.setProgress(value);
            textView.setText("끝남");
        }

        @Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);


            progressBar.setProgress(values[0].intValue());
            textView.setText("진행중 : " + values[0].toString());

        }

        @Override
        protected Integer doInBackground(Integer... params) {

            while(!isCancelled()) {
                value++;
                if (value >= 100) {
                    break;
                } else {
                    publishProgress(value);
                }

                try {
                    Thread.sleep(200);//0.2초
                } catch (Exception e) {
                }

            }

            return value;
        }
    }

```
**Button의 Listener 설정해 주기**
```JAVA

    start_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                task = new BackgroundTask();
                task.execute(100);

            }
        });
        end_btn=(Button)findViewById(R.id.end_btn);

        end_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                task.cancel(true);
                textView.setText("취소됨");
            }
        });

```

###참고문헌###
Do it! 안드로이드 앱 프로그래밍
