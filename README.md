# Android Location 位置服务演示

## 简介

本 Demo 演示 Android 位置服务的权限申请。

## 基本原理

使用 LocationManager 或 FusedLocationProviderClient 获取位置。

## 教程

1. 声明权限
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

2. 动态申请权限
```kotlin
if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) 
    != PackageManager.PERMISSION_GRANTED) {
    requestPermissions(arrayOf(Manifest.permission.ACCESS_FINE_LOCATION), 100)
}
```
