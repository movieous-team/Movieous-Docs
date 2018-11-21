# 阅读对象声明

本文档为技术文档，需要阅读者：

- 具备基本的 Android 开发能力
- 计划接入 Movieous 短视频 SDK

# 设计规则

SDK 的接口设计，遵循了如下的原则：

- 接口类均以 `U` 开头
- 参数类均以 `UxxxParam` 命名
- 回调类均以 `UxxxListener` 命名

# 核心接口

## 接口类

| 类名                      | 说明         | 备注              |
| :------------------------ | ----------- | ---------------- |
| UVideoRecord              | 负责视频拍摄录制      | 无 |
| UVideoEdit                | 负责视频编辑处理      | 无 |

## 参数类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| URecordParam              | 录制文件相关参数    | 无 |
| UCameraParam              | Camera 采集参数    | 无 |
| UMicrophoneParam          | 麦克风采集参数      | 无 |
| UVideoEncodeParam         | 视频编码参数        | 无 |
| UAudioEncodeParam         | 音频编码参数        | 无 |
| UVideoEditParam           | 编辑文件相关参数     | 无 |

## 回调类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| URecordStateListener      | 录制过程状态回调    | 无 |
| USaveFileListener         | 文件保存过程信息回调    | 无 |
| UVideoFrameListener       | 视频数据回调       | 纹理数据，支持第三方特效处理 |
| UAudioFrameListener       | 音频数据回调       | PCM 数据，支持音频处理 |

# 开发准备

本小结主要是阐述开发的前置条件。

## 开发环境

- Android Studio 集成 IDE 开发平台，[下载](http://developer.android.com/intl/zh-cn/sdk/index.html)
- Android 开发 SDK，[下载](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)

## 系统要求

- Android 4.4 (API 19) 及其以上

## 文件说明

SDK 主要包含 jar 包、动态库、以及相关的滤镜资源文件，具体说明如下：

| 文件名称                           | 功能      | 大小    | 备注          |
| ------------------------------ | ------- | ----- | ----------- |
| ushortvideo-x.x.x.jar          | 核心库    | 292KB | 必须依赖        |
| libcore.so                     | 核心库    |  78KB | 必须依赖        |
| libmuxer.so                    | 核心库    | 802KB | 必须依赖        |
| libamix.so                     | 混音模块   | 404KB | 不使用混音功能可以去掉 |
| libencoder.so                  | 软编码模块 | 3.0MB | 不使用软编功能可以去掉 |
| assets                         | 内置美颜滤镜 | 6.2KB | 不使用内置美颜可以去掉 |

## 添加依赖

打开工程目录下的 `build.gradle`，添加如下依赖：

```java
dependencies {
    implementation files('libs/ushortvideo-x.x.x.jar')
}
```

## 添加权限

在 AndroidManifest.xml 中添加权限：

```java
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

# 快速开始

## 初始化 SDK

```java
// MOVIEOUS_SIGN，在 Movieous 后台创建 APP 时生成，需要在初始化时传递给 SDK 进行鉴权
UShortVideoEnv.init(context, MOVIEOUS_SIGN)
```

## 视频录制

### 创建录制对象类

```java
UVideoRecord mVideoRecorder = new UVideoRecord();
// 设置录制状态回调
mVideoRecorder.setRecordStateListener(this);
// 设置视频数据回调
mVideoRecorder.setVideoFrameListener(this);
```

### 建录制参数类

SDK 提供了丰富的录制参数，可以通过以下方式进行配置：

```java
// 摄像头采集参数
UCameraParam cameraParam = new UCameraParam();
// 麦克风采集参数
UMicrophoneParam microphoneParam = new UMicrophoneParam();
// 视频编码参数
UVideoEncodeParam videoEncodeParam = new UVideoEncodeParam(getContext());
videoEncodeParam.setEncodingSizeLevel(UVideoEncodeParam.VIDEO_ENCODING_SIZE_LEVEL.SIZE_720P_16_9);
videoEncodeParam.setEncodingBitrate(Constants.DEFAULT_ENCODING_BITRATE);
// 音频编码参数
UAudioEncodeParam audioEncodeParam = new UAudioEncodeParam();
// 录制参数
URecordParam recordParam = new URecordParam();
recordParam.setMaxRecordDuration(Constants.DEFAULT_MAX_RECORD_DURATION);
recordParam.setVideoCacheDir(Constants.VIDEO_STORAGE_DIR);
recordParam.setVideoFilePath(Constants.RECORD_FILE_PATH);
```

### 创建预览窗口

视频预览窗口为 `GLSurfaceView`，可以在 XML 布局文件中创建，也可以通过代码动态创建。

### 配置录制参数

通过初始化接口 `init` 把录制参数配置到 SDK 中：

```java
mVideoRecorder.init(preview, cameraParam, microphoneParam, videoEncodeParam, audioEncodeParam, recordParam);
```

### 生命周期

请在 `Activity` 或者 `Fragment` 的生命周期回调中，需要调用 `UVideoRecord` 对应的方法：

```java
@Override
protected void onResume() {
    super.onResume();
    mVideoRecorder.resume();
}
@Override
protected void onPause() {
    super.onPause();
    mVideoRecorder.pause();
}
@Override
protected void onDestroy() {
    super.onDestroy();
    mVideoRecorder.destroy();
}
```

### 视频录制

上述配置完成后，就可以开始录制视频了。

```java
// 开始录制视频片段
mVideoRecorder.start();
// 停止录制
mVideoRecorder.stop();
// 合成本次录制的所有视频片段
mVideoRecorder.mergeClips(this);
```

<a id="6.3"></a>

## 视频编辑

### 创建参数类

通过 `UVideoEditParam` 配置视频编辑的参数：

```java
UVideoEditParam videoEditParam = new UVideoEditParam();
// 需要处理的视频文件路径
videoEditParam.setMediaFilepath(mediaFilepath);
// 处理后的文件保存路径
videoEditParam.setSaveFilepath(saveFilepath);
```

### 创建视频编辑对象

```java
// preview 用于预览渲染视频
UVideoEdit mVideoEditor = new UVideoEdit(preview, videoEditParam);
mVideoEditor.setVideoFrameListener(this);
mVideoEditor.setVideoSaveListener(this);
```

### 预览效果

```java
// 开始播放
mVideoEditor.startPlay();
// 恢复播放
mVideoEditor.resumePlay();
// 暂停播放
mVideoEditor.pausePlay();
// 停止播放
mVideoEditor.stopPlay();
```

### 特效处理

在预览播放的过程中，可以通过如下接口，对回调出来的视频数据进行各种效果的处理:

```java
@Override
public int onDrawFrame(int texId, int texWidth, int texHeight, long timestampNs, float[] transformMatrix) {
    ...
}
```

### 保存视频

编辑后的视频，调用如下接口进行保存：

```java
mVideoEditor.save();
```
