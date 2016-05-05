```
스터디 진행 : 2016년 5월 7일, 양소현
최초 작성자 : 양소현
최초 작성일 : 2016년 5월 5일
마지막 수정 : 2016년 5월 6일, 양소현
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

**XML파일로 view group 만들기 (view 구성)**

```XML


```
**만든 view group을 화면에 붙이기**
```JAVA
```

##3. Runnable 객체 사용
```JAVA

```
**arrayList 객체를 따로 만들어서 data 관리**
```JAVA

```

**listitem에 대한 class 생성**
```JAVA

```
**adaptor에 item설정해주기**
```JAVA

```        


```JAVA
 
```
**ListItem type으로 ArrayList를 만들고 ListItem객체를 만들어 ListItem 속에 정의되어 있는 data를 화면에 보여주기**
```JAVA


```

```JAVA

```


##4. Looper 사용


선택위젯은 보통 데이터가 많아 스크롤할 때 뷰를 재활용해야 버벅거림 발생을 방지할 수 있다.
즉, 한번 만들어진 뷰가 화면 상태에 그대로 다시 보일 수 있도록 한다.
이것은 이미 만들어진 뷰들을 그대로 사용하면서 데이터만 바꾸어 보여주는 방식으로, convertview(현재 인덱스에 해당하는 뷰 객체)를 이용한다.

```JAVA

 
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
        android:id="@+id/progressBar"/>

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
class BackgroundTask extends AsyncTask<Integer, Integer, Integer>{


onPreExcute(){
value = 0;
progressBar.setProgress(value);
}

onPostExcute(){
value = 0;
progressBar.setProgress(value);
textView.setText("중지됨“);
}
onProgressUpdate(){

progressBar.setProgress(values[0].intValue());
textView.setText("진행중 : “ +value.toString());
}

doInBackground(){

while(!isCancelled())
{
 value++;
if(value>=100){
break;
}else{
publishProgress(value)}
}
try{
Thread.sleep(200);/0.2초
}catch(Exception e){
}
return value;

}



```
**Button의 Listener 설정해 주기**
```JAVA

  start_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                task= new BackgroundTask();
                task.excute(100);//진행상황 100까지 진행?
            }
            
```
```JAVA

 end_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            task.cancel(true);
                
            }
        }


```

