## 新的组件

### TextInput

是一个允许用户输入文本的[基础组件](https://reactnative.cn/docs/intro-react-native-components)。

- **`onChangeText`**：接收一个函数，在文本变化时调用
- **`onSubmitEditing`**：文本提交后调用
- 拥有一些常规API，如`onFocus | onBlur | onChange`
- `onChageText`更像是原生`React`的`onChange`，`RN`中的`onChange`回调函数参数为一个对象`{nativeEvent: eventCount,target,text}`

### `Touchable`系列组件

- **`onPress`**：类似于`React`的`onClick`
- **`onLongPress`**：长按

### ScrollView

- 默认为水平滚动，设置`horizontal`可变为垂直滚动
- 因为会将其内部所有的元素渲染，所以只适用于展示为数不多的元素，更长的滚动列表请使用`FlatList`

### FlatList

- **data**：数据源
- **renderItem**：渲染方式
- 只渲染可视区域的元素
- 类似于`Ant Design`中的`List`

```react
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
```

### StyleSheet

提供`React`中类似`module.css`的功能，但语法有所不同

不用写复杂的选择器了，并且与`module.css`类似会为模块生成`hash`避免样式覆盖

```react
import React from "react";
import { StyleSheet, Text, View } from "react-native";

const App = () => (
  <View style={styles.container}>
    <Text style={styles.title}>React Native</Text>
  </View>
);
// 
const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 24,
    backgroundColor: "#eaeaea"
  },
  title: {
    marginTop: 16,
    paddingVertical: 8,
    borderWidth: 4,
    borderColor: "#20232a",
    borderRadius: 6,
    backgroundColor: "#61dafb",
    color: "#20232a",
    textAlign: "center",
    fontSize: 30,
    fontWeight: "bold"
  }
});

export default App;

```

### more

https://reactnative.cn/docs/components-and-apis

## API

### Platform

```react
// 检测系统
import {Platform} from 'react-native';

console.log(Platform.OS);	// ios || android

// 检测版本
Platform.Version
// ios返回一个'10.3'这样的字符串
// android返回一个数字
```

### react-navigation

`react-navigation`主要包括三个组件：

- **`StackNavigator` **导航组件：用于不同页面的跳转
- **`TabNavigator` **切换组件：用于同一页面不同模块的跳转
- **`DrawerNavigator`**抽屉组件：表现侧滑的抽屉效果

- 配置项参考https://juejin.im/post/59716e0ef265da6c2211c86e 或 https://reactnavigation.org/docs

### 引入图片

```react
// 静态资源
<Image source={require('./my-icon.png')} />
// 注意跨平台开发时引用APP中资源时不包括路径,资源放在asset | drawable文件夹下
<Image source={{uri: 'app_icon'}} style={{width: 40, height: 40}} />

// 网络图片
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />
// 甚至可以配置，以及可使用base64格式(data:image/png;base64,some hash)
<Image
  source={{
    uri: 'https://facebook.github.io/react/logo-og.png',
    method: 'POST',
    headers: {
      Pragma: 'no-cache',
    },
    body: 'Your Body goes here',
  }}
  style={{width: 400, height: 400}}
/>

// 背景图片
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
```

### 动画

https://reactnative.cn/docs/animations

### 性能优化

https://reactnative.cn/docs/performance

### 手势

https://reactnative.cn/docs/gesture-responder-system

### `State | Props`泛型

您可以为React Component的[Props](https://reactnative.cn/docs/props) and [State](https://reactnative.cn/docs/state)提供一个接口，通过`React.Component<Props, State>`该接口，当在JSX中使用该组件时，将提供类型检查和编辑器自动完成功能。

```tsx
interface State {
	name: string,
}
  
interface Props {
  sex: boolean,
}  

class XXX extends React.Component<Props,State> {}
```

