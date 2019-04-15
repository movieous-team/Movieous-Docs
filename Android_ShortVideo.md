# Android SDK

## 发版说明

### v2.0.6 (2018-04-15)

- 2.0.6
  - 发布 ushortvideo-2.0.6.jar
  - 增加停止录制时，是否清除预览画面参数
  - 增加自定义录制 cache 目录时，自动检查创建文件目录机制
  - 增加自动检查创建编辑文件输出目录机制
  - 修复录制时指定 GOP 无效问题
  - 修复剪辑时预览画面和用户指定画面不匹配 bug

### v2.0.5 (2018-04-09)

- 2.0.5
  - 发布 ushortvideo-2.0.5.jar
  - 增加图片编辑功能
  - 增加相机闪光灯接口
  - 增加相机变焦接口
  - 相芯特效库升级到 v6.0

### v2.0.4 (2018-03-29)

- 2.0.4
  - 发布 ushortvideo-2.0.4.jar
  - 更新 libuaudio_converter.so
  - 增加视频画面裁剪接口
  - 增加 fastStartMp4 接口，加快在线播放开播速度
  - Movieous Demo 中增加自定义音视频参数的演示
  - 修复某些手机图片合成方向不正确 bug
  - 修复保存编辑视频时，自定义画面尺寸无效 bug
  - 修复保存编辑视频时，可能没有相关回调的 bug

### v2.0.3 (2018-03-25)

- 2.0.3
  - 发布 ushortvideo-2.0.3.jar
  - 增加屏幕录制功能
  - 增加涂鸦功能
  - Demo 中增加 SENSETIME 特效演示

### v2.0.2 (2018-03-18)

- 2.0.2
  - 发布 ushortvideo-2.0.2.jar
  - 增加 MV 特效接口

### v2.0.1 (2018-03-08)

- 2.0.1
  - 发布 ushortvideo-2.0.1.jar
  - 增加左右平分布局视频合拍功能
  - 增加 view 录制功能

### v2.0.0 (2018-03-01)

- 2.0.0
  - 发布 ushortvideo-2.0.0.jar
  - 发布 libuaudio_converter.so
  - 核心模块重构，性能更出色
  - 增加 log 管理模块，自动捕获保存 crash 信息

### v1.0.0 (2018-11-28)

- 1.0.0
  - 发布 ushortvideo-1.0.0.jar
  - 发布 libcore.so
  - 发布 libmuxer.so
  - 发布 libamix.so
  - 发布 libencoder.so
  - 发布 assets
  - 音/视频采集
  - 音/视频编码（H.264 + AAC）
  - 实时美颜、滤镜
  - 支持第三方美颜、滤镜
  - 自定义录制时长、码率、分辨率
  - 支持 1:1 录制
  - 断点录制
  - 回删录制片段
  - 抖音特效
  - 背景替换
  - 手势识别
  - 表情识别
  - 视频保存为 mp4 格式
  - 支持主流架构（armv7、arm64、x86）

## 如何安装

- 把 `release` 目录下的 `jar` 文件拷贝到工程的 `libs` 目录下
- 把 `release` 目录下的 `arm64-v8a、armeabi-v7a、x86` 目录拷贝到 `main` 下 的 `jniLibs` 目录下

## 快速开始

### 开发环境

- Android Studio 集成 IDE 开发平台，[下载](https://developer.android.google.cn/studio)
- Android 开发 SDK，[下载](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)

### 系统要求

- Android 4.4 (API 19) 及其以上

### 文件说明

SDK 主要包含 jar 文件和 native 动态库，具体说明如下：

| 文件名称                           | 功能      | 大小    | 备注          |
| ------------------------------ | ------- | ----- | ----------- |
| ushortvideo-x.x.x.jar          | 核心库     | 418KB | 必须依赖        |
| libuaudio_converter.so         | 音频模块   | 387KB | 不使用混音功能可以去掉 |

### 添加依赖

打开工程目录下的 `build.gradle`，添加如下依赖：

```java
dependencies {
    implementation files('libs/ushortvideo-x.x.x.jar')
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
// MOVIEOUS_SIGN，在控制台创建 APP 时生成，需要在初始化时传递给 SDK 进行鉴权
UShortVideoEnv.init(context, MOVIEOUS_SIGN)
```

### 视频录制

#### 创建录制对象类

```java
// 设置预览画面和相关回调
UTextureView mPreview = $(R.id.render_view);
mVideoRecorder = new UVideoRecordManager()
        .init(mPreview)
        .setRecordListener(this)
        .setFocusListener(this)
        .setVideoFrameListener(this);
```

#### 生命周期

请在 `Activity` 或者 `Fragment` 的生命周期回调中，调用 `UVideoRecordManager` 对应的方法：

```java
@Override
protected void onResume() {
    super.onResume();
    mVideoRecorder.startPreview();
}
@Override
protected void onPause() {
    super.onPause();
    mVideoRecorder.stopPreview();
}
@Override
protected void onDestroy() {
    super.onDestroy();
    mVideoRecorder.release();
}
```

#### 录制视频

上述配置完成后，就可以开始录制视频了。
通过 `startRecord` 和 `stopRecord` 方法可以录制视频片段，视频片段保存在 APP 的 cache 目录下，录制一个或多个视频片段后，调用 `combineClip` 方法合成最终的视频文件。

```java
// 开始录制视频片段
mVideoRecorder.startRecord();
// 结束录制视频片段
mVideoRecorder.stopRecord();
// 合成本次录制的所有视频片段
mVideoRecorder.combineClip(mOutFile, this);
```

### 视频编辑

#### 创建视频编辑对象

```java
// mPreview 用于渲染预览视频
mVideoEditor = new UVideoEditManager()
        .init(mPreview, mVideoPath)
        .setVideoFrameListener(this)
        .setVideoPlayerListener(this)
        .setVideoSaveListener(this);
```

#### 预览播放

```java
// 开始播放
mVideoEditor.start();
// 恢复播放
mVideoEditor.resume();
// 暂停播放
mVideoEditor.pause();
// 停止播放
mVideoEditor.stop();
```

#### 美颜、贴纸等三方特效处理

在预览播放的过程中，可以通过如下接口，对回调出来的视频数据进行各种特效处理:

```java
@Override
public int onDrawFrame(int texId, int texWidth, int texHeight) {
    ...
}
```

#### 保存视频

编辑后的视频，调用如下接口进行保存：

```java
mVideoEditor.save(Constants.EDIT_FILE_PATH); // 没有外部特效
// 或
mVideoEditor.save(Constants.EDIT_FILE_PATH, listener); // 有外部特效
```

## 主要概念

### 设计规则

SDK 的接口设计，遵循了如下的原则：

- 接口类以 `U` 开头
- 参数通过 `UAVOptions` 设置
- 回调类以 `UxxxListener` 命名

### 核心接口

#### 接口类

| 类名                      | 说明         | 备注              |
| :------------------------ | ----------- | ---------------- |
| UVideoRecordManager       | 负责视频拍摄录制      | 无 |
| UVideoEditManager         | 负责视频编辑处理      | 无 |
| UImageEditManager         | 负责图片合成视频      | 无 |
| UViewRecordManager        | 负责 view 录制       | 无 |
| UMultiVideoRecordManager  | 负责视频合拍          | 无 |
| USticker                  | 文字、图像等贴纸类     | 无 |

#### 参数类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| UAVOptions                | 设置录制、编辑文件输出参数    | 无 |

#### 回调类

| 类名                      | 说明        | 备注              |
| :------------------------ | ----------- | ---------------- |
| URecordListener           | 录制过程状态回调    | 无 |
| UVideoSaveListener        | 文件保存过程信息回调    | 无 |
| UVideoFrameListener       | 视频数据回调       | 纹理数据，支持第三方特效处理 |
| UAudioFrameListener       | 音频数据回调       | PCM 数据，支持音频处理 |
| UVideoPlayListener        | 播放状态回调       | 无 |

## 使用指南

### 常用功能

#### 视频剪辑

创建 `UVideoEditManager` 对象:

```java
mVideoEditManager = new UVideoEditManager();
```

初始化视频剪辑对象，开始播放视频.

```java
mVideoEditManager.init(mRenderView, mVideoFile)
        .setVideoFrameListener(this)
        .setVideoSaveListener(this)
        .start();
```

设置输出路径、剪辑时间，开始剪辑.

```java
UMediaTrimTime trimTime = new UMediaTrimTime(startTimeMs, endTimeMs);
mVideoEditManager.transcode(mOutFile, trimTime);
```

剪辑进度和结果会通过 `UVideoSaveListener` 回调给用户.

#### 图片合成

创建并初始化 `UImageEditManager` 对象

```java
mImageEditManager = new UImageEditManager();
mImageEditManager.init(this, mRenderView);
```

设置图片文件路径列表

```java
mImageEditManager.setImageList(photos);
```

设置转场特效

```java
mImageEditManager.changeTransfer(item.type);
```

设置背景音乐

```java
mImageEditManager.setMusic(musicFile);
```

预览合成文件效果，确认后保存文件

```java
mImageEditManager.saveVideo(new UVideoSaveListener() {
    @Override
    public void onVideoSaveProgress(float progress) {
        // 合成进度
        dialog.setProgress((int) (progress * 100));
    }

    @Override
    public void onVideoSaveSuccess(String path) {
        // 保存成功, 回调文件路径
    }
});
```

#### 视频合拍

创建并初始化 `UMultiVideoRecordManager` 视频合拍对象

```java
mRecordManager = new UMultiVideoRecordManager();
// 设置输出文件尺寸
UAVOptions options = new UAVOptions()
        .setInteger(UAVOptions.KEY_VIDEO_WIDTH, 720)
        .setInteger(UAVOptions.KEY_VIDEO_HEIGHT, 640);
mRecordManager.setAVOptions(options);
// 设置合拍视频文件路径
mRecordManager.setVideoFile(videoFile);
mRecordManager.init(mRenderView);
```

在 `Activity` 或者 `Fragment` 的生命周期中启动或停止画面预览

```java
// 开始预览
mRecordManager.startPreview();
// 停止预览
mRecordManager.stopPreview();
```

开始录制

```java
mRecordManager.startRecord();
```

结束录制

```java
mRecordManager.stopRecord();
```

#### VIEW 录制

创建并初始化 `UViewRecordManager` 录制对象

```java
mViewRecordManager = new UViewRecordManager();
mViewRecordManager.init(mRenderView);
```

设置输出文件路径

```java
mViewRecordManager.setRecordFile(OUT_FILE);
```

把需要录制的 view 放到 `ULinearLayout` 或者 `URelativeLayout` 容器中，设置录制 view 的容器

```java
mViewRecordManager.setRecordView(mLinearLayout);
```

开始录制

```java
mViewRecordManager.startRecord();
```

结束录制

```java
mViewRecordManager.stopRecord();
```

#### 添加背景音乐

在录制或者编辑视频时，可以通过以下接口添加背景音乐：

```java
/**
 * 设置背景音乐
 *
 * @param file 背景音乐文件
 */
public void setMusicFile(String file)
```

#### 三方特效处理

在录制或者编辑视频时，通过 `setVideoFrameListener` 接口，可以集成第三方美颜和贴纸等特效的处理逻辑

```java
/**
 * 设置视频渲染相关事件回调
 *
 * @param listener 视频渲染事件回调，参见 {@link UVideoFrameListener}
 */
public UVideoRecordManager setVideoFrameListener(UVideoFrameListener listener)
```

`UVideoFrameListener` 包含了视频处理整个生命周期相关事件的回调，具体定义如下：

```java
public interface UVideoFrameListener {
    /**
     * Called when the surface is created or recreated.
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onSurfaceCreated(GL10, EGLConfig)} invoked.
     */
    default void onSurfaceCreated() {
    }

    /**
     * Called when the surface changed size.
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onSurfaceChanged(GL10, int, int)}.
     *
     * @param width  the width of the surface
     * @param height the height of the surface
     */
    default void onSurfaceChanged(int width, int height) {
    }

    /**
     * Called when the surface destroyed
     */
    default void onSurfaceDestroyed() {
    }

    /**
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onDrawFrame(GL10)}.
     *
     * @param texId     the texture ID to be rendered.
     * @param texWidth  texture width.
     * @param texHeight texture height.
     * @return new texture ID.
     */
    default int onDrawFrame(int texId, int texWidth, int texHeight) {
        return texId;
    }
}
```

#### 音频帧数据回调

在录制或者编辑视频时，通过 `setAudioFrameListener` 接口，可以获取到音频的 PCM 原始数据，您可以对音频做相关的处理.

```java
/**
 * 设置音频采集相关事件回调
 *
 * @param listener 音频采集事件回调，参见 {@link UAudioFrameListener}
 */
public UVideoRecordManager setAudioFrameListener(UAudioFrameListener listener)
```

`UAudioFrameListener` 包含了音频事件和原始数据的回调，具体定义如下：

```java
public interface UAudioFrameListener {
    /**
     * To be called when audio frame recording failed
     */
    default void onAudioRecordFailed(int errorCode) {
    }

    /**
     * To be called when audio frame available
     *
     * @param buffer the pcm data
     * @param size   the pcm data size
     * @param ptsUs  the timestamp of the frame in us
     */
    default void onAudioFrameAvailable(ByteBuffer buffer, int size, long ptsUs) {
    }
}
```

#### 添加文字贴纸

在编辑视频时，可以通过以下接口添加文字：

```java
    mTextSticker = new USticker();
    String stickerText = "这是一个美丽的传说";
    int stickerW = mVideoWidth / 2;
    int stickerH = stickerW / stickerText.length();
    mTextSticker.init(USticker.StickerType.TEXT, stickerW, stickerH)
            .setText(stickerText, Color.RED)
            .setDuration(0, (int) UMediaUtil.getMetadata(mVideoFile).duration)
            .setPosition(mVideoWidth / 2 - stickerW / 2, mVideoHeight - stickerH - 20);     //视频图像的左上角为坐标原点
    mVideoEditManager.addSticker(mTextSticker);
```

删除文字贴纸

```java
    mVideoEditManager.removeSticker(mTextSticker);
```

#### 涂鸦功能

在编辑视频时，可以通过以下接口添加涂鸦视图：

```java
    mPaintView = new UPaintView(this, mRenderView.getWidth(), mRenderView.getHeight());
    mVideoEditManager.addPaintView(mPaintView);
```

删除涂鸦视图

```java
    mVideoEditManager.removePaintView(mPaintView);
```

回删上一步涂鸦操作

```java
    mPaintView.undo();
```

清空涂鸦视图

```java
    mPaintView.clear();
```

#### 屏幕录制

初始化屏幕录制对象

```java
    mScreenRecordManager = new UScreenRecordManager();
    mScreenRecordManager.setRecordStateListener(this);
    mScreenRecordManager.init(this, OUTPUT_FILE, new UAVOptions());
```

屏幕录制需要通过用户授权，调用 `requestScreenRecord` 接口，申请屏幕录制

```java
    mScreenRecordManager.requestScreenRecord();
```

调用上述接口后，会弹出申请屏幕录制窗口，用户授权后，会通过 `onActivityResult` 接口回调用户操作结果，调用下面接口，把授权结果返回 SDK：

```java
    boolean isOk = mScreenRecordManager.onActivityResult(requestCode, resultCode, data);
```

该接口返回  `true`，表示用户同意开始屏幕录制，调用 `startRecord` 接口开始录制：

```java
    mScreenRecordManager.startRecord();
```

录制完成后，调用 `stopRecord` 接口停止屏幕录制：

```java
    mScreenRecordManager.stopRecord();
```

### API 参考

#### `UShortVideoEnv`

该类提供了设置 SDK 运行时需要的环境变量功能，具体接口如下：

```java
    /**
     * 初始化 SDK 环境.
     *
     * @param context 应用上下文
     * @param sign    鉴权签名字符串, 控制台注册 app 后获取
     */
    public static void init(Context context, String sign)

    /**
     * 设置 log 输出等级
     *
     * @param level the level，See also {@link ULog#V}/{@link ULog#D}/{@link ULog#I}/{@link ULog#W}/{@link ULog#E}
     */
    public static void setLogLevel(int level)
```

#### `UVideoRecordManager`

该类提供了视频录制功能，具体接口如下：

```java
    /**
     * 初始化
     *
     * @param renderView 预览界面
     */
    public UVideoRecordManager init(UTextureView renderView)

    /**
     * 设置音视频相关参数
     *
     * @param options 参数，参见：{@link UAVOptions}
     */
    public UVideoRecordManager setAVOptions(UAVOptions options)

    /**
     * 设置视频渲染相关事件回调
     *
     * @param listener 视频渲染事件回调，参见 {@link UVideoFrameListener}
     */
    public UVideoRecordManager setVideoFrameListener(UVideoFrameListener listener)

    /**
     * 设置音频采集相关事件回调
     *
     * @param listener 音频采集事件回调，参见 {@link UAudioFrameListener}
     */
    public UVideoRecordManager setAudioFrameListener(UAudioFrameListener listener)

    /**
     * 设置录制事件回调
     *
     * @param listener 录制事件回调，参见 {@link URecordListener}
     */
    public UVideoRecordManager setRecordListener(URecordListener listener)

    /**
     * 开始预览
     */
    public void startPreview()

    /**
     * 停止预览，清除当前预览画面
     */
    public void stopPreview()

    /**
     * 停止预览
     *
     * @param clearScreen true: 清除画面 false：保留当前预览画面
     */
    public void stopPreview(boolean clearScreen)

    /**
     * 释放资源
     */
    public void release()

    /**
     * 是否正在录制
     */
    public boolean isRecording()

    /**
     * 开始录制
     *
     * @return 启动录制是否成功
     */
    public boolean startRecord()

    /**
     * 停止录制
     */
    public void stopRecord()

    /**
     * 合并录制片段
     *
     * @param outFile  保存录制文件路径
     * @param listener 保存过程事件回调
     */
    public void combineClip(String outFile, UVideoSaveListener listener)

    /**
     * 删除当前上一个录制片段
     */
    public void removeLastClip()

    /**
     * 删除当前所有录制片段
     */
    public void removeAllClips()

    /**
     * 切换摄像头
     */
    public void switchCamera()

    /**
     * 截取视频帧
     *
     * @param callback 通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     */
    public void captureVideoFrame(UBitmapOutputCallback callback)

    /**
     * 截取视频帧
     *
     * @param callback   通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     * @param withFilter 是否包含滤镜处理
     */
    public void captureVideoFrame(UBitmapOutputCallback callback, boolean withFilter)

    /**
     * 设置背景音乐
     *
     * @param file 背景音乐文件
     */
    public void setMusicFile(String file)

    /**
     * 设置原始音频音量
     *
     * @param volume 0 ~ 1
     */
    public void setOriginVolume(float volume)

    /**
     * 设置背景音乐音量
     *
     * @param volume 0 ~ 1
     */
    public void setMusicVolume(float volume)

    /**
     * 设置背景音乐开始位置
     *
     * @param positionMs 单位：毫秒
     */
    public void setMusicPositionMs(int positionMs)

    /**
     * 获取当前录制片段文件个数
     */
    public int getClipCount()

    /**
     * 获取当前录制片段总的时长，单位：毫秒
     */
    public int getClipsDurationMs()

    /**
     * 设置手动对焦事件监听
     *
     * @param listener 对焦事件回调，参见 {@link UCameraFocusListener}
     */
    public UVideoRecordManager setFocusListener(UCameraFocusListener listener)

    /**
     * 手动对焦
     *
     * @param x 手动对焦点 x 坐标
     * @param y 手动对焦点 y 坐标
     */
    public void focus(float x, float y)

    /**
     * 是否支持变焦
     */
    public boolean isCameraZoomSupported()

    /**
     * 设置 Camera 缩放比例
     *
     * @param zoom 0: 不进行缩放，范围 0 - 100
     */
    public void setCameraZoom(int zoom)

    /**
     * 是否支持闪光灯操作
     */
    public boolean isCameraFlashSupported()

    /**
     * 打开/关闭闪光灯
     *
     * @param lightOn true: 打开闪光灯 false：关闭闪光灯
     * @return 操作是否成功
     */
    public boolean turnCameraLight(boolean lightOn)

    /**
     * 设置拍照和录制时，是否自动开启闪光灯
     */
    public UVideoRecordManager setLightOnEnalbed(boolean lightOnEnalbed)
```

#### `UVideoEditManager`

- 该类提供了视频编辑、剪辑等视频处理功能，具体接口如下：

```java
    /**
     * 初始化视频编辑器
     *
     * @param renderView 预览视图
     * @param inFile     需要编辑的视频文件
     * @return
     */
    public UVideoEditManager init(UTextureView renderView, String inFile)

    /**
     * 设置音视频相关参数
     *
     * @param options 参数，参见：{@link UAVOptions}
     */
    public UVideoEditManager setAVOptions(UAVOptions options)

    /**
     * 设置视频保存事件回调
     *
     * @param listener 视频保存事件回调
     */
    public UVideoEditManager setVideoSaveListener(UVideoSaveListener listener)

    /**
     * 设置视频渲染相关事件回调
     *
     * @param listener 视频渲染事件回调，参见 {@link UVideoFrameListener}
     */
    public UVideoEditManager setVideoFrameListener(UVideoFrameListener listener)

    /**
     * 添加文字、图片贴纸
     *
     * @param sticker 贴纸对象，参见 {@link USticker}
     */
    public UVideoEditManager addSticker(USticker sticker)

    /**
     * 删除贴纸
     *
     * @param sticker 贴纸对象
     */
    public void removeSticker(USticker sticker)

    /**
     * 开始视频播放
     */
    public void start()

    /**
     * 结束视频播放
     */
    public void stop()

    /**
     * 恢复视频播放
     */
    public void resume()

    /**
     * 暂停视频播放
     */
    public void pause()

    /**
     * 释放视频编辑器资源
     */
    public void release()

    /**
     * 快进到指定位置
     *
     * @param timeMs 毫秒
     */
    public void seekTo(int timeMs)

    /**
     * 是否正在播放
     */
    public boolean isPlaying()

    /**
     * 是否正在录制当前播放画面
     */
    public boolean isRecording()

    /**
     * 开始当前播放画面录制
     */
    public boolean startRecord()

    /**
     * 结束当前播放画面录制
     */
    public void stopRecord()

    /**
     * 将当前录制片段合并成一个 mp4 文件
     *
     * @param outFile  保存文件路径
     * @param listener 保存事件监听
     */
    public void combineClip(String outFile, UVideoSaveListener listener)

    /**
     * 获取当前播放进度，单位：毫秒
     */
    public int getCurrentPosition()

    /**
     * 文件转码，转码参数通过 {@link UVideoEditManager#setAVOptions(UAVOptions)} 设置
     *
     * @param outFile 输出文件路径
     */
    public UVideoEditManager transcode(String outFile)

    /**
     * 文件转码，转码参数通过 {@link UVideoEditManager#setAVOptions(UAVOptions)} 设置
     *
     * @param outFile  输出文件路径
     * @param trimTime 转码文件截取时间段
     */
    public UVideoEditManager transcode(String outFile, UMediaTrimTime trimTime)

    /**
     * 保存当前视频文件编辑结果
     *
     * @param outFile 输出文件路径
     */
    public void save(String outFile)

    /**
     * 保存当前视频文件编辑结果
     *
     * @param outFile  输出文件路径
     * @param listener 保存事件监听
     */
    public void save(String outFile, UVideoFrameListener listener)

    /**
     * 取消保存或转码操作
     */
    public void cancelSave()

    /**
     * 截取视频帧
     *
     * @param callback 通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     */
    public void captureVideoFrame(UBitmapOutputCallback callback)

    /**
     * 截取视频帧
     *
     * @param callback   通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     * @param withFilter 是否包含滤镜处理
     */
    public void captureVideoFrame(UBitmapOutputCallback callback, boolean withFilter)

    public UVideoEditManager setMusicFile(String file)

    /**
     * 同时设置视频文件和背景音乐音量
     *
     * @param originVolume 视频文件音量
     * @param musicVolume  背景音乐音量
     */
    public void setVolume(float originVolume, float musicVolume)

    /**
     * 设置视频文件音量
     *
     * @param volume 0 ~ 1
     */
    public void setOriginVolume(float volume)

    /**
     * 设置背景音乐音量
     *
     * @param volume 0 ~ 1
     */
    public void setMusicVolume(float volume)

    /**
     * 设置背景音乐开始位置
     *
     * @param positionMs 单位：毫秒
     */
    public void setMusicPositionMs(int positionMs)

    /**
     * 设置视频播放事件监听
     *
     * @param listener 播放事件监听
     */
    public UVideoEditManager setVideoPlayerListener(UVideoPlayListener listener)

    /**
     * 设置视频播放事件监听
     *
     * @param listener       播放事件监听
     * @param callbackPeriod 播放进度回调间隔，单位：毫秒，默认 1000 毫秒回调一次
     */
    public UVideoEditManager setVideoPlayerListener(UVideoPlayListener listener, int callbackPeriod)

    /**
     * 设置视频剪辑时间段
     *
     * @param trimTime 剪辑时间段
     */
    public void setTrimTime(UMediaTrimTime trimTime)

    /**
     * 添加 MV 特效视频文件
     *
     * @param mvFile   mv file
     * @param maskFile mask file for alpha channel
     */
    public void setOverlayVideoFile(String mvFile, String maskFile)

    /**
     * 添加涂鸦
     */
    public void addPaintView(UPaintView paintView)

    /**
     * 删除涂鸦
     */
    public void removePaintView(UPaintView paintView)

    /**
     * 设置获取当前纹理输出 buffer
     * 说明：该接口会增加耗时，如无需要，请不有调用该接口
     *
     * @param buffer
     */
    public void setOutputBuffer(ByteBuffer buffer)

    /**
     * 视频画面裁剪
     *
     * @param x      视频 x 坐标，左上角为坐标原点
     * @param y      视频 y 坐标，左上角为坐标原点
     * @param width  裁剪图像宽度
     * @param height 裁剪图像宽度
     */
    public void setCropRegion(int x, int y, int width, int height)

    /**
     * 撤销画面裁剪
     */
    public void undoCropRegion()
```

> 说明：保存编辑文件包含两种模式：<p>
> 1、`save` 接口，离屏保存视频，保存的视频处理操作和当前预览的视频处理操作是分开的，速度更快；<p>
> 2、`startRecord` 和 `stopRecord` 接口，直接录制当前预览画面显示的内容，可以分别摘取多个片段，然后进行合成保存，适用于边看边录制的模式.

#### `UImageEditManager`

该类提供了图片合成视频以及相应的转场特效功能，具体接口如下：

```java
    /**
     * 初始化图片合成器
     */
    public void init(Context context, UImageRenderView renderView)

    /**
     * 设置图片文件路径列表, 设置后，自动开始预览播放
     *
     * @param imageList 文件列表
     */
    public void setImageList(List<String> imageList)

    /**
     * 暂停预览播放
     */
    public void pause()

    /**
     * 恢复预览播放
     */
    public void resume()

    /**
     * 设置背景音乐
     *
     * @param musicFile 音乐文件路径
     */
    public void setMusic(String musicFile)

    /**
     * 设置转场特效
     *
     * @param type 特效类别，参见 {@link UPhotoMovieType}
     */
    public void changeTransfer(UPhotoMovieType type)

    /**
     * 保存预览结果
     *
     * @param listener 保存事件监听
     */
    public void saveVideo(UVideoSaveListener listener)
```

#### `UViewRecordManager`

该类提供了录制指定区域视图的功能，具体接口如下：

```java
    /**
     * 初始化 view 录制对象
     *
     * @param renderView 渲染对象
     */
    public UViewRecordManager init(UTextureView renderView)

    /**
     * 设置输出文件路径
     */
    public void setRecordFile(String outFile)

    /**
     * 设置录制 LinearLayout 容器
     */
    public void setRecordView(ULinearLayout recordView)

    /**
     * 设置录制 RelativeLayout 容器
     */
    public void setRecordView(URelativeLayout recordView)

    /**
     * 设置音视频相关参数
     *
     * @param options 参数，参见：{@link UAVOptions}
     */
    public UViewRecordManager setAVOptions(UAVOptions options)

    /**
     * 设置视频渲染相关事件回调
     *
     * @param listener 视频渲染事件回调，参见 {@link UVideoFrameListener}
     */
    public UViewRecordManager setVideoFrameListener(UVideoFrameListener listener)

    /**
     * 设置音频采集相关事件回调
     *
     * @param listener 音频采集事件回调，参见 {@link UAudioFrameListener}
     */
    public UViewRecordManager setAudioFrameListener(UAudioFrameListener listener)

    /**
     * 设置录制事件回调
     *
     * @param listener 录制事件回调，参见 {@link URecordListener}
     */
    public UViewRecordManager setRecordListener(URecordListener listener)

    /**
     * 释放资源
     */
    public void release()

    /**
     * 是否正在录制
     */
    public boolean isRecording()

    /**
     * 开始录制片段
     */
    public boolean startRecord()

    /**
     * 结束录制片段
     */
    public void stopRecord()

    /**
     * 合成录制片段
     *
     * @param outFile  输出文件
     * @param listener 保存事件监听
     */
    public void combineClip(String outFile, UVideoSaveListener listener)

    /**
     * 回删最近录制片段
     */
    public void removeLastClip()

    /**
     * 删除所有录制片段
     */
    public void removeAllClips()

    /**
     * 截取视频帧
     *
     * @param callback 通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     */
    public void captureVideoFrame(UBitmapOutputCallback callback)

    /**
     * 截取视频帧
     *
     * @param callback   通过回调返回视频帧，参见 {@link video.movieous.engine.UBitmapOutputCallback}
     * @param withFilter 是否包含滤镜处理
     */
    public void captureVideoFrame(UBitmapOutputCallback callback, boolean withFilter)

    /**
     * 设置背景音乐
     *
     * @param file 背景音乐文件
     */
    public void setMusicFile(String file)

    /**
     * 设置原始音频音量
     *
     * @param volume 0 ~ 1
     */
    public void setOriginVolume(float volume)

    /**
     * 设置背景音乐音量
     *
     * @param volume 0 ~ 1
     */
    public void setMusicVolume(float volume)

    /**
     * 设置背景音乐开始位置
     *
     * @param positionMs 单位：毫秒
     */
    public void setMusicPositionMs(int positionMs)

    /**
     * 获取当前录制片段文件个数
     */
    public int getClipCount()

    /**
     * 获取当前录制片段总的时长，单位：毫秒
     */
    public int getClipsDurationMs()
```

#### `UMultiVideoRecordManager`

该类提供了视频合拍功能，具体接口如下：

```java
    /**
     * 设置合拍文件
     *
     * @param videoFile 合拍视频文件路径
     */
    public void setVideoFile(String videoFile)
```

其它接口与 `UVideoRecordManager` 接口相同

#### `UMediaUtil`

提取媒体文件信息辅助类，具体接口如下：

```java
    /**
     * 获取指定文件元信息
     *
     * @param input 媒体文件
     * @return 媒体信息，参见 {@link MediaUtil.Metadata}
     */
    public static MediaUtil.Metadata getMetadata(String input)

    /**
     * 异步方式获取视频文件指定范围的缩略图
     *
     * @param videoUri 视频文件路径
     * @param count    缩略图个数
     * @param begin    开始时间
     * @param end      结束时间
     * @param width    图片宽度
     * @param height   图片高度
     * @param callback 图片回调，参见：{@link SingleCallback<Bitmap, Long>}, 参数分别为图片和当前时间戳
     */
    public static void getVideoThumb(final Uri videoUri, final int count, final long begin, final long end, final int width, final int height, final SingleCallback callback)

    /**
     * 在线播放时，把 moov 移动到文件开始，加快开始播放速度
     *
     * @param inFile
     * @param outFile
     * @return 0：执行成功，其他：错误码
     */
    public static int fastStartMp4(String inFile, String outFile)
```

#### `UAVOptions`

音视频输出参数设置类，具体参数及接口定义如下：

```java
    /**
     * 视频图像宽度
     * 通过 setInteger 接口设置
     */
    public static final String KEY_VIDEO_WIDTH = "video-width";

    /**
     * 视频图像高度
     * 通过 setInteger 接口设置
     */
    public static final String KEY_VIDEO_HEIGHT = "video-height";

    /**
     * 视频帧率
     * 通过 setInteger 接口设置
     */
    public static final String KEY_VIDEO_FPS = "video-fps";

    /**
     * 视频编码码率
     * 通过 setInteger 接口设置
     */
    public static final String KEY_VIDEO_BITRATE = "video-bitrate";

    /**
     * 视频编码关键帧间隔
     * 通过 setInteger 接口设置
     */
    public static final String KEY_VIDEO_KEYFRAME_INTERVAL = "video-keyframe-interval";

    /**
     * 视频编码模式
     * 通过 setInteger 接口设置，参数为 BitrateMode 枚举值的索引
     */
    public static final String KEY_VIDEO_BITRATE_MODE = "video-bitrate-mode";

    /**
     * 视频编码 profile 等级
     * 通过 setInteger 接口设置，参数为 H264Profile 枚举值的索引
     */
    public static final String KEY_VIDEO_PROFILE = "video-profile";

    /**
     * 音频设备输入源: {@link android.media.MediaRecorder.AudioSource}
     * 通过 setInteger 接口设置，参数为 MediaRecorder.AudioSource 下定义的音频源
     */
    public static final String KEY_AUDIO_SOURCE = "audio-source";

    /**
     * 音频采样率, 44100 ... etc
     * 通过 setInteger 接口设置，参数为具体的音频采样率
     */
    public static final String KEY_AUDIO_SAMPLE_RATE = "audio-sample-rate";

    /**
     * 音频通道设置, 参见: {@link android.media.AudioFormat#CHANNEL_IN_MONO}
     * 通过 setInteger 接口设置，参数为 AudioFormat.CHANNEL_IN_MONO 或者 AudioFormat.CHANNEL_IN_STEREO
     */
    public static final String KEY_AUDIO_CHANNEL_CONFIG = "audio-channel-config";

    /**
     * 音频编码码率, 默认: 96 * 1000
     * 通过 setInteger 接口设置
     */
    public static final String KEY_AUDIO_BITRATE = "audio-bitrate";

    /**
     * 录制视频片段保存路径，默认 APP 的缓存目录
     * 通过 setString 接口设置
     */
    public static final String KEY_CACHE_DIR = "cache-dir";

    /**
     * SDK 定义编码尺寸等级, 默认: 480p_16_9
     * 通过 setInteger 接口设置，参数为 UConstants.VIDEO_RECORD_SIZE_LEVEL 枚举值的索引
     */
    public static final String KEY_RECORD_SIZE_LEVEL = "record-size-level";

    /**
     * 设置整型参数
     *
     * @param name  the name
     * @param value the value
     */
    public final UAVOptions setInteger(String name, int value)

    /**
     * 设置字符型参数.
     *
     * @param name  the name
     * @param value the value
     */
    public final UAVOptions setString(String name, String value)
```

#### `UVideoFrameListener` 视频渲染事件回调

```java
    /**
     * 在 surface 被创建或者重新创建时回调
     * 在 {@link android.opengl.GLSurfaceView.Renderer#onSurfaceCreated(GL10, EGLConfig)} 中调用.
     */
    default void onSurfaceCreated() {
    }

    /**
     * 在 surface 尺寸发生变化时回调
     * 在 {@link android.opengl.GLSurfaceView.Renderer#onSurfaceChanged(GL10, int, int)} 中调用
     *
     * @param width  surface 宽度
     * @param height surface 高度
     */
    default void onSurfaceChanged(int width, int height) {
    }

    /**
     * 在 surface 销毁时回调
     */
    default void onSurfaceDestroyed() {
    }

    /**
     * 渲染视频帧时回调
     * 在 {@link android.opengl.GLSurfaceView.Renderer#onDrawFrame(GL10)} 中调用
     *
     * @param texId     渲染视频帧的纹理.
     * @param texWidth  纹理图像宽度.
     * @param texHeight 纹理图像高度.
     * @return 经过处理后的视频纹理.
     */
    default int onDrawFrame(int texId, int texWidth, int texHeight) {
        return texId;
    }
```

#### `UAudioFrameListener` 音频帧回调接口

```java
    /**
     * 音频录制失败时回调
     * 可能原因：1、用户禁止使用当前音频设备；2、音频设备被占用；3、音频设备不存在；4、其它未知异常
     */
    default void onAudioRecordFailed(int errorCode) {
    }

    /**
     * 成功获取到音频帧时回调
     *
     * @param buffer 音频 pcm 数据
     * @param size   音频 pcm 数据尺寸
     * @param ptsUs  时间戳，单位：微秒
     */
    default void onAudioFrameAvailable(ByteBuffer buffer, int size, long ptsUs) {
    }
```

#### `URecordListener` 录制事件回调

```java
    /**
     * 调用startRecord后回调
     */
    default void onRecordStart() {
    }

    /**
     * 调用stopRecord后回调
     */
    default void onRecordStop() {
    }

    /**
     * 录制完成后回调
     */
    default void onRecordFinish(String path, long durationMs) {
    }

    /**
     * 当前录制中视频片段发生变化时回调
     *
     * @param totalDurationMs 总的录制时长，单位：毫秒
     * @param clipCount       视频片段文件个数
     */
    default void onClipChanged(long totalDurationMs, int clipCount) {
    }
```

#### `UVideoSaveListener` 保存视频事件回调

```java
    /**
     * 当前进度
     *
     * @param progress 范围 [0.0, 1.0], 如果进度未知，则为负值
     */
    default void onVideoSaveProgress(float progress) {
    }

    /**
     * 保存视频成功回调
     */
    default void onVideoSaveSuccess(String path) {
    }

    /**
     * 取消保存回调
     */
    default void onVideoSaveCancel() {
    }

    /**
     * 保存失败回调
     */
    default void onVideoSaveFail(int errorCode) {
    }
```

#### `UVideoPlayListener` 视频播放事件回调

```java
    /**
     * 播放器准备就绪回调
     */
    default void onPrepared() {
    }

    /**
     * 播放完成回调
     */
    default void onCompletion() {
    }

    /**
     * 当前播放进度回调, 回调间隔默认为 1 秒，用户可以修改该回调间隔
     *
     * @param position 播放进度, 单位：毫秒
     */
    default void onPositionChanged(int position) {
    }
```

#### `USticker` 文字、图片贴纸类

```java
    /**
     * 初始化贴纸对象，必须在实例化后第一个调用
     *
     * @param stickerType 贴纸类型，文字、图片...etc
     * @param width       贴纸宽度，不能超过视频宽度
     * @param height      贴纸高度，不能超过视频高度
     */
    public USticker init(StickerType stickerType, int width, int height)

    /**
     * 开始合成贴纸
     */
    public void start()

    /**
     * 停止合成贴纸
     */
    public void pause()

    /**
     * 设置贴纸位置, 视频图像的左上角为坐标原点
     */
    public USticker setPosition(int x, int y)

    /**
     * 设置贴纸角度
     *
     * @param angle
     */
    public USticker setAngle(int angle)

    /**
     * 设置贴纸开始时间
     */
    public USticker setDuration(int beginTimeMs, int durationMs)

    /**
     * 设置文字贴纸
     *
     * @param text      文字内容
     * @param textColor 文字颜色
     */
    public USticker setText(String text, int textColor)

     /**
     * 设置文字贴纸
     *
     * @param text      文字内容
     * @param textPaint 自定义文字样式
     */
    public USticker setText(String text, TextPaint textPaint)

    /**
     * 设置图片贴纸
     *
     * @param bitmap 图片，保持图片尺寸
     */
    public USticker setImage(Bitmap bitmap)

    /**
     * 设置图片贴纸
     *
     * @param bitmap 图片
     * @param width  宽度
     * @param height 高度
     * @return
     */
    public USticker setImage(Bitmap bitmap, int width, int height)
```

#### `UPaintView` 涂鸦视图

```java
    /**
     * 设置画笔颜色.
     *
     * @param color the color of paint.
     */
    public void setPaintColor(int color) {
        super.setPaintColor(color);
    }

    /**
     * 设置画笔尺寸
     *
     * @param size the size of paint.
     */
    public void setPaintSize(int size) {
        super.setPaintSize(size);
    }

    /**
     * 自定义画笔参数
     *
     * @param paint the custom paint.
     */
    public void setPaint(Paint paint) {
        super.setPaint(paint);
    }

    /**
     * 回删上一步涂鸦操作
     */
    public void undo() {
        super.undo();
    }

    /**
     * 清除所有涂鸦操作
     */
    public void clear() {
        super.clear();
    }
```

#### `UScreenRecordManager` 屏幕录制

```java
   /**
     * 初始化
     */
    public void init(Activity activity, String outputFile, UAVOptions options)

    /**
     * 设置录制过程事件回调
     */
    public void setRecordStateListener(URecordListener stateListener)

    /**
     * 请求屏幕录制权限
     */
    public void requestScreenRecord()

    /**
     * 获取授权结果
     */
    public boolean onActivityResult(int requestCode, int resultCode, Intent data)

    /**
     * 开始录制
     */
    public void startRecord()

    /**
     * 结束录制
     */
    public void stopRecord()
```

#### `UImageEditManager` 图片编辑

```java
    /**
     * 初始化
     *
     * @param preview 图片预览画面
     * @return
     */
    public UImageEditManager init(UTextureView preview)

    /**
     * 设置图片
     *
     * @param bitmap 图片
     * @return
     */
    public UImageEditManager setBitmap(Bitmap bitmap)

    /**
     * 设置图片文件
     *
     * @param filePath 图片文件路径
     * @return
     */
    public UImageEditManager setFilePath(String filePath)

    /**
     * 设置图片文件, 同时指定图片输出尺寸
     *
     * @param filePath 图片文件路径
     * @param width    指定图片宽度
     * @param height   指定图片高度
     * @return
     */
    public UImageEditManager setFilePath(String filePath, int width, int height)

    /**
     * 设置图片纹理数据回调
     *
     * @param listener
     * @return
     */
    public UImageEditManager setVideoFrameListener(UVideoFrameListener listener)

    /**
     * 保存图片
     *
     * @param callback 返回处理后 Bitmap
     */
    public void save(String outFile, UBitmapOutputCallback callback)

    /**
     * 释放资源，与 init 接口成对调用
     */
    public void release()

    /**
     * 添加文字、图片贴纸
     *
     * @param sticker 贴纸对象，参见 {@link USticker}
     */
    public UImageEditManager addSticker(USticker sticker)

    /**
     * 删除贴纸
     *
     * @param sticker 贴纸对象
     */
    public void removeSticker(USticker sticker)

    /**
     * 添加涂鸦
     */
    public void addPaintView(UPaintView paintView)

    /**
     * 删除涂鸦
     */
    public void removePaintView(UPaintView paintView)
```

### 自定义对象

#### 自定义渲染 VIEW

- `UTextureView` 继承自 `TextureView`，`USurfaceView` 继承自 `SurfaceView`, 都可以作为录制和编辑处理视频的渲染视图。由于 `TextureView` 可以作为普通视图对待，所以，推荐用 `UTextureView` 作为渲染预览视图。

- 通过 `UFitViewHelper` 可以设置渲染视图的缩放规则和旋转角度等信息，具体定义如下：

```java
   public enum ScaleType {
        FIT_CENTER, // 居中显示全部画面，上下或者左右可能留黑
        FIT_WIDTH,  // 填满宽度
        FIT_HEIGHT, // 填满高度
        CENTER_CROP // 居中裁剪铺满画面
    }

    /**
     * 设置缩放规则
     *
     * @param scaleType 缩放规则, 默认：居中显示全部画面, ScaleType.FIT_CENTER
     */
    public void setScaleType(ScaleType scaleType)

    /**
     * 设置宽高比及最大宽度最大高度
     * maxWidth 和 maxHeight 有一个设置为0时表示 MatchParent
     *
     * @param ratio     宽高比
     * @param maxWidth  最大宽度
     * @param maxHeight 最大高度
     * @return 是否应该调用requestLayout刷新视图
     */
    public boolean setAspectRatio(float ratio, int maxWidth, int maxHeight)

    /**
     * 设置顺时针旋转90度的次数
     * 取值-3~0，0~3，表示旋转-270~0，0~270度
     *
     * @param numOfTimes 旋转次数
     * @return 是否应该调用requestLayout刷新视图
     */
    public boolean setRotate90Degrees(int numOfTimes)
```

#### UConstants

```java
   /**
     * The enum h.264 profile mode
     */
    public enum H264Profile {
        BASELINE,
        MAIN,
        HIGH
    }

    /**
     * The enum bitrate mode
     */
    public enum BitrateMode {
        /**
         * In order to improve the video quality, the real bitrate maybe higher than the requests.
         **/
        QUALITY_PRIORITY,

        /**
         * In order to control the request bitrate accurately, the video quality maybe not good.
         **/
        BITRATE_PRIORITY,
    }

    /**
     * The enum video record size level
     */
    public enum VIDEO_RECORD_SIZE_LEVEL {
        /**
         * 240x240
         */
        SIZE_240P_1_1,
        /**
         * 320x240
         */
        SIZE_240P_4_3,
        /**
         * 352x352
         */
        SIZE_352P_1_1,
        /**
         * 640x352
         */
        SIZE_352P_16_9,
        /**
         * 360x360
         */
        SIZE_360P_1_1,
        /**
         * 480x360
         */
        SIZE_360P_4_3,
        /**
         * 640x360
         */
        SIZE_360P_16_9,
        /**
         * 480x480
         */
        SIZE_480P_1_1,
        /**
         * 640x480
         */
        SIZE_480P_4_3,
        /**
         * 848x480
         */
        SIZE_480P_16_9,
        /**
         * 544x544
         */
        SIZE_544P_1_1,
        /**
         * 720x544
         */
        SIZE_544P_4_3,
        /**
         * 720x720
         */
        SIZE_720P_1_1,
        /**
         * 960x720
         */
        SIZE_720P_4_3,
        /**
         * 1280x720
         */
        SIZE_720P_16_9,
        /**
         * 1088x1088
         */
        SIZE_1080P_1_1,
        /**
         * 1440x1088
         */
        SIZE_1080P_4_3,
        /**
         * 1920x1088
         */
        SIZE_1080P_16_9,
    }

    public static final int[][] VIDEO_ENCODE_SIZE = {
            {240, 240},
            {320, 240},
            {352, 352},
            {640, 352},
            {360, 360},
            {480, 360},
            {640, 360},
            {480, 480},
            {640, 480},
            {848, 480},
            {544, 544},
            {720, 544},
            {720, 720},
            {960, 720},
            {1280, 720},
            {1088, 1088},
            {1440, 1088},
            {1920, 1088}
    };

    /**
     * The enum Camera facing id.
     */
    public enum CAMERA_FACING_ID {
        /**
         * back camera facing id.
         */
        BACK,
        /**
         * front camera facing id.
         */
        FRONT,
        /**
         * 3rd camera facing id.
         */
        THIRD
    }

    /**
     * The enum Camera preview size ratio.
     */
    public enum CAMERA_PREVIEW_SIZE_RATIO {
        /**
         * Ratio 4:3
         */
        RATIO_4_3,
        /**
         * Ratio 16:9
         */
        RATIO_16_9
    }

    /**
     * The enum Camera preview size level.
     */
    public enum CAMERA_PREVIEW_SIZE_LEVEL {
        /**
         * 120P
         */
        SIZE_120P,
        /**
         * 240P
         */
        SIZE_240P,
        /**
         * 360P
         */
        SIZE_360P,
        /**
         * 480P
         */
        SIZE_480P,
        /**
         * 720P
         */
        SIZE_720P,
        /**
         * 960P
         */
        SIZE_960P,
        /**
         * 1080P
         */
        SIZE_1080P,
        /**
         * 1200P
         */
        SIZE_1200P,
    }

    public static final int[] PREVIEW_SIZE_LEVEL = {
            120, 240, 360, 480, 720, 960, 1080, 1200
    };
```

#### UErrorCode

自定义错误码，包含如下内容：

| 错误码                               | 错误值  | 说明             |
| --------------------------------- | ---- | ------------------- |
| ERROR_IO_EXCEPTION                | 20101    | IO 错误               |
| ERROR_SIGN_INVALID                | 20102    | SDK 授权签名无效       |
| ERROR_JSON_INVALID                | 20103    | JSON 字符串无效        |
| ERROR_HTTP_SOCKET_TIMEOUT         | 20104    | HTTP Socket 超时      |
| ERROR_HTTP_IO_EXCEPTION           | 20105    | HTTP IO 异常          |
| ERROR_HTTP_RESPONSE_EXCEPTION     | 20106    | HTTP 返回码异常        |
| ERROR_WRONG_STATE                 | 20001    | 状态错误              |
| ERROR_NO_CLIPS                    | 20002    | 无片段                |
| ERROR_MUXER_STOP_FAILED           | 20003    | MUXER 停止失败         |
| ERROR_SET_CAMERA_FAILED           | 20004    | 设置摄像头失败          |
| ERROR_SET_MICROPHONE_FAILED       | 20005    | 设置麦克风失败          |
| ERROR_SET_VIDEO_ENCODER_FAILED    | 20006    | 设置视频编码器失败       |
| ERROR_SET_AUDIO_ENCODER_FAILED    | 20007    | 设置音频编码器失败       |
| ERROR_UNAUTHORIZED                | 20008    | 鉴权失败               |
| ERROR_UNSUPPORTED_ANDROID_VERSION | 20009    | 不支持的安卓版本         |
| ERROR_INVALID_ARG                 | 20010    | 无效的参数              |
| ERROR_DIFFERENT_AUDIO_PARAMS      | 20011    | 音频参数不一致           |
| ERROR_MISS_DYNAMIC_LIBRARY        | 20012    | 缺少动态库文件           |
| ERROR_NO_VIDEO_TRACK              | 20013    | 未发现视频              |
| ERROR_SAME_FILE_PATH              | 20014    | 源文件和目标文件路径相同  |
| ERROR_LOW_MEMORY                  | 20015    | 内存不足                |
| ERROR_MULTI_CODEC_WRONG           | 20016    | 该设备不支持多个 MediaCodec 同时运行     |
| ERROR_SET_VIDEO_DECODER_FAILED    | 20017    | 设置视频解码器失败       |
| ERROR_MUXER_START_FAILED          | 20018    | MUXER 启动失败         |
| ERROR_DURATION_TOO_SHORT          | 20019    | 文件时长太短            |
