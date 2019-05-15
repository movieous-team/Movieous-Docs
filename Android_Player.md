# Android SDK

## 发版说明

### v2.0.2 (2019-05-15)

- 2.0.2
  - 发布 movieous-player-2.0.2.aar
  - 增加 `RTMP` 协议播放支持
  - `ULoadControl` 中增加 `setPauseBufferWithPlay` 接口，控制暂停播放后是否自动停止缓冲数据

### v2.0.1 (2019-05-13)

- 2.0.1
  - 发布 movieous-player-2.0.1.aar
  - 增加自定义缓冲区控制接口

### v2.0.0 (2019-05-10)

- 2.0.0
  - 发布 movieous-player-2.0.0.aar
  - 支持本地缓存，可以离线播放
  - 支持主流直播、点播播放
  - 支持 mp4、hls、dash、flv 等常见播放协议

## 如何安装

- 下载最新版本的 [MovieousPlayer SDK](https://github.com/movieous-team/MovieousPlayer-Android-Release)
- 将 release 目录下的 aar 包，拷贝到您的工程对应的目录下

## 快速开始

### 开发环境

- Android Studio 开发工具，[下载地址](http://developer.android.com/intl/zh-cn/sdk/index.html)
- Android 官方开发 SDK，[下载地址](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)。

### 系统要求

- Android 4.0 (API 14) 及其以上

### 文件说明

SDK 主要包含一个 aar 核心库，具体说明如下：

| 文件名称                           | 功能      | 大小    | 备注          |
| ------------------------------ | ------- | ----- | ----------- |
| movieous-player-x.x.x.aar          | 核心库     | 1.22MB | 必须依赖        |

### 添加依赖

在 app 模块的 'build.gradle' 中添加对 SDK 的依赖：

```java
repositories {
    flatDir {
        dirs 'libs' //this way we can find the .aar file in libs folder
    }
}

dependencies {
    implementation fileTree(include: ['*.aar'], dir: 'libs')
}
```

### 添加 java8 支持

SDK 需要 java8 支持，在 app 模块的 'build.gradle' 的 `android` 中添加 java8 支持：

```java
android {
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ...
}
```

### 添加权限

在 AndroidManifest.xml 中添加权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

### 使用 VideoView 播放

MovieousPlayer SDK 提供的 `VideoView` 类可以快速实现带界面的播放器功能，接口与 Android 官方的 `VideoView` 类基本保持一致，其内部封装了 `ExoPlayer` 类所提供的播放功能。

#### 初始化播放器环境

在调用 SDK 的接口前，必须首先对 SDK 进行初始化，调用代码如下：

```java
// 初始化 SDK，必须第一个调用，否则会出现异常
MovieousPlayerEnv.init(gContext);
```

#### 添加播放界面布局

在您的布局文件中添加 `VideoView` 播放控件：

```xml
<video.movieous.droid.player.ui.widget.VideoView
    android:id="@+id/video_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:useDefaultControls="true"
    app:useTextureViewBacking="true" />
```

如果采用 `TextureView` 渲染，设置 `app:useTextureViewBacking="true"`, 如果采用 `SurfaceView` 渲染，设置 `app:useTextureViewBacking="false"`, 默认是采用 `SurfaceView` 进行渲染。

#### 初始化 `VideoView` 控件

采用 `VideoView` 进行播放，初始化的方法如下：

```java
private void setupVideoView() {
    mVideoView = findViewById(R.id.video_view);
    // 设置相关回调
    mVideoView.setOnPreparedListener(this);
    mVideoView.setOnCompletionListener(this);
    mVideoView.setOnErrorListener(this);
    mVideoView.setAnalyticsListener(this);
    // 设置循环播放
    mVideoView.setRepeatMode(Player.REPEAT_MODE_ALL);
    // 设置播放地址
    mVideoView.setVideoPath(mVideoPath);
}
```

#### 设置播放控制器

如果设置 `app:useDefaultControls="true"`, 将使用 SDK 自带的播放器控制器，可以满足基本的播放控制。
同时 SDK 也支持自定义播放器控制器, 您需要实现 `VideoControlsCore` 中的方法，然后，通过 `setControls(@Nullable VideoControlsCore controls)` 接口把控制器对象传递给 `VideoView` 播放器对象。

#### 设置加载封面图像

`VideoView` 提供了设置视频封面的接口，在进入播放前，自动显示视频封面，开始播放后，自动隐藏视频封面，设置方法如下：

```java
mVideoView.setPreviewImage(...);
```

参数支持 `Uri` `Bitmap` `resourceId` `Drawable` 等本地图片资源类型。

对于远程图片资源，您可以通过 `public ImageView getPreviewImageView()` 获取视频封面资源，然后自行控制视频封面的显示内容，参考代码如下：

```java
// 视频封面
if (!TextUtils.isEmpty(videoItem.coverUrl)) {
    Glide.with(mContext.getApplicationContext())
            .load(videoItem.coverUrl)
            .into(holder.videoView.getPreviewImageView());
}
```

#### 设置播放状态监听器

`VideoView` 提供了丰富的播放状态消息回调，您可以通过 `setXXXListener` 接口实现各种消息状态的获取，示例如下：

```java
public class VideoViewActivity extends Activity implements
        OnPreparedListener,
        OnCompletionListener,
        OnErrorListener,
        AnalyticsListener
...
    mVideoView.setOnPreparedListener(this);
    mVideoView.setOnCompletionListener(this);
    mVideoView.setOnErrorListener(this);
    mVideoView.setAnalyticsListener(this);
...
```

#### 设置画面预览模式

`VideoView` 提供了各种画面预览模式，包括：原始尺寸、适应屏幕、全屏铺满等，设置方法如下：

```java
mVideoView.setScaleType(ScaleType.CENTER_INSIDE); // 默认
```

具体的 `ScaleType` 如下：

```java
/**
 * 参见 {@link android.widget.ImageView.ScaleType}
 */
public enum ScaleType {
    CENTER,
    CENTER_CROP,
    CENTER_INSIDE,
    FIT_CENTER,
    FIT_XY,
    NONE;
```

#### 设置画面旋转

`VideoView` 支持画面旋转，支持播放画面以 0 ~ 359 度旋转，设置方法如下：

```java
mVideoView.setVideoRotation(30)
```

#### 设置播放地址

这是最重要的环节，在调用播放器的控制接口之前，必须先设置好播放地址。

传入播放地址，可以是 /path/to/local.mp4 本地文件绝对路径，或远程服务器的 URL 地址

```java
mVideoView.setVideoPath(videoPath);
```

#### 播放控制

可以通过 `VideoView` 提供的接口进行播放过程的控制，相关函数如下：

```java
mVideoView.start();
mVideoView.pause();
mVideoView.stopPlayback();
mVideoView.restart();
mVideoView.seekTo(long milliSeconds);
```

#### 设置音量

在播放过程中，您可以调整播放器的音量，接口如下：

```java
// 音量调节范围从 0.0 到 1.0
mVideoView.setVolume(1.0f)
```

#### 自定义缓冲区设置

播放器允许您修改缓冲区的参数设置，以便更符合您的业务场景，接口如下：

```java
/**
 * 自定义缓冲设置
 * minBufferMs： 最小缓冲时间 默认 15000ms
 * maxBufferMs： 最大缓冲时间 默认 50000ms
 * bufferForPlaybackMs：首次缓冲开始播放时间，需要小于最小缓冲时间 默认 2500ms
 * bufferForPlaybackAfterRebufferMs：再次缓冲开始播放时间，需要小于最小缓冲时间 5000ms
 */
ULoadControl myLoadControl = new ULoadControl(10000, 30000, 1000, 2000);
// 是否播放暂停后自动停止缓冲，默认会继续缓冲到 maxBufferMs 停止
myLoadControl.setPauseBufferWithPlay(true);
MovieousPlayer.setLoadControl(myLoadControl);
```

#### `RTMP` 协议播放支持

针对播放 `RTMP` 直播流的场景，推荐采用 `HTTP FLV` 协议播放，首开上具有一定的优势。如果您希望直接播放 `RTMP` 的流，可以参考如下设置，引入 `RTMP` 支持。

添加 `RTMP` 扩展支持库：

```java
// for rtmp
api 'net.butterflytv.utils:rtmp-client:3.0.1'
```

您可以正常播放 `RTMP` 流了。
