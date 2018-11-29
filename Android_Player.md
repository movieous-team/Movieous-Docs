# Android SDK

## Release Note

### 1.0.0 版本发布

- 1.0.0
  - 发布 uplayer-1.0.0.jar
  - 发布 libuplayer.so
  - 支持主流直播、点播播放
  - 支持常见播放协议

## 如何安装

- 下载最新版本的 [UPlayer SDK](https://github.com/movieous-team/MovieousPlayer-Android-Release/releases)
- 将 release 目录下的 jar 包和动态库，拷贝到您的工程对应的目录下

## 快速开始

### 开发环境

- Android Studio 开发工具，[下载地址](http://developer.android.com/intl/zh-cn/sdk/index.html)
- Android 官方开发 SDK，[下载地址](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)。

### 添加依赖

```java
dependencies {
    implementation files('libs/uplayer-x.x.x.jar')
}
```

### 添加权限

在 AndroidManifest.xml 中添加权限

```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

### 使用 UVideoView/UVideoTextureView 播放

UPlayer SDK 提供的 `UVideoView` 类和 `UVideoTextureView` 类可以快速实现带界面的播放器功能，它们的接口与 Android 官方的 `VideoView` 类基本保持一致，其内部封装了 `UMediaPlayer` 类所提供的播放功能。

他们的使用方法基本相同，唯一的区别在于 `UVideoView` 类使用了 `SurfaceView` 来完成视频画面的渲染，而 `UVideoTextureView` 采用了 `TextureView` 完成视频画面的渲染 ，因此，在这里合并在一起来介绍。

#### 布局

如果采用 `UVideoView` 的话，布局如下：

```java
<com.movieous.player.widget.UVideoView
        android:id="@+id/UVideoView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center" />

```

如果采用 `UVideoTextureView` 的话，布局如下：

```java
<com.movieous.player.widget.UVideoTextureView
        android:id="@+id/UVideoTextureView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center" />

```

#### 初始化

如果采用 `UVideoView` 的话，初始化的方法如下：

```java
UVideoView mVideoView = (UVideoView) findViewById(R.id.UVideView);
```

如果采用 `UVideoTextureView` 的话，初始化的方法如下：

```java
UVideoTextureView mVideoView = (UVideoTextureView) findViewById(R.id.UVideoTextureView);
```

#### 关联播放控制器

Demo 里面提供了一个播放控制器示例，`MediaController`，您可以随意修改满足定制化需求，将该控制器控件关联到 `UVideoView` 或者 `UVideoTextureView` 的方法如下：

```java
MediaController mMediaController = new MediaController(this);
mVideoView.setMediaController(mMediaController);
```

#### 设置加载动画

`UVideoView` 和 `UVideoTextureView` 提供了设置加载动画的接口，在播放器进入缓冲状态时，自动显示加载界面，缓冲结束后，自动隐藏加载界面，设置方法如下：

```java
View loadingView = findViewById(R.id.LoadingView);
mVideoView.setBufferingIndicator(loadingView);
```

该 `loadingView` 可以是任意的 Android View 视图对象。

#### 设置播放状态监听器

`UVideoView` 和 `UVideoTextureView` 都提供了丰富的播放状态消息回调，您可以通过 `setXXXListener` 接口实现各种消息状态的获取，示例如下：

```java
public class UVideoViewActivity extends Activity implements
        UMediaPlayer.OnPreparedListener,
        UMediaPlayer.OnInfoListener,
        UMediaPlayer.OnCompletionListener,
        UMediaPlayer.OnVideoSizeChangedListener,
        UMediaPlayer.OnErrorListener
...
    mVideoView.setOnPreparedListener(this);
    mVideoView.setOnInfoListener(this);
    mVideoView.setOnCompletionListener(this);
    mVideoView.setOnVideoSizeChangedListener(this);
    mVideoView.setOnErrorListener(this);
...
```

#### 设置画面预览模式

`UVideoView` 和 `UVideTextureView` 提供了各种画面预览模式，包括：原始尺寸、适应屏幕、全屏铺满、16:9、4:3 等，设置方法如下：

```java
mVideoView.setDisplayAspectRatio(UVideoView.ASPECT_RATIO_ORIGIN);
mVideoView.setDisplayAspectRatio(UVideoView.ASPECT_RATIO_FIT_PARENT);
mVideoView.setDisplayAspectRatio(UVideoView.ASPECT_RATIO_PAVED_PARENT);
mVideoView.setDisplayAspectRatio(UVideoView.ASPECT_RATIO_16_9);
mVideoView.setDisplayAspectRatio(UVideoView.ASPECT_RATIO_4_3);
```

#### 设置画面旋转

`UVideTextureView` 还支持画面旋转，支持播放画面以 0度，90度，180度，270度旋转，设置方法如下：

```java
mVideoView.setDisplayOrientation(90); // 旋转90度
```

#### 设置播放画面镜像变换

`UVideTextureView` 还支持画面的镜像变换，设置方法如下：

```java
mVideoView.setMirror(true);
```

#### 设置播放地址

这是最重要的环节，在调用播放器的控制接口之前，必须先设置好播放地址。

传入播放地址，可以是 /path/to/local.mp4 本地文件绝对路径，或 HLS URL，或 RTMP URL

```java
mVideoView.setVideoPath(videoPath);
```

#### 播放控制

如果已经关联了 `MediaController`，可以直接通过该控件实现播放过程的控制，包括：暂停、继续、停止等，当然，您也可以通过 `UVideoView` 提供的接口自行进行播放过程的控制，相关函数如下：

```java
mVideoView.start();
mVideoView.pause();
mVideoView.stopPlayback();
```

### 使用 UMediaPlayer 播放

UPlayer SDK 提供的 `UMediaPlayer` 实现了一个媒体播放器的各种基础功能和接口，与 Android 官方的 `MediaPlayer` 的设计基本保持一致。

#### 布局

`UMediaPlayer` 不是一个 `View` 控件，因此，如果希望用它播放视频而不是纯音频的话，则需要在布局文件中自己定义一个 `SurfaceView` 或者 `TextureView` 控件，然后将 `Surface` 或者 `SurfaceHolder` 传递给 `UMediaPlayer`，示例布局如下：

```java
<SurfaceView
    android:id="@+id/SurfaceView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center" />
```

#### 创建对象

```java
UMediaPlayer mMediaPlayer = new UMediaPlayer(mContext);
UMediaPlayer mMediaPlayer = new UMediaPlayer(mContext, mAVOptions);
```

#### 设置播放地址

```java
mMediaPlayer.setDataSource(mVideoPath);
```

#### 设置播放状态监听器

`UMediaPlayer` 提供了丰富的播放状态消息回调，您可以通过 `setXXXListener` 接口实现各种消息状态的获取，示例如下：

```java
public class UMediaPlayerActivity extends Activity implements
        UMediaPlayer.OnPreparedListener,
        UMediaPlayer.OnInfoListener,
        UMediaPlayer.OnCompletionListener,
        UMediaPlayer.OnVideoSizeChangedListener,
        UMediaPlayer.OnErrorListener {
...
    mMediaPlayer.setOnPreparedListener(this);
    mMediaPlayer.setOnInfoListener(this);
    mMediaPlayer.setOnCompletionListener(this);
    mMediaPlayer.setOnVideoSizeChangedListener(this);
    mMediaPlayer.setOnErrorListener(this);
...
```

#### 配置和准备

当 `SurfaceView` 成功创建后，需要将 `SurfaceHolder` 传入`UMediaPlayer`，示例如下：

```java
mMediaPlayer.setDisplay(mSurfaceHolder);
```

注意，如果是纯音频播放的话，则没有这一步。

当一切都准备就绪以后，就可以调用 `prepareAsync()` 开始准备播放了，该过程是异步的，因此需要首先注册一个 `OnPreparedListener` 获取准备结束的回调（如果前面已经注册过，则不用重复注册）。

```java
mMediaPlayer.setOnPreparedListener(new UMediaPlayer.OnPreparedListener() {
    @Override
    public void onPrepared(UMediaPlayer UMediaPlayer) {
        mMediaPlayer.start();
    }
});
mMediaPlayer.prepareAsync();
```

#### 播放控制

`UMediaPlayer` 提供的播放控制接口如下：

```java
mMediaPlayer.start();
mMediaPlayer.pause();
mMediaPlayer.stop();
mMediaPlayer.reset();
```

当播放完成，系统退出时，不要忘记调用 `release` 函数释放资源：

```java
mMediaPlayer.release();
```

## 主要概念

### 设计规则

接口与系统 API `MediaPlayer` 保持一致，方便您使用和理解

## 使用指南

### 播放参数配置

UPlayer SDK 提供的 `AVOptions` 类，可以用来配置播放参数，例如：

```java
AVOptions options = new AVOptions();

public final static int MEDIA_CODEC_SW_DECODE = 0;
public final static int MEDIA_CODEC_HW_DECODE = 1;
public final static int MEDIA_CODEC_AUTO = 2;

// 解码方式:
// codec＝AVOptions.MEDIA_CODEC_HW_DECODE，硬解
// codec=AVOptions.MEDIA_CODEC_SW_DECODE, 软解
// codec=AVOptions.MEDIA_CODEC_AUTO, 硬解优先，失败后自动切换到软解
// 默认值是：MEDIA_CODEC_SW_DECODE
options.setInteger(AVOptions.KEY_MEDIACODEC, codec);

// 准备超时时间，包括创建资源、建立连接、请求码流等，单位是 ms
// 默认值是：无
options.setInteger(AVOptions.KEY_PREPARE_TIMEOUT, 10 * 1000);

// 读取视频流超时时间，单位是 ms
// 默认值是：10 * 1000
options.setInteger(AVOptions.KEY_GET_AV_FRAME_TIMEOUT, 10 * 1000);

// 当前播放的是否为在线直播，如果是，则底层会有一些播放优化
// 默认值是：0
options.setInteger(AVOptions.KEY_LIVE_STREAMING, 1);

// 是否开启"延时优化"，只在在线直播流中有效
// 默认值是：0
options.setInteger(AVOptions.KEY_DELAY_OPTIMIZATION, 1);

// 默认的缓存大小，单位是 ms
// 默认值是：2000
options.setInteger(AVOptions.KEY_CACHE_BUFFER_DURATION, 2000);

// 最大的缓存大小，单位是 ms
// 默认值是：4000
options.setInteger(AVOptions.KEY_MAX_CACHE_BUFFER_DURATION, 4000);

// 是否自动启动播放，如果设置为 1，则在调用 `prepareAsync` 或者 `setVideoPath` 之后自动启动播放，无需调用 `start()`
// 默认值是：1
options.setInteger(AVOptions.KEY_START_ON_PREPARED, 1);

// 播放前最大探测流的字节数，单位是 byte
// 默认值是：128 * 1024 
options.setInteger(AVOptions.KEY_PROBESIZE, 128 * 1024);

// 请在开始播放之前配置
mVideoView.setAVOptions(options);
```

### 播放状态回调

UPlayer SDK 提供的所有状态监听接口，状态、错误的常量，均定义在 `UMediaPlayer` 类中。

#### OnPreparedListener

```java
public interface OnPreparedListener {
    void onPrepared(UMediaPlayer mp);
}
```

该对象用于监听播放器的 `prepare` 过程，该过程主要包括：创建资源、建立连接、请求码流等等，当 `prepare` 完成后，SDK 会回调该对象的 `onPrepared` 接口，下一步则可以调用播放器的 `start()` 启动播放。

#### OnInfoListener

```java
public interface OnInfoListener {
    boolean onInfo(UMediaPlayer mp, int what, int extra);
}
```

`what` 定义了消息类型，`extra` 是附加参数

| what                              | value | 描述         |
| --------------------------------- | ----- | ---------- |
| MEDIA_INFO_UNKNOWN                | 1     | 未知消息       |
| MEDIA_INFO_VIDEO_RENDERING_START  | 3     | 第一帧视频已成功渲染 |
| MEDIA_INFO_BUFFERING_START        | 701   | 开始缓冲       |
| MEDIA_INFO_BUFFERING_END          | 702   | 停止缓冲       |
| MEDIA_INFO_VIDEO_ROTATION_CHANGED | 10001 | 获取到视频的播放角度 |
| MEDIA_INFO_AUDIO_RENDERING_START  | 10002 | 第一帧音频已成功播放 |
| MEDIA_INFO_VIDEO_GOP_TIME         | 10003 | 获取视频的I帧间隔   |
| MEDIA_INFO_SWITCHING_SW_DECODE    | 802   | 硬解失败，自动切换软解 |

该对象用于监听播放器的状态消息，在播放器启动后，SDK 会在播放器发生状态变化时调用该对象的 `onInfo` 方法，同步状态信息。

#### OnCompletionListener

```java
public interface OnCompletionListener {
    void onCompletion(UMediaPlayer mp);
}
```

该对象用于监听播放结束的消息，关于该回调的时机，有如下定义：

- 如果是播放文件，则是播放到文件结束后产生回调
- 如果是在线视频，则会在读取到码流的`EOF`信息后产生回调，回调前会先播放完已缓冲的数据
- 如果播放过程中产生`onError`，并且没有处理的话，最后也会回调本接口
- 如果播放前设置了 `setLooping(true)`，则播放结束后会自动重新开始，不会回调本接口

#### OnErrorListener

```java
public interface OnErrorListener {
    boolean onError(UMediaPlayer mp, int errorCode);
}
```

该对象用于监听播放器的错误消息，一旦播放过程中产生任何错误信息，SDK 都会回调该接口，返回值决定了该错误是否已经被处理，如果返回 false，则代表没有被处理，下一步则会触发 `onCompletion` 消息。

| errorCode                      | value      | 描述           |
| ------------------------------ | ---------- | ------------ |
| MEDIA_ERROR_UNKNOWN            | -1         | 未知错误         |
| ERROR_CODE_INVALID_URI         | -2         | 无效的 URL      |
| ERROR_CODE_IO_ERROR            | -5         | 网络异常         |
| ERROR_CODE_STREAM_DISCONNECTED | -11        | 与服务器连接断开     |
| ERROR_CODE_EMPTY_PLAYLIST      | -541478725 | 空的播放列表       |
| ERROR_CODE_404_NOT_FOUND       | -875574520 | 播放资源不存在      |
| ERROR_CODE_CONNECTION_REFUSED  | -111       | 服务器拒绝连接      |
| ERROR_CODE_CONNECTION_TIMEOUT  | -110       | 连接超时         |
| ERROR_CODE_UNAUTHORIZED        | -825242872 | 未授权，播放一个禁播的流 |
| ERROR_CODE_PREPARE_TIMEOUT     | -2001      | 播放器准备超时      |
| ERROR_CODE_READ_FRAME_TIMEOUT  | -2002      | 读取数据超时       |
| ERROR_CODE_HW_DECODE_FAILURE   | -2003      | 硬解码失败       |

#### OnBufferingUpdateListener

```java
public interface OnBufferingUpdateListener {
    void onBufferingUpdate(UMediaPlayer mp, int percent);
}
```

该回调用于监听当前播放器已经缓冲的数据量占整个视频时长的百分比，在播放直播流中无效，仅在播放文件和回放时才有效。

#### OnVideoSizeChangedListener

```java
public interface OnVideoSizeChangedListener {
    void onVideoSizeChanged(UMediaPlayer mp, int width, int height);
}
```

该回调用于监听当前播放的视频流的尺寸信息，在 SDK 解析出视频的尺寸信息后，会触发该回调，开发者可以在该回调中调整 UI 的视图尺寸。

#### OnSeekCompleteListener

```java
public interface OnSeekCompleteListener {
    void onSeekComplete(UMediaPlayer mp);
}
```

该回调用于监听 `seek` 完成的消息，当调用的播放器的 `seekTo` 方法后，SDK 会在 `seek` 成功后触发该回调。

### 连接状态处理

#### 如何判断直播结束

对于直播应用而言，播放器本身是无法判断直播是否结束，这需要通过业务服务器来告知。当主播端停止推流后，播放器会因为读取不到新的数据而产生超时，从而触发 `onCompletion` 回调。

建议的处理方式是：在 `onCompletion` 回调后，查询业务服务器，获知直播是否结束，如果已经结束，则关闭播放器，清理资源；如果直播没有结束，则尝试重连。

如果使用的是 `UVideoView` 或者 `UVideoTextureView`，重连的方法如下：

```java
mVideoView.setVideoPath(mVideoPath);
mVideoView.start();
```

如果使用的是 `UMediaPlayer`，重连的方法如下：

```java
mMediaPlayer.reset();
mMediaPlayer.setDisplay(mSurfaceView.getHolder());
mMediaPlayer.setDataSource(mVideoPath);
mMediaPlayer.prepareAsync();
```

重连之前，建议使用如下方法判断一下网络的联通性，并且在每次重连之前，delay 1～2s

```java
public static boolean isNetworkAvailable(Context context) {
    ConnectivityManager cm = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo netInfo = cm.getActiveNetworkInfo();
    return netInfo != null && netInfo.isConnectedOrConnecting();
}
```

记得添加如下权限：

```java
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

#### 网络异常的处理

如果申请的直播并没有在推流，或者直播过程中发生网络错误（比如：WiFi 断开），播放器在请求超时或者播放完当前缓冲区中的数据后，会触发 `onError` 回调，`errorCode` 通常是 `ERROR_CODE_IO_ERROR`。

这种情况下，通常要做如下判断来考虑是否需要重连：

- 查询业务服务器，获知直播是否结束，如果没有结束，则可以尝试做重连
- 判断网络是否断开，如果网络连接是正常的，则可以尝试做重连

需要注意，如果决定做重连，则 `onError` 回调中，请返回 `true`，否则会导致触发 `onCompletion`.

### 播放器声音调节

如果期望调节播放器的音量，接口如下所示：

```java
public void setVolume(float leftVolume, float rightVolume);
```

音量参数的取值范围是：0.0～1.0，使用如下代码可以达到静音的效果：

```java
setVolume(0.0f, 0.0f);
```

### 设置播放封面

在开始播放前，UVideoView、UVideoTextureView 可以显示封面图片，设置接口如下：

```java
public void setCoverView(View coverView);
```

### 获取播放器当前状态

在播放过程中，用户可以调用接口，获取播放器当前状态，接口如下：

```java
public PlayerState getPlayerState();
```

### 获取 METADATA 信息

在播放过程中，用户可以调用接口，获取当前播放流的 METADATA 信息，接口如下：

```java
    public HashMap<String, String> getMetadata();
```

### 获取实时统计信息

```java
    public int getVideoFps();
    public long getVideoBitrate();
```