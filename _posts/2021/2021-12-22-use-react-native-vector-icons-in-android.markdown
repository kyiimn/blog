---
title:  "[React Native] 안드로이드에서 react-native-vector-icons 사용하기"
date:   2021-12-22 21:30:34 +0900
category:
 - react-native
tag: 
 - android
 - react-native
 - react-native-vector-icons
---
## 설치
```
$ yarn add react-native-vector-icons
```

## 안드로이드 설정
android/app/build.gradle 에 다음을 추가 
```
apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```