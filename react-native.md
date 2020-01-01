[TOC]

## React Native

### 基础

#### 创建新项目

```sh
# 需要vpn
react-native init AwesomeProject

# 运行应用
react-native run-android
```

#### 基本组件

```jsx
<View></View>	// 相当于div

// 一般图片 require里面必须是静态字符串
<Image source={require('./images/mio.jpg')} />

// 网络图片 需要指定宽高
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />

// base64编码的图片
let pic = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==';
<Image style={{ width: 51, height: 51, }} source={{ uri: pic, }} />

// 背景图片
<ImageBackground
          source={require('./images/mio.jpg')}
          style={styles.img1}>
</ImageBackground>

<Text></Text>	// 显示文本
<TextInput onChangeText={}, onSubmitEditing={}></TextInput>		// 处理输入
// 例如：
<TextInput
    placeholder="Type here to show your message"
    //onChangeText={text => this.setState({ textContent: text })}
    onSubmitEditing={event =>
    	this.setState({textContent: event.nativeEvent.text})
	}
    //value={this.state.textContent}
/>

// 弹框
Alert.alert('You kick me!!!');

// 按钮
<Button
    onPress={() => {
        Alert.alert('You kick me!!!');
    }}
    title="Press"
/>

// 定义样式
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  buttonContainer: {
    margin: 20,
  },
  alternativeLayoutButtonContainer: {
    margin: 20,
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
});

// 滚动视图
<ScrollView horizontal pagingEnabled>
	<View></View>
</ScrollView>

// 长列表
<View style={styles.container}>
    <FlatList
        data={[
            {key: 'Devin'},
            {key: 'Dan'},
            {key: 'Dominic'},
            {key: 'Jackson'},
            {key: 'James'},
            {key: 'Joel'},
            {key: 'John'},
            {key: 'Jillian'},
            {key: 'Jimmy'},
            {key: 'Julie'},
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
        />
</View>

// 带分组标签的长列表
<View style={styles.container}>
    <SectionList
        sections={[
            {title: 'D', data: ['Devin', 'Dan', 'Dominic']},
            {title: 'J', data: ['Jackson', 'James', 'Jillian', 'Jimmy', 'Joel', 'John', 'Julie']},
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
        renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}
        keyExtractor={(item, index) => index}
        />
</View>
```



### 属性

#### flex 弹性宽高

- 使组件扩张，以撑满父组件的剩余空间

- flex 值越大，所占空间越大

- 例如：

  ```jsx
  export default class HelloWorldApp extends Component {
    render() {
      return (
        <View style={{height: 300, width: 100}}>
          <View style={{flex: 1, backgroundColor: 'red'}} />
          <View style={{flex: 2, backgroundColor: 'yellow'}} />
          <View style={{flex: 3, backgroundColor: 'green'}} />
        </View>
      );
    }
  }
  ```



### FlexBox布局

#### flexDirection属性

- 决定主轴的方向，默认是 column，表示从列方向（竖直轴）上排列

  ```jsx
  export default class HelloWorldApp extends Component {
    render() {
      return (
        <View style={{height: 200, width: 300, flexDirection: 'row'}}>
          <View style={{flex: 1, backgroundColor: 'red'}} />
          <View style={{flex: 2, backgroundColor: 'yellow'}} />
          <View style={{flex: 3, backgroundColor: 'skyblue'}} />
        </View>
      );
    }
  }
  ```

#### justifyContent 属性

- 决定其子元素沿**主轴**的分布方式，例如：

  ```jsx
  export default class HelloWorldApp extends Component {
    render() {
      return (
        <View style={{flex: 1, flexDirection: 'row', justifyContent: 'space-between'}}>
          <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
          <View style={{width: 50, height: 50, backgroundColor: 'skyblue'}} />
          <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
        </View>
      );
    }
  }
  ```

#### alignItems 属性

- 决定其子元素沿**次轴**的分布方式，例如：

  ```jsx
  export default class HelloWorldApp extends Component {
    render() {
      return (
        <View style={{ flex: 1, flexDirection: 'column', 
  		justifyContent: 'center', alignItems: 'stretch',
          }}>
          <View style={{height: 50, backgroundColor: 'powderblue'}} />
          <View style={{height: 50, backgroundColor: 'skyblue'}} />
          <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
        </View>
      );
    }
  }
  ```

  

### 高德地图

- 使用 react-native-amap3d

- 安装

  ```sh
  npm i react-native-amap3d
  ```

- 项目配置

  - Android
  
    ```sh
    react-native link react-native-amap3d
    ```
  
- 添加高德 API

  - 首先要获得[高德 API](https://lbs.amap.com/api/android-sdk/guide/create-project/get-key)

    - 需要生成Android签名证书，用于获得发布版 SHA1

      ```sh
      keytool -genkey -alias android.keystore -keyalg RSA -validity 36500 -keystore android.keystore
      // 会在当前目录生成一个android.keystore文件
      ```

  - Android 添加 API：在 AndroidManifest.xml 文件中加入

    ```xml
    <application>
      <meta-data
        android:name="com.amap.api.v2.apikey"
        android:value="你的高德 Key" />
    </application>
    ```

- 使用地图

  ```jsx
  import {MapView} from 'react-native-amap3d';
  export default class SectionListBasics extends Component {
    render() {
      return (
        <MapView
          coordinate={{
            latitude: 39.91095,
            longitude: 116.37296,
          }}
          style={StyleSheet.absoluteFill}		// 需要指定宽高
        />
      );
    }
  }
  ```

### 获取权限

#### 获取 Android 权限

- 在 AndroidManifest.xml 中加入

  ```xml
  // 位置权限
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
  
  // 网络权限
  <uses-permission android:name="android.permission.INTERNET" />
  ```

  







