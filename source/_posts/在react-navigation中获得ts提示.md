---
title: 在react-navigation中获得ts提示
date: 2022-05-09 09:23:21
tags:
  - RN
---

react-navigation 中使用 ts 类型提示

<!--more-->

## 定义全局 IRoute

在项目根目录中`typings.d.ts`中全局定义

```ts
declare global {
  // ...
  interface IRoute<T> {
    /** 路由参数 */
    params: T;
    /** 路由key */
    key: string;
    /** 路由名称 */
    name: string;
  }
}
```

## 编写路由参数定义

例如需要参数的路由名`myProfile`和不需要参数的路由名`main`，则在`@/declarations/navigation.ts`中编写如下

```ts
import type { StackNavigationProp } from "@react-navigation/stack";

// 这里导出了给myProfile路由组件获取参数使用
export type TMyProfileParam = {
  name: string;
  age: number;
};

// 这里面列出所有的路由和它所需要的参数，key必须和路由名保持一致
type TStackParamList = {
  // 路由main
  main: undefined;
  // 路由myProfile
  myProfile: TMyProfileParam;
};

// 这里导出给自定义hooks使用
export type TNavigationProp = StackNavigationProp<TStackParamList>;
```

## 编写自定义 hooks

新建`@/hooks/useAppNavigation.ts`进行导出

```ts
// 导入官方的hook
import { useNavigation } from "@react-navigation/native";

// 导入路由参数的定义
import type { TNavigationProp } from "@/declarations/navigation";

export default function useAppNavigation() {
  // 导出
  return useNavigation<TNavigationProp>();
}
```

## 编写`myProfile`页面

在`@/pages/myProfile/MyProfile.tsx`中，需要使用`useRoute`接收参数，此时可以手动从`@/declarations/navigation.ts`中导入刚才编写的路由定义，并用全局定义的`IRoute`进行包装

```ts
import React from "react";
import { View } from "react-native";
import { useRoute } from "@react-navigation/native";

import { TAParam } from "@/declarations/navigation";

export default function MyProfile(): JSX.Element {
  // 这里的params就是传过来的参数
  const { params } = useRoute<IRoute<TAParam>>();
  return <View />;
}
```

## 在需要跳转到`myProfile`的页面中获得 ts 提示

例如从`main`跳转到`myProfile`中，只要跳转的`navigation`使用`useAppNavigation`获取即可

```ts
import React from "react";
import { View } from "react-native";

import useAppNavigation from "@/hooks/useAppNavigation";

export default function Main(): JSX.Element {
  const navigation = useAppNavigation();
  // 这里第一个参数和第二个参数都会获得提示
  navigation.navigate("myProfile", {
    age: 12,
    name: "",
  });
  return <View />;
}
```
