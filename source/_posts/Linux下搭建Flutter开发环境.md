---
title: Linux下搭建Flutter开发环境
date: 2020-03-22 12:04:31
tags:
	- Flutter
---

# 认识Flutter

Flutter是谷歌的移原生动UI框架，可以快速在IOS和Android上构建高质量的原生用户界面。Flutter可以与现有的代码一起工作。在全世界，Flutter正在被越来越多的开发者和组织使用，并且Flutter是完全免费、开源的。

github：https://github.com/flutter/flutter.git

## 主流框架对比

- Cordova：基于网页技术进行包装，利用插件的形式开发移动应用，性能和体验不太好。
- RN（React Native）：将View编译成原生HTML5高很多，但是它也有效率问题，RN的渲染机制是基于前端框架的考虑，复杂的UI渲染是需要依赖多个view叠加。比如：我们渲染一个复杂的ListView，每一个小的控件都是一个native的view，然后相互组合叠加，如果此时我们的list需要滑动刷新，将会有很多个对象需要渲染。
- Flutter：吸收了前两者的教训之后，在渲染技术上选择自己实现GDI，由于有更好的可控性，使用了新的语言Dart，避免了RN的那种通过桥接器与JavaScript通讯导致效率低下的问题，所以在性能方面比RN更高一筹。有经验的开发者可以打开Android手机开发者选项里面的显示边界布局，发现Flutter的布局是一个整体，说明Flutter没有使用原生控件进行渲染。

##  优点

- 跨平台
- 原生用户界面
- 开源免费
- 120fps超高性能：Flutter采用GPU渲染技术，所以性能极高。RN的性能只能达到60fps。

# 搭建开发环境

## 下载Flutter SDK

地址：https://flutter.dev/docs/get-started/install/linux

## 配置环境变量

```shell
sudo vim /etc/profile

export FLUTTER_HOME=/opt/flutter
export PATH=$FLUTTER_HOME/bin:$PATH

source /etc/profile
```

## 安装Android Studio

下载地址：http://www.android-studio.org/

- 打开Android Studio下载Android SDK

![img](https://flutter.dev/assets/get-started/android/android-sdk-tools-7a3eaa161678e404dc0c570cc0f4921944a3413586bad47b5e1f585ddfefada0.png)

一定要确保下载了上图所示的Tools

- 安装Flutter插件
- 安装AVD虚拟机

## 检查开发环境

```shell
flutter doctor --android-licenses //允许协议（android-licenses）
```

## Pub源配置

```shell
sudo vim /etc/profile

export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

source /etc/profile
```

# VSCode玩转Flutter

- 安装Flutter插件

- 开启虚拟机

  ```shell
  $HOME/Android/Sdk/emulator/emulator -netdelay none -netspeed full -avd 虚拟机名称
  ```

  - -netdelay none：设置模拟器的网络延迟时间，默认为none，就是没有延迟。
  - -netspeed full：设置网络加速值，full代表全速。

- 开启预览

  ```shell
  flutter run
  ```

  