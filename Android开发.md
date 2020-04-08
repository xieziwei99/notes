[TOC]

## 基本要点

1. 单位 dp：density-independent pixels，独立密度像素
2. 对于按钮，一般要求宽和高不低于 48 dp
3. 用单位 sp 来指定字体大小
4. 资源文件，尤其是图片，命名中不能包含短横线`-`，要替换成下横杠`_`

### 基本组件

#### 系统弹窗

- 普通弹窗

  ```java
  // 精简版
  public void showNormalDialog(View view) {
      new AlertDialog.Builder(this)
          .setTitle("标题")
          .setMessage("这是message")
          .create()
          .show();
  }
  
  // 较丰富版
  public void showNormalDialog(View view) {
      new AlertDialog.Builder(this)
          .setTitle("标题")
          .setIcon(R.mipmap.ic_launcher)
          .setMessage("这是message")
          .setPositiveButton("确定", new DialogInterface.OnClickListener() {
              @Override
              public void onClick(DialogInterface dialog, int which) {
                  Toast.makeText(ShowQuestionActivity.this,
                                 "后续信息提示", Toast.LENGTH_SHORT).show();
                  dialog.dismiss();
              }
          })
          .create()
          .show();
  }
  ```

  

### 关于网络的错误

1. 报错信息：CLEARTEXT communication to 101.37.172.100 not permitted by network security policy

   - 解决方法1

     - 服务器端改用 HTTPS ，而不是 HTTP

   - 解决方法2

     1. 添加文件 `src\main\res\xml\network_security_config.xml `

        ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <network-security-config>
        <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">101.37.172.100</domain>
        </domain-config>
        </network-security-config>
        ```

     2. 修改 `AndroidManifest.xml` 文件，设置属性 `android:networkSecurityConfig ` 

        ```xml
        android:networkSecurityConfig="@xml/network_security_config"
        ```

### 动态权限申请

- 例如，在 MainActivity.java 中：

  ```java
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
  
      // 申请权限：定位权限
      String[] neededPermissions = {
          Manifest.permission.ACCESS_COARSE_LOCATION,
          Manifest.permission.ACCESS_FINE_LOCATION
      };
      List<String> tempPermissions = new ArrayList<>();
      for (String p : neededPermissions) {
          if (ContextCompat.checkSelfPermission(this, p) != PackageManager.PERMISSION_GRANTED) {
              tempPermissions.add(p);
          }
      }
      if (!tempPermissions.isEmpty()) {
          ActivityCompat.requestPermissions(this, tempPermissions.toArray(new String[0]), 100);
      }
  }
  
  // 需要重载回调函数：用户对权限申请做出相应操作后执行
  @Override
  public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
      super.onRequestPermissionsResult(requestCode, permissions, grantResults);
      if (requestCode == 100) {
          for (int i = 0; i < grantResults.length; i++) {
              if (grantResults[i] != PackageManager.PERMISSION_GRANTED) {
                  Toast.makeText(this, "权限被拒绝：" + permissions[i], Toast.LENGTH_LONG).show();
              }
          }
      }
  }
  ```

### 当填写输入框后才允许点击提交按钮

- 例如

  ```java
  package com.example.wemeet;
  
  // import 。。。
  public class LoginActivity extends AppCompatActivity {
      private EditText emailInput;
      private EditText passwordInput;
      private Button loginButton;
  
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_login);
  
          // 监听输入框
          emailInput = findViewById(R.id.input_email);
          passwordInput = findViewById(R.id.input_password);
          loginButton = findViewById(R.id.button_login);
          
          EditTextChange editTextChange = new EditTextChange();
          emailInput.addTextChangedListener(editTextChange);
          passwordInput.addTextChangedListener(editTextChange);
      }
  
      // EditText监听器
      class EditTextChange implements TextWatcher {
  
          @Override
          public void beforeTextChanged(CharSequence s, int start, int count, int after) { }
  
          @Override
          public void onTextChanged(CharSequence s, int start, int before, int count) {
              boolean b = emailInput.getText().length() > 0;
              boolean b1 = passwordInput.getText().length() > 0;
              if (b && b1) {
                  loginButton.setEnabled(true);
              } else {
                  loginButton.setEnabled(false);
              }
          }
  
          @Override
          public void afterTextChanged(Editable s) { }
      }
  }
  ```




## Retrofit 使用

1. 添加Retrofit库的依赖（和Gson或其他的依赖）

   ```csharp
   dependencies {
       implementation 'com.squareup.retrofit2:retrofit:2.7.0'
       // Retrofit库
       implementation 'com.squareup.retrofit2:converter-gson:2.7.0'
     }
   ```

2. 添加网络权限

   ```xml
   <!-- 在AndroidManifest.xml文件中 -->
   <uses-permission android:name="android.permission.INTERNET"/>
   ```

3. 创建 Model 类，例如：

   ``` java
   package com.example.myfirstapp;
   import lombok.Data;
   @Data
   public class City {
       private Long id;
       private Long provinceId;
       private String cityName;
       private String description;
   }
   ```

4. 创建用于描述网络请求的接口，例如新建 interface：

   ```java
   package com.example.myfirstapp;
   import java.util.List;
   import retrofit2.Call;
   import retrofit2.http.Body;
   import retrofit2.http.GET;
   import retrofit2.http.POST;
   
   public interface CityInterface {
       @GET("city")
       Call<List<City>> getCityList();
   
       @POST("city")
       Call<Void> addCity(@Body City city);
   }
   ```

5. 接下来的代码需要在一个 Activity 中编写，例如：

   ```java
   // 在MainActivity中使用GET
   package com.example.myfirstapp;
   
   //import ...
   
   public class MainActivity extends AppCompatActivity {
   
       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
       }
   
       public void showCities(View view) {
           TextView citiesTextView = findViewById(R.id.showCitiesTextView);
   
           Retrofit retrofit = new Retrofit.Builder().baseUrl("http://101.37.172.100:8081/api/")
                   .addConverterFactory(GsonConverterFactory.create()).build();
           CityInterface request = retrofit.create(CityInterface.class);
           Call<List<City>> call = request.getCityList();
           call.enqueue(new Callback<List<City>>() {
               @Override
               public void onResponse(Call<List<City>> call, Response<List<City>> response) {
                   List<City> cities = response.body();
                   StringBuilder s = new StringBuilder();
                   if (cities != null) {
                       for (City city : cities) {
                           s.append(city.toString()).append("\n");
                       }
                   }
                   citiesTextView.setText(s);
               }
   
               @Override
               public void onFailure(Call<List<City>> call, Throwable t) {
                   citiesTextView.setText(t.getMessage());
               }
           });
       }
   
       public void addCity(View view) {
           Intent intent = new Intent(this, AddCityActivity.class);
           startActivity(intent);
       }
   }
   
   // 在AddCityActivity中使用POST
   package com.example.myfirstapp;
   
   // import ...
   
   public class AddCityActivity extends AppCompatActivity {
   
       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_add_city);
       }
   
       public void addCity(View view) {
           Long cityNo = Long.parseLong(((EditText) findViewById(R.id.cityNoEditText)).getText().toString());
           String cityName = ((EditText) findViewById(R.id.cityNameEditText)).getText().toString();
           String cityDesc = ((EditText) findViewById(R.id.cityDescEditText)).getText().toString();
           City city = new City();
           city.setProvinceId(cityNo).setCityName(cityName).setDescription(cityDesc);
   
           Retrofit retrofit = new Retrofit.Builder().baseUrl("http://101.37.172.100:8081/api/")
                   .addConverterFactory(GsonConverterFactory.create()).build();
           CityInterface request = retrofit.create(CityInterface.class);
           Call<Void> cityCall = request.addCity(city);
           cityCall.enqueue(new Callback<Void>() {
               @Override
               public void onResponse(Call<Void> call, Response<Void> response) {
                   ((TextView)(findViewById(R.id.showMessageText))).setText("增加城市成功");
               }
   
               @Override
               public void onFailure(Call<Void> call, Throwable t) {
                   ((TextView)(findViewById(R.id.showMessageText))).setText("增加城市失败");
               }
           });
       }
   }
   
   // 或者采用单例模式，编写工具类NetworkUtil
   package com.example.wemeet.util;
   
   import retrofit2.Retrofit;
   import retrofit2.converter.gson.GsonConverterFactory;
   
   public class NetworkUtil {
       private static Retrofit retrofit;
   
       public static Retrofit getRetrofit() {
           if (retrofit == null) {
               String baseUrl = "http://101.37.172.100:8080/";
               retrofit = new Retrofit.Builder().baseUrl(baseUrl)
                       .addConverterFactory(GsonConverterFactory.create()).build();
           }
           return retrofit;
       }
   }
   ```




## 高德地图

### 操作步骤

1. [地图API依赖项配置：官网](https://lbs.amap.com/api/android-sdk/guide/create-project/android-studio-create-project)

2. [Key、权限等配置：官网](https://lbs.amap.com/api/android-sdk/gettingstarted)

3. [声明周期的管理：官网](https://lbs.amap.com/api/android-sdk/guide/create-map/show-map#t3)

   1. 到这里，运行程序将看到地图

4. [定位蓝点：官网](https://lbs.amap.com/api/android-sdk/guide/create-map/mylocation#location-marker-5-0)

   1. 一般设置如下

      ```java
      @Override
          protected void onCreate(Bundle savedInstanceState) {
              super.onCreate(savedInstanceState);
              setContentView(R.layout.activity_map);
      
              mapView = findViewById(R.id.map);
              mapView.onCreate(savedInstanceState);
              AMap aMap = mapView.getMap();
              MyLocationStyle myLocationStyle = new MyLocationStyle();
              myLocationStyle.myLocationType(
                  MyLocationStyle.LOCATION_TYPE_LOCATION_ROTATE_NO_CENTER);
              myLocationStyle.interval(2000);
              // 设置精度圆圈颜色
              myLocationStyle.strokeColor(Color.argb(0, 0, 0, 0));
              myLocationStyle.radiusFillColor(Color.argb(0, 0, 0, 0));
              aMap.setMyLocationStyle(myLocationStyle);
              // 官网说非必须，但实际需要（实测）
              aMap.getUiSettings().setMyLocationButtonEnabled(true);
      	    aMap.getUiSettings().setScaleControlsEnabled(true);
              aMap.setMyLocationEnabled(true);
          }
      ```
   
   2. 注意，在上面的配置中，虽然已经在 AndroidManifest.xml 文件中申明了需要位置权限，但仍然需要手动去设置中开启（实测）
   
5. 室内地图

   ```java
   aMap.showIndoorMap(true);
   ```

6. 设置放缩级别

   ```java
   aMap.moveCamera(CameraUpdateFactory.zoomTo(18));
   ```

7. [标记点：官网](https://lbs.amap.com/api/android-sdk/guide/draw-on-map/draw-marker)

8. 定位：[官网](https://lbs.amap.com/api/android-location-sdk/guide/android-location/getlocation)

