---
title: Travis CI 自动化部署 Cordova 工程
date: 2018-10-10 22:50:34
categories: 软件开发
tags: [travis-ci, cordava, android]
reward: true
---
<img src="https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/others/26.png" width="45%" height="45%">

前些时间心血来潮，开发了一款针对武汉大学图书馆预约系统的桌面端抢座软件(使用 Electron + Vue 搭建)，发布之后，有很多同学向我反映说想要 Android 版本的。

虽然利用 Cordova/PhoneGap 将网页打包为 Android 安装包并不是一件很复杂的事，但在笔记本上安装 Android 的开发环境实在让人崩溃。

但出于对用户需求的尊重，我还是开始了将网页打包为 Apk 的挖坑之旅，项目链接：[https://github.com/CS-Tao/whu-library-seat-mobile](https://github.com/CS-Tao/whu-library-seat-mobile)。

本文用于记录在本项目中利用 Travis CI 持续集成和部署的配置代码。
<!-- more -->

### .travis-ci.yml 源码

>注意：请在 Travis CI 的网站中设置`$GH_TOKEN`环境变量并设置为不可见(默认便是不可见)，该环境变量是 GitHub 的`Personal access token`。

```yml
sudo: true

language: node_js
node_js:
  - "8"

cache:
  directories:
  - node_modules

addons:
  apt:
    sources:
    - ubuntu-toolchain-r-test
    packages:
    - g++-4.8
    - openjdk-7-jdk
    - lib32stdc++6
    - lib32z1
env:
  CXX=g++-4.8

before_script:
  - wget -q https://dl.google.com/android/android-sdk_r24.4.1-linux.tgz
  - tar -xf android-sdk_r24.4.1-linux.tgz
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter platform-tools
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter build-tools-26.0.2
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter android-27
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter extra-android-support
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter extra-android-m2repository
  - echo y | ./android-sdk-linux/tools/android update sdk --no-ui --all --filter extra-google-m2repository
  - export ANDROID_HOME=$PWD/android-sdk-linux
  - export PATH=${PATH}:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$ANDROID_HOME/build-tools/26.0.2

script:
  - export TRAVIS_TAG=v1.0.0
  - npm install -g cordova
  - npm install
  - rm -rf www
  - git clone --depth=1 -b gh-pages https://github.com/CS-Tao/whu-library-seat-mobile.git www
  - cordova platform add android
  - cordova prepare
  - cordova build
  - mv ./platforms/android/app/build/outputs/apk/debug/app-debug.apk whu-library-seat-mobile_${TRAVIS_TAG}.apk
  # - cordova build android --release -- --keystore="release-key.keystore" --alias=whu-library-seat-mobile --storePassword=$STORE_PASSWORD --password=$PASSWORD
  # - zipalign -v 4 ./platforms/android/app/build/outputs/apk/release/app-release.apk whu-library-seat-mobile_${TRAVIS_TAG}.apk

deploy:
  provider: releases
  skip-cleanup: true
  overwrite: true
  api_key: $GH_TOKEN
  file: 
    - "whu-library-seat-mobile_${TRAVIS_TAG}.apk"
  on:
    tags: true
```
>代码中的`git clone`用于下载网站的源代码，也可以直接将网站提前放入`www`文件夹中。注释部分用于生成签名版本的`Apk`，需要提前生成`release-key.keystore`文件放在项目中(生成该文件的命令如下)。上文中的`$STORE_PASSWORD`和`$PASSWORD`环境变量为下面这条命令需要输入的两个密码，也应该添加到 Travis CI 的网站中，并设置为不可见。

```bash
# 生成 release-key.keystore，别名为 whu-library-seat-mobile
keytool -genkey -v -keystore release-key.keystore -alias whu-library-seat-mobile -keyalg RSA -keysize 2048 -validity 10000
```
附源文件链接：[https://github.com/CS-Tao/whu-library-seat-mobile/blob/master/.travis.yml](https://github.com/CS-Tao/whu-library-seat-mobile/blob/master/.travis.yml)
