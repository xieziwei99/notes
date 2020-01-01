[TOC]

## 基本要点

1. 单位 dp：density-independent pixels，独立密度像素
2. 对于按钮，一般要求宽和高不低于 48 dp
3. 用单位 sp 来指定字体大小



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

   

