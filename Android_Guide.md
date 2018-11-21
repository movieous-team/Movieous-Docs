# 接口设计

## 核心类

短视频核心类有 2 个，分别是 `UVideoRecord` 、 `UVideoEdit`，分别完成音视频的录制和后期编辑处理工作。

### UShortVideoEnv

`UShortVideoEnv` 包含了 SDK 初始化的接口，必须在 APP 启动时调用，方法列表如下：

```java
/**
 * Initialize the environment of SDK.
 * <p>
 * You must invoke it when application launch, otherwise, {@link IllegalStateException}
 * will be thrown when invoke the {@link UVideoRecord} or {@link UVideoEdit}
 *
 * @param context the context of the Application
 * @param sign    the Movieous sign
 */
public static void init(Context context, String sign)

/**
 * Set log level.
 *
 * @param level the level，See also {@link com.movieous.base.Log#V}/{@link Log#D}/{@link Log#I}/{@link Log#W}/{@link Log#E}
 */
public static void setLogLevel(int level)
```

### UVideoRecord

`UVideoRecord` 包含了录制短视频需要的接口，方法列表如下：

```java
/**
 * init the recorder
 *
 * @param preview          the GLSurfaceView
 * @param cameraParam      the camera param
 * @param microphoneParam  the microphone param
 * @param videoEncodeParam the video encode param
 * @param audioEncodeParam the audio encode param
 * @param beautyParam      the face beauty param
 * @param recordParam      the record param
 */
public void init(GLSurfaceView preview,
                 UCameraParam cameraParam,
                 UMicrophoneParam microphoneParam,
                 UVideoEncodeParam videoEncodeParam,
                 UAudioEncodeParam audioEncodeParam,
                 UBeautyParam beautyParam,
                 URecordParam recordParam)

/**
 * Resume.
 */
public void resume()

/**
 * Pause.
 */
public void pause()

/**
 * Destroy.
 */
public void destroy()

/**
 * Set filter file path
 *
 * @param filterPath   filter file path
 * @param isAssetFile  whether asset file or sdcard file
 */
public void setFilterFile(String filterPath, boolean isAssetFile)

/**
 * Set watermark.
 *
 * @param watermarkParam the watermark param
 */
public void setWatermark(UWatermarkParam watermarkParam)

/**
 * Start record clip.
 */
public boolean start()

/**
 * Stop record clip.
 */
public boolean stop()

/**
 * Remove last clip.
 */
public boolean removeLastClip()

/**
 * Remove all of the clips.
 */
public boolean removeAllClips()

/**
 * Merge clips.
 *
 * @param listener the listener
 */
public void mergeClips(USaveFileListener listener)

/**
 * cancel previous clips merging
 */
public void cancelMerge()

/**
 * set music file in sd card
 *
 * @param filePath the music file path
 */
public void setMusicFile(String filePath)

/**
 * set music position
 *
 * @param position the position
 */
public void setMusicStartPosition(int position)

/**
 * Sets camera preview listener.
 *
 * @param listener the listener
 */
public final void setCameraPreviewListener(UCameraPreviewListener listener)

/**
 * Sets video frame listener.
 *
 * @param listener the listener
 */
public final void setVideoFrameListener(UVideoFrameListener listener)

/**
 * set video speed of the recording, only support multiple of 2 or multiple of 1/2 for now.
 *
 * @param recordSpeed  4, 2, 0.5, 0.25 etc
 */
public void setVideoSpeed(double recordSpeed)

/**
 * Sets audio frame listener.
 *
 * @param listener the listener
 */
public final void setAudioFrameListener(UAudioFrameListener listener)

/**
 * Sets record state listener.
 *
 * @param listener the listener
 */
public final void setRecordStateListener(URecordStateListener listener)

/**
 * Mute microphone
 */
public void mute(boolean isMute)

/**
 * Switch camera.
 */
public void switchCamera()

/**
 * specify the focus area size on previous set preview with the corresponding central x and y coordinate.
 *
 * @param focusAreaWidth    area width
 * @param focusAreaHeight   area height
 * @param focusAreaCenterX  area center x
 * @param focusAreaCenterY  area center y
 */
public void manualCameraFocus(int focusAreaWidth, int focusAreaHeight, int focusAreaCenterX, int focusAreaCenterY)

/**
 * capture the present preview frame
 *
 * @param listener
 */
public void captureVideoFrame(UCaptureVideoFrameListener listener)

/**
 * set exposure compensation to adjust brightness
 *
 * @param value to be set value
 */
public void setExposureCompensation(int value)

/**
 * set zoom ratio of camera, must be called {@link #getCameraZoomRatios()} first
 *
 * @param zoom the value in {{@link #getCameraZoomRatios()}}
 */
public void setCameraZoomRatio(float zoom)

/**
 * get available zoom ratio values, sorted from small to large
 *
 * @return null if zoom not supported
 */
public List<Float> getCameraZoomRatios()

/**
 * set the listener for focus related events
 *
 * @param focusListener the listener
 */
public void setCameraFocusListener(UCameraFocusListener focusListener)

/**
 * Update builtin face beauty param.
 *
 * @param beautyParam the face beauty param
 */
public void updateBeautyParam(UBeautyParam beautyParam)

/**
 * Turn flashlight on or off. Set the flashlight to FLASH_MODE_TORCH mode.
 * <p>
 * The following situations will cause the failed invocation:
 * <ol>
 * <li> Camera is unactivated
 * <li> The handset don't support flashlight
 * <li> The handset don't support FLASH_MODE_TORCH mode
 * </ol>
 *
 * @return true if operate successfully, otherwise, false.
 */
public boolean setCameraFlashEnabled(boolean enabled)

```

### UVideoEdit

`UVideoEdit` 包含了编辑短视频需要的接口，方法列表如下：

```java
/**
 * Constructor of UVideoEdit
 *
 * @param preview Play preview screen
 * @param param   Video editing parameters
 */
public UVideoEdit(GLSurfaceView preview, UVideoEditParam param)

/**
 * Sets video frame listener.
 */
public final void setVideoFrameListener(UVideoFrameListener listener)

/**
 * Start playback
 */
public void startPlay()

/**
 * Resume play
 */
public void resumePlay()

/**
 * Pause play
 */
public void pausePlay()

/**
 * Stop play
 */
public void stopPlay()

/**
 * Setting encoding parameters
 *
 * @param param video encode parameters
 */
public void setVideoEncodeParam(UVideoEncodeParam param)

/**
 * whether or not looping play
 */
public void setPlayLoop(boolean loop)

/**
 * set display mode of preview
 */
public void setDisplayMode(UDisplayMode displayMode)

/**
 * set speed of the video, only support multiple of 2 or multiple of 1/2 for now.
 *
 * @param speed 4, 2, 0.5, 0.25 etc
 */
public void setVideoSpeed(double speed)

/**
 * seek to target position
 */
public void seekTo(int millisecond)

/**
 * get current video position
 *
 * @return millisecond
 */
public int getCurrentPosition()

/**
 * set video mix file, blend a video with current one
 *
 * @param mvFile   mv file
 * @param maskFile mask file for alpha channel
 */
public void setVideoMixFile(String mvFile, String maskFile)

/**
 * Sets video save listener.
 */
public void setVideoSaveListener(USaveFileListener listener)

/**
 * Sets video player listener.
 */
public void setVideoPlayListener(UVideoPlayListener listener)

/**
 * Set filter file path
 *
 * @param filterPath  filter file path, null to clear filter
 * @param isAssetFile whether asset file or sdcard file
 */
public void setFilterFile(String filterPath, boolean isAssetFile)

/**
 * Sets watermark.
 *
 * @param watermarkParam the watermark param
 */
public void setWatermark(UWatermarkParam watermarkParam)

/**
 * set audio mix file
 *
 * @param file the mix audio file
 */
public void setAudioMixFile(String file)

/**
 * set audio mix asset
 *
 * @param afd the AssetFileDescriptor
 */
public void setAudioMixAssetFile(AssetFileDescriptor afd)

/**
 * get audio mix file duration
 *
 * @return duration: ms
 */
public int getAudioMixFileDuration()

/**
 * set audio mix start position
 *
 * @param startTime start position, ms
 * @param endTime   end position
 */
public void setAudioMixFileDuration(long startTime, long endTime)

/**
 * Set origin audio volume
 *
 * @param volume origin audio volume
 */
public void setAudioVolume(float volume)

/**
 * Set mix audio volume
 *
 * @param volume mix audio volume
 */
public void setAudioMixVolume(float volume)

/**
 * set whether mix audio looping or not
 *
 * @param isLooping
 */
public void setAudioMixLooping(boolean isLooping)

/**
 * mute origin audio
 */
public void muteOriginAudio(boolean isMute)

/**
 * Save the edited video file path with video frame callback.
 */
public void save(UVideoFrameListener listener)

/**
 * cancel previous video saving
 */
public void cancelSave()

/**
 * get source video duration in Ms
 *
 * @return ms
 */
public long getMediaFileDurationMs()

/**
 * Add the UTextView to the preview screen.
 */
public void addTextView(UTextView textView)

/**
 * Remove the UTextView from it's parent.
 */
public void removeTextView(UTextView textView)

/**
 * Add the UPaintView to the preview screen.
 */
public void addPaintView(UPaintView paintView)

/**
 * Remove the UPaintView from it's parent.
 */
public void removePaintView(UPaintView paintView)

/**
 * Add the UImageView to the preview screen.
 */
public void addImageView(UImageView imageView)

/**
 * Remove the UImageView from it's parent.
 */
public void removeImageView(UImageView imageView)

/**
 * Set the view's display duration time, the view must be added.
 *
 * @param view       the added view, can be UImageView, UTextView and UPaintView
 * @param beginMs    time of the view begin to display
 * @param durationMs duration of the view display
 */
public void setViewDuration(View view, long beginMs, long durationMs)

/**
 * Set duration of the save video file.
 *
 * @param beginMs the beginning timestamp
 * @param endMs   the ending timestamp
 */
public void setVideoDuration(long beginMs, long endMs)
```

## 参数类

### UCameraParam

`UCameraParam` 类用于配置摄像头采集的相关参数，包含以下方法：

```java
/**
 * Set camera id.
 * default: CAMERA_FACING_ID.BACK
 */
public UCameraParam setCameraId(CAMERA_FACING_ID cameraId)

/**
 * Set camera preview size ratio.
 * default: CAMERA_PREVIEW_SIZE_RATIO.RATIO_16_9;
 */
public UCameraParam setCameraPreviewSizeRatio(CAMERA_PREVIEW_SIZE_RATIO ratio)

/**
 * Set camera preview size level.
 * default: CAMERA_PREVIEW_SIZE_LEVEL.SIZE_480P
 */
public UCameraParam setCameraPreviewSizeLevel(CAMERA_PREVIEW_SIZE_LEVEL level)
```

### 7.2.2 UMicrophoneParam

`UMicrophoneParam` 类用于配置麦克风采集的相关参数，包含以下方法：

```java
/**
 * Set audio source. see {@link android.media.MediaRecorder.AudioSource}
 * default: MediaRecorder.AudioSource.MIC
 */
public UMicrophoneParam setAudioSource(int source)

/**
 * Set sample rate.
 * default: 44100
 */
public UMicrophoneParam setSampleRate(int sampleRate)

/**
 * Set channel config. see {@link android.media.AudioFormat}
 * default: AudioFormat.CHANNEL_IN_MONO
 */
public UMicrophoneParam setChannelConfig(int channelConfig)

/**
 * Set audio format. see {@link android.media.AudioFormat}
 * default: AudioFormat.ENCODING_PCM_16BIT
 */
public UMicrophoneParam setAudioFormat(int audioFormat)

 /**
  * Set bluetooth sco enabled.
  *
  * @param enabled whether enabled or not
  * @return the bluetooth sco enabled
 */
public UMicrophoneParam setBluetoothScoEnabled(boolean enabled)

/**
 * Set noise suppressor enabled.
 *
 * @param enabled whether enabled or not
 * @return whether noise suppressor setting successful or not
 */
public boolean setNsEnabled(boolean enabled)

/**
 * Set acoustic echo canceler enabled.
 *
 * @param enabled whether enabled or not
 * @return whether is acoustic echo canceler setting successful or not
 */
public boolean setAecEnabled(boolean enabled)
```

### UVideoEncodeParam

`UVideoEncodeParam` 类用于配置视频编码的相关参数，包含以下方法：

```java
/**
 * Set preferred custom video encoding size
 *
 * @param width  encode width
 * @param height encode height
 */
public UVideoEncodeParam setPreferredEncodingSize(int width, int height)

/**
 * Set preset video encoding size level.
 *
 * @param level See also {@link VIDEO_ENCODING_SIZE_LEVEL}
 */
public UVideoEncodeParam setEncodingSizeLevel(VIDEO_ENCODING_SIZE_LEVEL level)

/**
 * Set video encoding fps.
 *
 * @param fps frame per second
 */
public UVideoEncodeParam setEncodingFps(int fps)

/**
 * Set video bitrate
 */
public UVideoEncodeParam setEncodingBitrate(int bitrate)

/**
 * Set key frame interval
 *
 * @param keyFrameIntervalCount key frame interval count
 */
public UVideoEncodeParam setKeyFrameInterval(int keyFrameIntervalCount)
```

### UAudioEncodeParam

`UAudioEncodeParam` 类用于配置音频编码的相关参数，包含以下方法：

```java
/**
 * Set audio sample rate. default: 44100
 */
public UAudioEncodeParam setSampleRate(int sampleRate)

/**
 * Set audio channels. default: 1
 * 
 * @param channelCount audio channel count
 */
public UAudioEncodeParam setChannels(int channelCount)

/**
 * Set audio encoding bitrate. default: 44100
 */
public UAudioEncodeParam setBitrate(int bitrate)
```

### URecordParam

`URecordParam` 类用于配置录制参数，包含以下方法：

```java
/**
 * Set max record duration. default: 10 * 1000 ms
 *
 * @param durationMs duration ms
 */
public URecordParam setMaxRecordDuration(long durationMs)

/**
 * Set record clip cache dir.
 */
public URecordParam setClipCacheDir(File dir)

/**
 * Set record clip cache dir.
 */
public URecordParam setClipCacheDir(String dir)

/**
 * Set record file save path
 */
public URecordParam setRecordFilePath(String path)

/**
 * Set display mode. default: {@link UDisplayMode#FULL}
 *
 * @param displayMode can be set {@link UDisplayMode#FULL} or {@link UDisplayMode#FIT}
 */
public URecordParam setDisplayMode(UDisplayMode displayMode)

/**
 * Set change record speed enabled or not.
 */
public URecordParam setChangeRecordSpeedEnabled(boolean enabled)
```

### UVideoEditParam

`UVideoEditParam` 类用于配置视频编辑参数，包含以下方法：

```java
/**
 * Set source media file path.
 */
public UVideoEditParam setMediaFilepath(String filepath)

/**
 * Set save file path.
 */
public UVideoEditParam setSaveFilepath(String destFilePath)

/**
 * Whether keep origin file or not. default: true
 */
public UVideoEditParam setKeepOriginFileEnabled(boolean isKeepEnabled)
```

### UBeautyParam

`UBeautyParam` 类用于配置内置美颜参数，包含以下方法：

```java
/**
 * Whether builtin face beauty enabled or not. default: false
 */
public void setEnable(boolean enable)

/**
 * Set face beauty level. default: 0.8f
 *
 * @param beautyLevel the beauty level, range: 0.0f ~ 1.0f
 */
public void setBeautyLevel(float beautyLevel)
```

## 回调类

### UVideoFrameListener

该接口用于回调相机/编辑预览视频相关的事件和数据，方法如下：

```java
public interface UVideoFrameListener {
    /**
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onSurfaceCreated(GL10, EGLConfig)} invoked.
     */
    void onSurfaceCreated();

    /**
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onSurfaceChanged(GL10, int, int)}.
     *
     * @param width  the width of the surface
     * @param height the height of the surface
     */
    void onSurfaceChanged(int width, int height);

    /**
     * To be called after surface destroyed
     */
    void onSurfaceDestroy();

    /**
     * To be called in {@link android.opengl.GLSurfaceView.Renderer#onDrawFrame(GL10)}.
     *
     * @param texId           the texture ID of Camera SurfaceTexture object to be rendered.
     * @param texWidth        width of the drawing surface in pixels.
     * @param texHeight       height of the drawing surface in pixels.
     * @param timestampNs     timestamp of this frame in Ns.
     * @param transformMatrix when NOT specify the callback texture to be OES, it will be Identity matrix.
     * @return the texture ID of the newly generated texture to be assigned to the SurfaceTexture object.
     */
    int onDrawFrame(int texId, int texWidth, int texHeight, long timestampNs, float[] transformMatrix);
}
```

### UAudioFrameListener

该接口用于回调录制/编辑音频相关的事件和数据，方法如下：

```java
public interface UAudioFrameListener {
    /**
     * To be called when audio frame recording failed
     */
    void onAudioRecordFailed(int errorCode);

    /**
     * To be called when audio frame available
     *
     * @param data        the pcm data
     * @param timestampNs the timestamp of the frame in Ns
     */
    void onAudioFrameAvailable(byte[] data, long timestampNs);
}
```

### USaveFileListener

该接口用于回调保存录制/编辑文件相关的事件，方法如下：

```java
public interface USaveFileListener {
    /**
     * To be called when save video success.
     *
     * @param destFile the edited file
     */
    void onSaveFileSuccess(String destFile, UVideoEncodeParam videoEncodeParam);

    /**
     * To be called when save video failed.
     *
     * @param errorCode the error code
     */
    void onSaveFileFailed(int errorCode);

    /**
     * To be called when save video canceled
     */
    void onSaveFileCanceled();

    /**
     * To be called when save video progress update
     *
     * @param percentage
     */
    void onSaveFileProgress(float percentage);
}
```

### URecordStateListener

该接口用于回调录制过程中相关的事件，方法如下：

```java
public interface URecordStateListener {
    /**
     * To be called when record ready.
     */
    void onReady();

    /**
     * To be called when record error.
     */
    void onError(int code);

    /**
     * To be called when record real start, after encoders started.
     */
    void onRecordStarted();

    /**
     * To be called when record real stop, after encoders stopped.
     */
    void onRecordStopped();

    /**
     * To be called when record clip count changed.
     *
     * @param totalDurationMs the total duration
     * @param clipCount       the clip count
     */
    void onClipCountChanged(long totalDurationMs, int clipCount);

    /**
     * To be called when record finished.
     */
    void onRecordFinish();
}
```

### UVideoPlayListener

该接口用于回调视频编辑播放相关的事件，方法如下：

```java
public interface UVideoPlayListener {
    /**
     * To bo called when play position changed
     *
     * @param position current play position, ms
     */
    void onPositionChanged(int position);

    /**
     * To be called when the end of a media source is reached during playback.
     */
    void onCompletion();
}
```

### UCameraFocusListener

该接口用于回调 Camera 对焦相关的事件，方法如下：

```java
public interface UCameraFocusListener {
    /**
     * To bo called whether manual focus start success or not.
     */
    void onManualFocusStart(boolean result);

    /**
     * To bo called whether manual focus stop success or not.
     */
    void onManualFocusStop(boolean result);

    /**
     * To be called when ongoing manual focus canceled, because another manual focus triggered.
     */
    void onManualFocusCancel();

    /**
     * To be called when continuous auto focus start, only trigger when selected focus mode as FOCUS_MODE_CONTINUOUS_VIDEO or FOCUS_MODE_CONTINUOUS_PICTURE
     */
    void onAutoFocusStart();

    /**
     * To be called when continuous auto focus stop, only trigger when selected focus mode as FOCUS_MODE_CONTINUOUS_VIDEO or FOCUS_MODE_CONTINUOUS_PICTURE
     */
    void onAutoFocusStop();
}
```

## 自定义对象


### UErrorCode

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

