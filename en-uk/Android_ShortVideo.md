# Android SDK

##  Release Note

- 1.0.0
  - Release ushortvideo-1.0.0.jar
  - Release libcore.so
  - Release libmuxer.so
  - Release libamix.so
  - Release libencoder.so
  - Release assets
  - Audio/Video Acquisition
  - Audio/Video encoding（H.264 + AAC）
  - Real-time beautificaion, Filter
  - Support beatification and Filter from the third part 
  - Customized recording duration, bitrate, resolution
  - Support recording size as 1:1
  - Breakpoint recording
  - Delete the recorded clip
  - Place effects
  - Background replacement
  - Gesture recognition
  - Expression recognition
  - Video format save as Mp4
  - Supports the mainstream architecture (armv7, arm64, x86)

# Quict start

## Statement for readers

This document is a technical document, requirements asked for readers as below:

- Basic Android development capabilities
- Plan to integrate the Movieous Short Video SDK

## How to install
- Copy the `jar` package from the `release` directory to the `libs` directory of the project.
- Copy the contents of the `assets` folder in the `release` directory to the `assets` directory.

## Design rules

The interface design of the SDK follows principles as below :

- Interface classes start with `U`
- Parameter classes are named after `UxxxParam`
- Callback classes are named after `UxxxListener`

## Core interface

### Interface classes

| Class name                      | Description       | Comments              |
| :------------------------ | ----------- | ---------------- |
| UVideoRecord              | Responsible for video recording      | NO |
| UVideoEdit                | Responsible for video editing     | NO |

### Parameter classes

| Class name                       | Description        | Comments              |
| :------------------------ | ----------- | ---------------- |
| URecordParam              | Recording file related parameters    | NO |
| UCameraParam              | Camera Acquisition parameter    | NO |
| UMicrophoneParam          | Microphone acquisition parameters      | NO |
| UVideoEncodeParam         | Video encoding parameter        | NO |
| UAudioEncodeParam         | Audio encoding parameters       | NO |
| UVideoEditParam           | Edit file related parameters     | NO |

### Callback classes 

| Class name                      | Description        | Comments                |
| :------------------------ | ----------- | ---------------- |
| URecordStateListener      | Recording process status callback    | NO |
| USaveFileListener         | File save process information callback    | NO |
| UVideoFrameListener       | Video data callback       | Texture data, support for third-party effects processing |
| UAudioFrameListener       | Audio data callback         | PCM data，support audio processing |


## Development environment

- Android Studio Integrated IDE development platform [Download](http://developer.android.com/intl/zh-cn/sdk/index.html)
- Android development SDK，[Download](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)

## System requirements

- Android 4.4 (API 19) and later

## Document descriptions

The SDK mainly contains jar packages, dynamic libraries, and related filter resource files, as described below:

| FIle name                           | Function      | Size   | Comments          |
| ------------------------------ | ------- | ----- | ----------- |
| ushortvideo-x.x.x.jar          | Core library    | 292KB | Must rely on       |
| libcore.so                     | Core library    |  78KB | Must rely on       |
| libmuxer.so                    | Core library    | 802KB | Must rely on        |
| libamix.so                     | Mix-audio module   | 404KB | Can be dropped if there is no Mix-audio function |
| libencoder.so                  | Soft-encoding module | 3.0MB | Can be dropped if there is no soft-encoding function |
| assets                         | Built-in beautification filter | 6.2KB | Can be dropped if there is no built-in beautification function |

## Add dependency

Open `build.gradle` under the project directory and add the following dependencies:

```java
dependencies {
    implementation files('libs/ushortvideo-x.x.x.jar')
}
```

## Add permissions

Add permissions in AndroidManifest.xml:

```java
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

## Initialize the SDK

```java
// MOVIEOUS_SIGN，generated when the APP is created in the Movieous backstage, it needs to be passed to the SDK for authentication at initialization time.
UShortVideoEnv.init(context, MOVIEOUS_SIGN)
```

## Video recording

### Create a recording object class

```java
UVideoRecord mVideoRecorder = new UVideoRecord();
// Setup the recording status callback
mVideoRecorder.setRecordStateListener(this);
// Setup the video data callback
mVideoRecorder.setVideoFrameListener(this);
```

### Create recording parameter class

The SDK provides a wealth of recording parameters that can be configured in the following ways:

```java
// Camera acquisition parameters
UCameraParam cameraParam = new UCameraParam();
// Microphone acquisition parameters
UMicrophoneParam microphoneParam = new UMicrophoneParam();
// Video encoding parameter
UVideoEncodeParam videoEncodeParam = new UVideoEncodeParam(getContext());
videoEncodeParam.setEncodingSizeLevel(UVideoEncodeParam.VIDEO_ENCODING_SIZE_LEVEL.SIZE_720P_16_9);
videoEncodeParam.setEncodingBitrate(Constants.DEFAULT_ENCODING_BITRATE);
// Audio encoding parameter
UAudioEncodeParam audioEncodeParam = new UAudioEncodeParam();
// Recording parameters
URecordParam recordParam = new URecordParam();
recordParam.setMaxRecordDuration(Constants.DEFAULT_MAX_RECORD_DURATION);
recordParam.setVideoCacheDir(Constants.VIDEO_STORAGE_DIR);
recordParam.setVideoFilePath(Constants.RECORD_FILE_PATH);
```

### Create preview window

The video preview window is `GLSurfaceView`, which can be created in an XML layout file or dynamically created by code.

### Configuring recording parameters

Configure the recording parameters into the SDK by initializing the interface `init`:

```java
mVideoRecorder.init(preview, cameraParam, microphoneParam, videoEncodeParam, audioEncodeParam, recordParam);
```

### The life cycle

In the lifecycle callback of `Activity` or `Fragment`, it need to call the method corresponding to `UVideoRecord`:

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

### Video recording

Once the above configuration is complete, you can start recording your video.

```java
// Start recording a video clip
mVideoRecorder.start();
// Stop recording
mVideoRecorder.stop();
// Combine all video clips recorded this time
mVideoRecorder.mergeClips(this);
```

<a id="6.3"></a>

## Video editing

### Create parameter class

Configure video editing parameters with `UVideoEditParam`:

```java
UVideoEditParam videoEditParam = new UVideoEditParam();
// Video file path to be processed
videoEditParam.setMediaFilepath(mediaFilepath);
// Processed file save path
videoEditParam.setSaveFilepath(saveFilepath);
```

### Create the video editing object

```java
// Preview for preview rendering video
UVideoEdit mVideoEditor = new UVideoEdit(preview, videoEditParam);
mVideoEditor.setVideoFrameListener(this);
mVideoEditor.setVideoSaveListener(this);
```

### Preview effect

```java
// Start playback
mVideoEditor.startPlay();
// Restore playback
mVideoEditor.resumePlay();
// Pause playback
mVideoEditor.pausePlay();
// Stop playback
mVideoEditor.stopPlay();
```

### Special effects processing

In the process of preview playback, various effects can be processed on the callback video data through the following interfaces:

```java
@Override
public int onDrawFrame(int texId, int texWidth, int texHeight, long timestampNs, float[] transformMatrix) {
    ...
}
```

### Save video

After editing the video, call the following interface to save:

```java
mVideoEditor.save();
```

# Main concept


## Core class

There are 2 short video core classes, `UVideoRecord` and `UVideoEdit`, which complete the recording and post-editing processing of audio and video respectively.

### UShortVideoEnv

`UShortVideoEnv` contains the interface that the SDK initializes and must be called when the APP starts. The method list is as follows:

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

#### UVideoRecord

`UVideoRecord` contains the interfaces required to record short videos. The method list is as follows:

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

`UVideoEdit` contains the interfaces required to edit short videos. The method list is as follows:

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

## Parameter class

### UCameraParam

The `UCameraParam` class is used to configure the relevant parameters for camera acquisition, including the following methods:

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

### UMicrophoneParam

The `UMicrophoneParam` class is used to configure the relevant parameters for microphone acquisition, including the following methods:

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

The `UVideoEncodeParam` class is used to configure the relevant parameters of the video encoding, including the following methods:

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

The `UAudioEncodeParam` class is used to configure the relevant parameters of the audio encoding, including the following methods:

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

The `URecordParam` class is used to configure recording parameters and includes the following methods:

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

The `UVideoEditParam` class is used to configure video editing parameters, including the following methods:

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

The `UBeautyParam` class is used to configure built-in beautification parameters, including the following methods:

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

## Callback class

### UVideoFrameListener

This interface is used to call back the camera/edit preview video related events and data as follows:

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

This interface is used to call back to record/edit audio related events and data as follows:

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

This interface is used for callbacks to save events related to recording/editing files as follows:

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

This interface is used to call back related events during recording, as follows:

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

This interface is used to call back video editing and playback related events as follows:

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

This interface is used to call back camera focus related events as follows:

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

## Customized object


### UErrorCode

Customized error code, including the following:

| Error code                              | Error value  | Description            |
| --------------------------------- | ---- | ------------------- |
| ERROR_IO_EXCEPTION                | 20101    | IO error               |
| ERROR_SIGN_INVALID                | 20102    | SDK Invalid authorization signature       |
| ERROR_JSON_INVALID                | 20103    | JSON Invalid string        |
| ERROR_HTTP_SOCKET_TIMEOUT         | 20104    | HTTP Socket time out     |
| ERROR_HTTP_IO_EXCEPTION           | 20105    | HTTP IO abnormal          |
| ERROR_HTTP_RESPONSE_EXCEPTION     | 20106    | HTTP Return code exception        |
| ERROR_WRONG_STATE                 | 20001    | Status error              |
| ERROR_NO_CLIPS                    | 20002    | No clips                |
| ERROR_MUXER_STOP_FAILED           | 20003    | MUXER Stop failure         |
| ERROR_SET_CAMERA_FAILED           | 20004    | Setting camera failed         |
| ERROR_SET_MICROPHONE_FAILED       | 20005    | Setting the microphone failed          |
| ERROR_SET_VIDEO_ENCODER_FAILED    | 20006    | Setting video encoder failed       |
| ERROR_SET_AUDIO_ENCODER_FAILED    | 20007    | Setting the audio encoder failed       |
| ERROR_UNAUTHORIZED                | 20008    | Authentication failure               |
| ERROR_UNSUPPORTED_ANDROID_VERSION | 20009    | Unsupported Android version         |
| ERROR_INVALID_ARG                 | 20010    | Invalid parameter             |
| ERROR_DIFFERENT_AUDIO_PARAMS      | 20011    | Inconsistent audio parameters           |
| ERROR_MISS_DYNAMIC_LIBRARY        | 20012    | Missing dynamic library file           |
| ERROR_NO_VIDEO_TRACK              | 20013    | No video found             |
| ERROR_SAME_FILE_PATH              | 20014    | Source file path and target file path are the same  |
| ERROR_LOW_MEMORY                  | 20015    | Not enough storage/memory                |
| ERROR_MULTI_CODEC_WRONG           | 20016    | The device does not support multiple MediaCodec running simultaneously    |
| ERROR_SET_VIDEO_DECODER_FAILED    | 20017    | Setting video decoder failed       |
| ERROR_MUXER_START_FAILED          | 20018    | MUXER Startup failed        |
| ERROR_DURATION_TOO_SHORT          | 20019    | File length is too short           |
