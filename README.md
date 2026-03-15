# Android Location 位置服务演示

## 简介

本 Demo 演示 Android 位置服务的权限申请流程，展示如何在应用中获取位置权限。

## 基本原理

Android 位置服务允许应用获取设备的地理位置信息。主要有两种方式：

1. **LocationManager**（传统方式）
   - 直接与系统位置服务交互
   - 支持 GPS、网络等多种定位方式

2. **FusedLocationProviderClient**（推荐方式）
   - Google Play Services 提供的统一位置 API
   - 自动选择最佳定位方式（GPS/网络）

位置权限：
- `ACCESS_FINE_LOCATION`：精确位置（GPS）
- `ACCESS_COARSE_LOCATION`：模糊位置（网络）
- `ACCESS_BACKGROUND_LOCATION`：后台位置（Android 10+）

## 启动和使用

### 环境要求
- Android Studio
- JDK 17
- Gradle 8.x

### 安装和运行

1. 用 Android Studio 打开项目
2. 连接 Android 设备或模拟器
3. 点击 Run 运行

### 使用方法
- 点击"获取位置"按钮申请位置权限
- 权限结果会通过 Toast 显示

## 教程

### 什么是位置服务？

位置服务是移动应用的重要功能，用于获取设备的地理位置。常见应用场景包括：
- 地图导航
- 附近好友/商家
- 运动轨迹记录
- 天气应用

### 位置权限

Android 6.0（API 23）开始需要动态申请权限。在清单文件中声明权限后，还需要在运行时请求。

声明权限（在 AndroidManifest.xml 中）：

```xml
<!-- 精确位置（GPS） -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<!-- 模糊位置（网络） -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

### 动态申请权限

检查权限是否已授予：

```kotlin
if (ContextCompat.checkSelfPermission(
        this,
        Manifest.permission.ACCESS_FINE_LOCATION
    ) == PackageManager.PERMISSION_GRANTED) {
    // 已授权
} else {
    // 未授权，需要申请
}
```

请求权限：

```kotlin
ActivityCompat.requestPermissions(
    this,
    arrayOf(Manifest.permission.ACCESS_FINE_LOCATION),
    100  // 请求码
)
```

处理权限结果：

```kotlin
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)
    if (requestCode == 100) {
        if (grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            // 用户授权
        } else {
            // 用户拒绝
        }
    }
}
```

### FusedLocationProviderClient（获取实际位置）

获取位置的基本代码：

```kotlin
// 获取位置客户端
val fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

// 检查权限后获取最后已知位置
if (ContextCompat.checkSelfPermission(...) == PackageManager.PERMISSION_GRANTED) {
    fusedLocationClient.lastLocation
        .addOnSuccessListener { location ->
            location?.let {
                val lat = it.latitude
                val lng = it.longitude
            }
        }
}
```

### 注意事项

1. **真机测试**：模拟器无法获取真实 GPS 位置
2. **权限检查**：始终检查权限后再调用定位 API
3. **后台权限**：Android 10+ 需要单独申请后台位置权限
4. **电池优化**：GPS 耗电较高，非必要时不开启

## 关键代码详解

### MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {

    // 声明组件变量
    private lateinit var resultText: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 获取 TextView 组件
        resultText = findViewById(R.id.resultText)

        // 2. 设置按钮点击事件
        findViewById<Button>(R.id.getLocationBtn).setOnClickListener {
            // 3. 检查位置权限是否已授予
            if (ContextCompat.checkSelfPermission(
                    this,
                    Manifest.permission.ACCESS_FINE_LOCATION
                ) == PackageManager.PERMISSION_GRANTED) {

                // 已授权，显示提示信息
                resultText.text = "定位功能演示\n需要真机测试"

            } else {
                // 未授权，请求权限
                ActivityCompat.requestPermissions(
                    this,
                    arrayOf(Manifest.permission.ACCESS_FINE_LOCATION),
                    100  // 请求码，用于在结果中识别
                )
            }
        }
    }

    // 3. 处理权限请求结果
    override fun onRequestPermissionsResult(
        requestCode: Int,
        permissions: Array<out String>,
        grantResults: IntArray
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)

        // 判断是否是位置权限请求
        if (requestCode == 100) {
            // 显示权限结果
            // PERMISSION_GRANTED = 0, PERMISSION_DENIED = -1
            Toast.makeText(
                this,
                "权限结果: ${grantResults[0]}",
                Toast.LENGTH_SHORT
            ).show()
        }
    }
}
```

### activity_main.xml

```xml
<!-- 根布局：垂直线性布局 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <!-- 标题 -->
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Location 位置服务演示"
        android:textSize="20sp"
        android:textStyle="bold"
        android:gravity="center"
        android:paddingBottom="16dp" />

    <!-- 获取位置按钮 -->
    <Button
        android:id="@+id/getLocationBtn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="获取位置" />

    <!-- 显示结果的 TextView -->
    <TextView
        android:id="@+id/resultText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="16dp" />
</LinearLayout>
```

### 权限常量说明

| 权限 | 说明 | 精度 |
|------|------|------|
| ACCESS_FINE_LOCATION | GPS + 网络 | 精确 |
| ACCESS_COARSE_LOCATION | 仅网络 | 模糊 |
| ACCESS_BACKGROUND_LOCATION | 后台定位 | 需要前两个 |
