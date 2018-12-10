# Android SDK

## Release Note

### 1.0.6 版本发布

- 1.0.6
  - 发布 ustreaming-1.0.6.jar
  - 增加音频采集控制接口

- 1.0.5
  - 发布 ustreaming-1.0.5.jar
  - 修复偶现的编码器死锁问题

### 1.0.4 版本发布

- 1.0.4
  - 发布 ustreaming-1.0.4.jar
  - 修复偶现的音画不同步问题

### 1.0.3 版本发布

- 1.0.3
  - 发布 ustreaming-1.0.3.jar
  - 修复切换摄像头、图片推流可能导致的音视频不同步问题
  - 修复弱网下重连可能导致的 ANR 问题

### 1.0.2 版本发布

- 1.0.2
  - 发布 ustreaming-1.0.2.jar
  - 优化内置美颜实现方案
  - 优化 Demo 进入预览界面自动推流
  - 修复不严谨的鉴权逻辑
  - 修复 Demo 进入后台，回到前台没有自动推流问题

### 1.0.1 版本发布

- 1.0.1
  - 发布 ustreaming-1.0.1.jar
  - 修复连续调用 `startStreaming` 导致无法推流问题
  - Demo 增加断线重连机制

### 1.0.0 版本发布

- 1.0.0
  - 发布 ustreaming-1.0.0.jar
  - 支持标准 RTMP 推流
  - 支持外置美颜
  - 支持添加水印

## 如何安装

- 把 `release` 目录下的 `jar` 包拷贝到工程的 `libs` 目录下
- 把 `release` 目录下的 `assets` 文件夹下的内容拷贝到 `assets` 目录下

## 快速开始

### 开发环境

- Android Studio 集成 IDE 开发平台，[下载](http://developer.android.com/intl/zh-cn/sdk/index.html)
- Android 开发 SDK，[下载](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)

### 系统要求

- Android 4.4 (API 19) 及其以上

### 文件说明

SDK 主要包含 demo 代码、jar 包，以及依赖的资源文件，说明如下：

| 文件名称                           | 功能      | 大小    | 备注          |
| ------------------------------ | ------- | ----- | ----------- |
| ustreaming-x.x.x.jar   | SDK 库   | 209KB | 必须依赖        |
| assets           | 内置美颜资源文件    | 6KB | 不使用内置美颜可以去掉 |

### 添加依赖

打开工程目录下的 `build.gradle`，添加如下依赖：

```java
dependencies {
    implementation files('libs/ustreaming-x.x.x.jar')
}
```

### 添加权限

在 AndroidManifest.xml 中添加权限：

```java
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### 初始化 SDK

```java
// 初始化 SDK 运行环境，建议在应用启动时调用
UStreamingEnv.init(context);
```

### 创建推流核心类对象

```java
UStreamingManager mStreamingManager = new UStreamingManager();
mStreamingManager.setStreamingStateListener(this); // 推流过程中状态信息回调
mStreamingManager.setVideoFrameListener(this);     // 视频数据回调
mStreamingManager.setAudioFrameListener(this);     // 音频数据回调
mStreamingManager.setStatisticsInfoListener(this); // 推流过程中实时码率、帧率信息回调
```

### 配置采集参数

```java
// 摄像头采集参数
UCameraParam cameraParam = new UCameraParam();
cameraParam.setCameraId(UCameraParam.CAMERA_FACING_ID.FRONT);
cameraParam.setCameraPreviewSizeRatio(UCameraParam.CAMERA_PREVIEW_SIZE_RATIO.RATIO_16_9);
cameraParam.setCameraPreviewSizeLevel(UCameraParam.CAMERA_PREVIEW_SIZE_LEVEL.SIZE_480P);
// 麦克风采集参数
UMicrophoneParam microphoneParam = new UMicrophoneParam();
// 视频编码参数
UVideoEncodeParam videoEncodeParam = new UVideoEncodeParam();
videoEncodeParam.setEncodingSizeLevel(UVideoEncodeParam.VIDEO_ENCODING_SIZE_LEVEL.SIZE_480P_16_9); // 480x848
videoEncodeParam.setEncodingBitrate(800*1024); // 800kbps
// 音频编码参数
UAudioEncodeParam audioEncodeParam = new UAudioEncodeParam();
```

### 创建预览窗口

视频采集需要的预览窗口为 `GLSurfaceView` 或者其派生的类对象，可以配置在 Layout 文件中，也可以动态创建。

### 配置推流参数

通过 `prepare` 把推流需要的参数配置到 SDK 中：

```java
// 如果不使用内置美颜，beautyParam 可以传递 null
mStreamingManager.prepare(preview, cameraParam, microphoneParam, videoEncodeParam, audioEncodeParam, beautyParam);
```

### 开始推流

```java
// 通过 streamingUrl 传入 RTMP 推流地址
mStreamingManager.startPublish(streamingUrl);
```

## 主要概念

### 设计规则

SDK 的接口设计，遵循了如下的原则：

- 接口类均以 `U` 开头
- 参数类均以 `UxxxParam` 命名
- 回调类均以 `UxxxListener` 命名

#### 接口类

| 类名                      | 说明         | 备注              |
| :------------------------ | ----------- | ---------------- |
| UStreamingManager              | 负责 RTMP 直播推流      | 无 |

#### 参数类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| UCameraParam              | Camera 采集参数    | 无 |
| UMicrophoneParam          | 麦克风采集参数      | 无 |
| UVideoEncodeParam         | 视频编码参数        | 无 |
| UAudioEncodeParam         | 音频编码参数        | 无 |

#### 回调类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| UVideoFrameListener       | 视频数据回调       | 纹理数据，支持第三方特效处理 |
| UAudioFrameListener       | 音频数据回调       | PCM 数据，支持音频处理 |
| UStreamingStateListener   | 用于监听推流过程中的状态变化 | 无 |

## 使用指南

### 核心类

`UStreamingManager` 包含了 RTMP 直播推流需要的接口，方法列表如下：

```java
/**
 * 构造 UStreamingManager 对象
 */
public UStreamingManager();

/**
 * 使 UStreamingManager 暂停工作，通常在 Activity#onPause 中调用
 */
public pause();

/**
 * 使 UStreamingManager 恢复工作，通常在 Activity#onResume 中调用
 */
public resume();

/**
 * 销毁 UStreamingManager 对象，通常在 Activity#onDestroy 中调用
 */
public destory();

/**
 * Prepare UStreamingManager
 * @param glSurfaceView      预览的 GLSurfaceView
 * @param cameraParam      UCameraParam 对象
 * @param microphoneParam  UMicrophoneParam 对象
 * @param videoEncodeParam UVideoEncodeParam 对象
 * @param audioEncodeParam UAudioEncodeParam 对象
 * @param faceBeautyParam  UFaceBeautyParam 对象
 */
public void prepare(GLSurfaceView glSurfaceView,
                        UCameraParam cameraParam,
                        UMicrophoneParam microphoneParam,
                        UVideoEncodeParam videoEncodeParam,
                        UAudioEncodeParam audioEncodeParam,
                        UFaceBeautyParam faceBeautyParam);

/**
 * 开始推流
 * @param rtmpUrl 推流地址
 */
public void startPublish(String rtmpUrl);

/**
 * 停止推流
 */
public void stopPublish();

/**
 * 设置图片推流的图片文件地址
 * @param filePath 图片在手机上的路径
 */
public void setPictureStreamingFilePath(String filePath);

/**
 * 设置图片推流的图片资源文件
 * @param resId 图片的资源 ID
 */
public void setPictureStreamingResourceId(int resId);

/**
 * 在摄像头和图片推流之间切换
 */
public synchronized boolean switchPictureStreaming();

/**
 * 手动对焦，在预览画面上指定对焦区域大小以及相应的中心坐标
 * @param 对焦区域的宽度
 * @param 对焦区域的高度
 * @param 对焦区域中心 X 坐标
 * @param 对焦区域中心 Y 坐标
 */
public void manualFocus(int focusBlockWidth, int focusBlockHeight, int focusBlockCenterX, int focusBlockCenterY);

/**
 * 注册相机对焦监听器
 * @param 对焦监听器
 */
public void setFocusListener(UFocusListener listener);

/**
 * 切换到下一个摄像头
 */
public void switchCamera();

/**
 * 切换到指定摄像头
 */
public void switchCamera(UCameraParam.CAMERA_FACING_ID facingId);

/**
 * 设置闪光灯开关
 */
public void setFlashEnabled(boolean enabled);

/**
 * 更新美颜设置
 * @param 美颜设置对象
 */
public final void updateFaceBeautyParam(UFaceBeautyParam Param);

/**
 * 注册自定义美颜监听器
 * @param 监听器对象
 */
public final void setVideoFrameListener(UVideoFrameListener listener);

/**
 * 注册音频帧监听器
 * @param 监听器对象
 */
public final void setAudioFrameListener(UAudioFrameListener listener);

/**
 * 注册推流过程码率、帧率统计信息监听器
 * @param 监听器对象
 */
public final void setStatisticsInfoListener(UStatisticsInfoListener listener);

/**
 * 获取当前摄像头的最大曝光补偿值
 * @return 最大曝光补偿值
 */
public int getMaxExposureCompensation();

/**
 * 获取当前摄像头的最小曝光补偿值
 * @return 最小曝光补偿值
 */
public int getMinExposureCompensation();

/**
 * 设置曝光补偿值
 */
public void setExposureCompensation(int exposureCompensation);

/**
 * 设置预览画面的缩放值
 * 调用此方法前，请先调用 getZooms 方法获取有效值
 * @param 缩放值
 */
public void setZoom(float zoom);

/**
 * 获取有效的缩放值列表
 * 已由小至大排序
 * @return 如果该分辨率不支持缩放，则返回 null
 */
public List<Float> getZooms();

/**
 * 设置水印
 * @param watermarkParam 水印参数，包括水印图片、放置位置等
 */
public void setWatermark(UWatermarkParam watermarkParam)
```

### 参数类

#### UCameraParam

`UCameraParam` 类用于设置摄像头采集的相关参数，包含以下方法：

```java
/**
 * 设置采集摄像头
 * @param 默认设置为手机的后置摄像头
 */
public UCameraParam setCameraId(UCameraParam.CAMERA_FACING_ID cameraId);

/**
 * 设置采集画面的分辨率
 * @param 可以选择从 120P 到 1200P 之间的多种分辨率。默认设置为 480P
 */
public UCameraParam setCameraPreviewSizeLevel(UCameraParam.CAMERA_PREVIEW_SIZE_LEVEL level);

/**
 * 设置采集画面的长宽比
 * @param 可以选择 4:3 或 16:9。默认设置为 16:9
 */
public UCameraParam setCameraPreviewSizeRatio(UCameraParam.CAMERA_PREVIEW_SIZE_RATIO ratio);
```

#### UMicrophoneParam

`UMicrophoneParam` 类用于设置麦克风采集的相关参数，包含以下方法：

```java
/**
 * 设置音频采集源
 * @param 默认为系统的 MediaRecorder.AudioSource.MIC，可视情况进行修改
 */
public UMicrophoneParam setAudioSource(int source);

/**
 * 设置音频格式
 * @param 默认为系统的 AudioFormat.ENCODING_PCM_16BIT，其他格式可能不兼容部分设备，请视情况使用
 */
public UMicrophoneParam setAudioFormat(int format);

/**
 * 设置音频声道
 * @param 默认为系统的 AudioFormat.CHANNEL_IN_MONO，其他格式如 AudioFormat.CHANNEL_IN_STEREO 可能不兼容部分设备，请视情况使用
 */
public UMicrophoneParam setChannelConfig(int channelConfig);

/**
 * 设置音频采样率
 * @param 采样率, 默认为 44100，其他设置可能不兼容部分设备，请视情况使用
 */
public UMicrophoneParam setSampleRate(int rate);
```

#### UVideoEncodeParam

`UVideoEncodeParam` 类用于设置视频编码的相关参数，包含以下方法：

```java
/**
 * 设置编码码率
 * @param 码率，单位为 bps，默认为 1000 * 1000
 */
public UVideoEncodeParam setEncodingBitrate(int bitrate);

/**
 * 设置编码 fps
 * @param fps 值，默认设置为 30
 */
public UVideoEncodeParam setEncodingFps(int fps);

/**
 * 设置编码视频的分辨率
 * @param 可以选择从 240P 到 1200P 之间的多种分辨率，默认设置为 480 * 848
 */
public UVideoEncodeParam setEncodingSizeLevel(UVideoEncodeParam.VIDEO_ENCODING_SIZE_LEVEL level);

/**
 * 如果 SDK 内置的分辨率列表不能满足需求，可以通过此方法自定义编码视频的分辨率
 * @param width 宽度
 * @param height 高度
 */
public UVideoEncodeParam setPreferedEncodingSize(int width, int height);

/**
 * 设置编码视频关键帧的间隔
 * @param 单位为帧数，默认设置为 30 帧
 */
public UVideoEncodeParam setKeyFrameInterval(int interval);
```

#### UAudioEncodeParam

`UAudioEncodeParam` 类用于设置音频编码的相关参数，包含以下方法：

```java
/**
 * 设置编码码率
 * @param 码率，单位为 bps，默认为 44100
 */
public UAudioEncodeParam setBitrate(int bitrate);

/**
 * 设置音频声道数
 * @param 声道数，目前只支持 1 (默认) 和 2，分别对应 UMicrophoneParam 声道设置的 AudioFormat.CHANNEL_IN_MONO 与 AudioFormat.CHANNEL_IN_STEREO
 */
public UAudioEncodeParam setChannels(int channels);

/**
 * 设置音频采样率
 * @param 采样率, 默认为 44100
 */
public UAudioEncodeParam setSampleRate(int sampleRate);
```

### 回调类

#### UStreamingStateListener

该接口用户回调推流过程中的状态变化信息，方法如下：

```java
/**
 * 推流状态发生变化时触发
 * @param state 当前状态
 * @param extra 相关信息
 */
void onStateChanged(UStreamingState state, Object extra);
```

#### UVideoFrameListener

该接口用于回调视频采集相关的事件，方法如下：

```java
/**
 * Surface 创建时触发.
 */
void onSurfaceCreated();

/**
 * Surface 大小发生变化时触发
 * @param width  the width of the Surface
 * @param height the height of the Surface
 */
void onSurfaceChanged(int width, int height);

/**
 * Surface 销毁时触发
 */
void onSurfaceDestroy();

/**
 * 采集到一帧视频时触发
 * @param texId           the texture ID of Camera SurfaceTexture object to be rendered.
 * @param texWidth        width of the drawing surface in pixels.
 * @param texHeight       height of the drawing surface in pixels.
 * @param timestampNs     timestamp of this frame in Ns.
 * @param transformMatrix transform matrix.
 * @return                the texture ID of the newly generated texture to be assigned to the SurfaceTexture object.
 */
int onDrawFrame(int texId, int texWidth, int texHeight, long timestampNs, float[] transformMatrix);
```

#### UAudioFrameListener

该接口用于回调音频采集相关的事件，方法如下：

```java
/**
 * 录制失败时触发
 * @param 错误码，在 UErrorCode 中定义
 */
void onAudioRecordFailed(int errorCode);

/**
 * 采集到一帧音频时触发
 * @param data PCM 数据
 * @param timestampNs 音频帧时间戳，单位为 ns
 */
void onAudioFrameAvailable(byte[] data, long timestampNs);
```

#### UStatisticsInfoListener

该接口用于回调推流过程中的帧率、码率等统计信息，方法如下：

```java
/**
 * 发送视频帧后触发
 * @param fps      frame per second
 * @param bitrate  bit per second
 */
void onVideoInfoChanged(double fps, double bitrate);

/**
 * 发送音频帧后触发
 * @param bitrate  bit per second
 */
void onAudioBitrateChanged(double bitrate);
```

### 自定义对象

#### UStreamingState

推流过程状态信息，包含如下内容：

| 错误码                             | 说明    |
| --------------------------------- | ---- |
| READY                       | SDK 采集准备完成     |
| CONNECTING                  | 正在连接推流服务器    |
| STREAMING                   | 开始发送音视频数据    |
| SHUTDOWN                    | 关闭推流            |
| ERROR                       | 推流过程中出现异常    |
| DISCONNECTED                | 推流连接断开         |
| CONNECTED                   | 推流连接建立成功      |
| UNAUTHORIZED_STREAMING_URL  | 推流域名未授权       |

#### UErrorCode

自定义错误码，包含如下内容：

| 错误码                               | 错误值  | 说明                  |
| --------------------------------- | ---- | ------------------- |
| SUCCESS                           | 0    | 无错误发生               |
| ERROR_IO_EXCEPTION                | 20101    | IO 错误               |
| ERROR_SIGN_INVALID                | 20102    | 签名无效               |
| ERROR_JSON_INVALID                | 20103    | JSON 格式错误               |
| ERROR_HTTP_SOCKET_TIMEOUT         | 20104    | SOCKET 错误               |
| ERROR_HTTP_IO_EXCEPTION           | 20105    | HTTP IO 错误               |
| ERROR_HTTP_RESPONSE_EXCEPTION     | 20106    | HTTP 返回错误               |
| ERROR_WRONG_STATE                 | 20001    | 状态错误                |
| ERROR_SETUP_CAMERA_FAILED         | 20004    | 设置摄像头失败             |
| ERROR_SETUP_MICROPHONE_FAILED     | 20005    | 设置麦克风失败             |
| ERROR_SETUP_VIDEO_ENCODER_FAILED  | 20006    | 设置视频编码器失败           |
| ERROR_SETUP_AUDIO_ENCODER_FAILED  | 20007    | 设置音频编码器失败           |
| ERROR_UNAUTHORIZED                | 20008    | 鉴权失败                |
| ERROR_INVALID_ARG                 | 20010   | 传入了错误的参数信息          |
| ERROR_DIFFERENT_AUDIO_PARAMS      | 20011   | 音频参数不一致     |
| ERROR_MISSING_DYNAMIC_LIBRARY     | 20012   | 缺少动态库文件             |
| ERROR_NO_VIDEO_TRACK              | 20013   | 未发现视频信息             |
| ERROR_LOW_MEMORY                  | 20015   | 内存不足                |
| ERROR_MULTI_CODEC_WRONG           | 20016   | 该设备不支持多例 MediaCodec     |
| ERROR_SETUP_VIDEO_DECODER_FAILED  | 20017   | 设置视频解码器失败           |
