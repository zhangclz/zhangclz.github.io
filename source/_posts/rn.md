---
title: rn
date: 2025-02-13 10:41:58
tags: 语法
categories: RN
description : react native的基本用法
---

#### 此工程仅适用于公司app项目，技术通用

工程采用react native + redux7.0 + react-navigation + react-native-paper

**运行编译步骤：**

1. 使用android studio打开项目中的android文件夹，进行编译
2. 下载adb调试工具，手机连接电脑，然后在命令提示符中输入`adb devices`检查已连接的设备，确保只连接了一个设备。然后输入`adb reverse tcp:8081 tcp:8081`
3. 在android studio中点击运行，将app安装并运行到手机上
4. 在vscode中运行 `npm run start`启动项目

此时在vscode中修改代码并保存，就会实时更新到手机上，在终端输入r，即可刷新app

#### 开发

**RN基本使用：**

原生组件：

* `<View>`---`<div>`(不可滚动，不支持点击事件)
* `<Text>`---`<p>`
* `<Image>`---`<img>`，source指定图片，url可加载在线图片和base64，require加载本地图片
* `<ScrollView>`---`<div>`(可滚动的容器)
* `FlatList`，可滚动容器，优先渲染视图中的元素，data是数据源，renderItem是渲染规则
* `<TextInput>`---`<input type='text'>`，输入事件是onChangeText
* `<Button>`---`<button>`，点击事件是onPress
* `<TouchableHighlight>`，点击时可设置透明度动画效果

使用时需要导入：

```javascript
import React from 'react';
import { Text, Image, FlatList } from 'react-native';

const Cat = () => {
  return (
    <Text>Hello, I am your cat!</Text>
    <Image
       style={styles.tinyLogo}
       source={require('@expo/snack-static/react-native-logo.png')}
     />
     <Image
       style={styles.tinyLogo}
       source={{
         uri: 'https://reactnative.dev/img/tiny_logo.png',
       }}
     />
     <FlatList data={[
        {
          name: 1
        },
        {
          name: 2
        },
      ]}
      renderItem={({item})=><Text>{item.name}</Text>}
    />
  );
}

export default Cat;
```

样式：

在RN中宽高是无单位的，表示的是与设备像素密度无关的逻辑像素点表示

使用`StyleSheet`创建样式表

```javascript
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

const LotsOfStyles = () => {
    return (
      <View style={styles.container}>
        <Text style={styles.red}>just red</Text>
      </View>
    );
};

const styles = StyleSheet.create({
  container: {
    marginTop: 50,
  },
  red: {
    color: 'red',
  },
});

export default LotsOfStyles;
```

**react-navigation路由基本使用**

* 整个程序需要包裹在`NavigationContainer`组件中，并使用`createNativeStackNavigator`创建本机堆栈

```javascript
import * as React from 'react';
import { View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

function HomeScreen() {
  return(
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Home Screen</Text>
    </View>
  )
}

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

`Stack.Screen`代表每一个页面
包裹的每个组件的props中都会接收到一个`navigation`对象，使用`navigation.navigate`即可进行页面之间的跳转
`navigation.push`也可实现跳转，但这个方法可以重复添加路由，上面的方法不能重复添加
`navigation.replace`用新的页面替换当前页面
`navigation.goBack()`可以返回上一个页面

* 路由传参
  `navigation.navigate('RouteName', { /* params go here */ })`
  读取参数：`route.params`，route是组件props中的参数
