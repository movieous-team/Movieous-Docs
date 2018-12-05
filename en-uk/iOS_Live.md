# iOS SDK

##  Release Note

### v1.0.0(2018-11-28)
- support standard RTMP live straming
- support external face beauty
- support watermark
- support automatic retry when disconnected
  
## Quick start
### Create an audio and video configuration Object
`MovieousLive` respectively uses the `MLAudioConfiguration` and `MLVideoConfiguration` objects for audio and video parameter configuration. Both configuration objects support the use of the `-defaultConfiguration` method to get a default configuration object. After getting the default configuration object, we can change the relevant parameters for subsequent use according to business requirements.
```
// Create default object
MLAudioConfiguration *audioConfiguration = [MLAudioConfiguration defaultConfiguration];
MLVideoConfiguration *videoConfiguration = [MLVideoConfiguration defaultConfiguration];
// Configure the parameters that need to be configured
audioConfiguration.audioBitRate = MLAudioBitRate128Kbps;
videoConfiguration.cameraResolution = AVCaptureSessionPresetHigh;
```
### Create `MLController`
Create the `MLController` object using the URLs of the push server and the `MLAudioConfiguration` and `MLVideoConfiguration` objects created in the previous step. After the creation is successful, you can use this object to control the live behavior, state acquisition and parameter adjustment.
```
MLController controller = [[MLController alloc] initWithURL:[NSURL URLWithString:_URL] audioConfiguration:_audioConfiguration videoConfiguration:_videoConfiguration];
```
- Note: Creating the `MLController` object requires that you have filed the domain name used by the push stream to [movie] (https://movieous.cn/) otherwise the initialization method will return `nil`
### Start collecting
Start the acquisition of audio and video, After the acquisition interface is called, the system will apply for the camera and microphone permissions to users (if `enable` is configured as `YES` in the corresponding configuration), make sure that you have  already added  `Privacy - Camera Usage Description` and `Privacy - Microphone Usage Description` description in the project of  `Info.plist` otherwise calling this interface will cause the program to crash.
```
[_movieousLiveController startCapturingWithCompletion:^(AVAuthorizationStatus cameraAuthorizationStatus, AVAuthorizationStatus microphoneAuthorizationStatus, NSError *error) {
// To make respond accordingly
}];
```
### Adding a preview-view to the subview of the current one
`MLController` provides `@property(nonatomic, strong, readonly) UIView *preview` property is used to preview the data collected by the camera, you can add it to the subview of your current view and resize it to start previewing the camera enter.
```
[self.view addSubview:controller.preview];
controller.preview.frame = self.view.bounds;
```
- Note: Please get the preview view after starting the acquisition, otherwise the previous preview-view object will be `nil`
### Start pushing stream 
Please push stream as follows
```
[controller startBroadcastingWithCompletion:^(NSError *error) {
// To make respond accordingly
}];
```
### Setting delegate of `MLController` 
`MLController` informs the status of the live broadcast by means of the delegation, You can obtain notifications including live broadcast status and other related information by implementing the method defined in `MLControllerDelegate`.
```
{
...
controller.delegate = self
...
}

// Obtain callbacks related to live broadcast status changes.
- (void)controller:(MLController *)controller stateDidChange:(MLState)state error:(NSError *)error {
// Make a respond accordingly based on live broadcast status.
}
```
### End pushing stream
After  pushing stream  is completed, you can call the following method to end the pushing stream  (you can still use this object to call the `-startBroadcastingWithCompletion:` method again to start the pushing stream again after the end)
```
[controller stopBroadcasting];
```
## How to install

MovieousLive supports multiple methods for installing the library in a project.

### Installation with CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like MovieousLive in your projects. See the ["Getting Started" guide for more information](https://developer.movieous.cn/#/en-uk/iOS_Live?id=quick-start). You can install it with the following command:

```bash
$ gem install cocoapods
```

> CocoaPods 0.39.0+ is required to build MovieousLive.

#### Podfile

To integrate MovieousLive into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'

target 'TargetName' do
pod 'MovieousLive'
end
```

Then, run the following command:

```bash
$ pod install
```

## Main concept
## Usage guideline
### MLController
```objectivec
@class MLController;

@protocol MLControllerDelegate<NSObject>

@optional

/**
 * @brief Called when state of the controller changed
 * @param controller The controller whose state has changed
 * @param state New state of the controller
 * @param error This Parameter will only be non nil when state turns to `MLStreamStateError`
 */
- (void)controller:(MLController *)controller stateDidChange:(MLState)state error:(NSError *)error;

/**
 * @brief Called when new video data are available, you can do custom process within this delegate method
 * @param controller The controller whose state has changed
 * @param videoData Video data
 * @return The processed video data
 */
- (CVPixelBufferRef)controller:(MLController *)controller didGetVideoData:(CVPixelBufferRef)videoData;

/**
 * @brief Called when new audio data are available, you can do custom process within this delegate method
 * @param controller The controller whose state has changed
 * @param audioData Aideo data
 * @return The processed audio data
 */
- (AudioBufferList *)controller:(MLController *)controller didGetAudioData:(AudioBufferList *)audioData;

@end

@interface MLController : NSObject

/**
 * @brief SDK Log Level, Default to MLStreamLogLevelWarning
 * @warning Don't use MLLogLevelVerbose for release version，this will cause performance issue。
 */
+ (void)setLogLevel:(MLLogLevel)logLevel;

/**
 * @brief Camera preview
 */
@property(nonatomic, strong, readonly) UIView *preview;

/**
 * @brief State of the MLController
 */
@property(nonatomic, assign, readonly) MLState state;

/**
 * @brief URL of the server to which the stream is pushed
 */
@property(nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief How to scale content when aspect ratios of the content and preview are not equal
 * Default to MovieousScalingModeAspectFit
 */
@property(nonatomic, assign) MovieousScalingMode scalingMode;

/**
 * @brief Delegate of the controller
 * Default to nil
 */
@property(nonatomic, weak) id <MLControllerDelegate> delegate;

/**
 * @brief When dynamicFrameRate is enabled, ML engine will adjust frame rate when network is not able to send data realtime
 * Default to NO
 */
@property(nonatomic, assign) BOOL dynamicFrameRate;

/**
 * @discussion When autoReconnect is enabled, ML engine will try to reconnect to the server when live broadcasting is disconnected due to some exception. In this situation, state of the controller will turn to `MLStreamStateReconnecting`, and ML engine will try at most 3 times, from 0~2 seconds to 10 seconds. If the server still can't be connected, ML engine will give up trying, and state of the controller will turn to `MLStreamStateError`
 * Default to YES
 */
@property(nonatomic, assign) BOOL autoReconnect;

/**
 * @brief When monitorNetwork is enabled, ML engine will try to reconnect to the server when network changed from Wifi to Cellular or from Cellular to Wifi.
 * Default to YES
 */
@property (nonatomic, assign) BOOL monitorNetwork;

/**
 * @brief When monitorNetwork is set to YES, this handler will be called when network state changes, you can return yes to try reconnect to the server, or return NO to do nothing.
 */
@property (nonatomic, copy) ConnectionChangeActionHandler connectionChangeActionHandler;

/**
 * @brief Initialize the controller with the specific URL, default audio configuration and default video configuration
 * @param URL The URL to which the live stream will be pushed, if the URL you passed is unauthorized by ML, this method will return a nil object
 * @return The initialized controller, if the URL you passed is unauthorized by ML, this method will return a nil object
 */
- (instancetype)initWithURL:(NSURL *)URL;

/**
 * @brief Initialize the controller with the specific URL, audio configuration and video configuration
 * @param URL The URL to which the live stream will be pushed, if the URL you passed is unauthorized by ML, this method will return a nil object
 * @param audioConfiguration Audio Configuration for the live broadcasting, nil to use default configuration
 * @param videoConfiguration Video Configuration for the live broadcasting, nil to use default configuration
 * @return The initialized controller, if the URL you passed is unauthorized by ML, this method will return a nil object
 */
- (instancetype)initWithURL:(NSURL *)URL audioConfiguration:(MLAudioConfiguration *)audioConfiguration videoConfiguration:(MLVideoConfiguration *)videoConfiguration NS_DESIGNATED_INITIALIZER;

/**
 * @brief Start capturing audio and video data. Call this method will request for authorization of the camera and microphone if enabled
 * @param completion Block called when start completed
 */
- (void)startCapturingWithCompletion:(void (^)(AVAuthorizationStatus cameraAuthorizationStatus, AVAuthorizationStatus microphoneAuthorizationStatus, NSError *error))completion;

/**
 * @brief Start broadcasting to the server
 * @param completion Completion callback. if any error happens, error will not be nil.
 */
- (void)startBroadcastingWithCompletion:(void (^)(NSError *error))completion;

/**
 * @brief Stop broadcasting to the server
 */
- (void)stopBroadcasting;

@end

@interface MLController(Video)

/**
 * @brief Turn mirrorFrontPreview means preview for front camera will be mirrored
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign) BOOL mirrorFrontPreview;

/**
 * @brief Turn mirrorBackPreview means preview for back camera will be mirrored
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign) BOOL mirrorBackPreview;

/**
 * @brief Turn mirrorFrontStream means stream broadcasted for front camera will be mirrored
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign) BOOL mirrorFrontStream;

/**
 * @brief Turn mirrorBackStream means stream broadcasted for back camera will be mirrored
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign) BOOL mirrorBackStream;

/**
 * @brief Indicates that you can touch preview to make the camera focus on the point you touched
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign) BOOL touchToFocus;

/**
 * @brief Indicates whether to turn torch on
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) BOOL torchOn;
- (BOOL)setTorchOn:(BOOL)torchOn error:(NSError **)error;

/**
 * @brief Indicates that the device should automatically focus when needed
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) BOOL continuousAutofocus;
- (BOOL)setContinuousAutofocus:(BOOL)continuousAutofocus error:(NSError **)error;

/**
 * @brief Number of minimum frames per seconds captured by the camera
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) NSUInteger frameRate;
- (BOOL)setFrameRate:(NSUInteger)frameRate error:(NSError **)error;

/**
 * @brief You may use an AVCaptureSessionPreset to set the format for camera output data
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, strong, readonly) AVCaptureSessionPreset cameraResolution;
- (BOOL)setCameraResolution:(AVCaptureSessionPreset)cameraResolution error:(NSError **)error;

/**
 * @brief Indicates the physical position of camera on the system.
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) AVCaptureDevicePosition cameraPosition;
- (BOOL)setCameraPosition:(AVCaptureDevicePosition)cameraPosition error:(NSError **)error;

/**
 * @brief Indicates video orientation
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) AVCaptureVideoOrientation cameraOrientation;
- (BOOL)setCameraOrientation:(AVCaptureVideoOrientation)cameraOrientation error:(NSError **)error;

/**
 * @brief  Controls zoom level of image outputs
 * @discussion Applies a centered crop for all image outputs, scaling as necessary to maintain output dimensions. Minimum value of 1.0 yields full field of view, increasing values will increase magnification, up to a maximum value specified in the videoMaxZoomFactor property. Assigning values outside the acceptable range will generate an NSRangeException.
 * Default to the value you set by MLVideoConfiguration
 */
@property (nonatomic, assign, readonly) CGFloat cameraZoomFactor;
- (BOOL)setCameraZoomFactor:(CGFloat)cameraZoomFactor error:(NSError **)error;

/**
 * @brief Switch the camera
 */
- (BOOL)switchCameraWithError:(NSError **)error;

@end

@interface MLController(Audio)

/**
 * @brief Turn headphoneMonitor on, you can hear the voice of yourself from the headphone connected to the device
 * Default to NO
 */
@property (nonatomic, assign) BOOL headphoneMonitor;

@end
```

### MLAudioConfiguration
```objectivec
@interface MLAudioConfiguration : NSObject
<
NSCopying
>

/**
 * @brief Whether to enable audio broadcast
 * Default to YES
 */
@property (nonatomic, assign) BOOL enable;

/**
 * @brief The input source for audio
 * Default to MLAudioSourceMicrophone
 */
@property (nonatomic, assign) MLAudioSource source;

/**
 * @brief Whether to allow mix with other audio
 * Default to YES
 */
@property (nonatomic, assign) BOOL allowMixWithOthers;

/**
 * @brief Turn on this function to avoid acoustic echo.
 * Default to NO
 */
@property (nonatomic, assign) BOOL acousticEchoCancellationEnable;

/**
 * @brief The encoded audio bitrate
 * Default to MLAudioBitRate96Kbps
 */
@property (nonatomic, assign) MLAudioBitRate audioBitRate;

/**
 * @brief The default configuration
 * @return The initialized default configuration
 */
+ (instancetype)defaultConfiguration;

@end
```

### MLVideoConfiguration
```objectivec
@interface MLVideoConfiguration : NSObject
<
NSCopying
>

/**
 * @brief Whether to enable video broadcast
 * Default to YES
 */
@property (nonatomic, assign) BOOL enable;

/**
 * @brief The input source for video
 */
@property (nonatomic, assign) MLVideoSource source;

/**
 * @brief Number of maximum frames per seconds captured by the camera
 * Default to 30
 */
@property (nonatomic, assign) NSUInteger frameRate;

/**
 * @brief Turn mirrorFrontPreview means preview for front camera will be mirrored
 * Default to YES
 */
@property (nonatomic, assign) BOOL mirrorFrontPreview;

/**
 * @brief Turn mirrorBackPreview means preview for back camera will be mirrored
 * Default to NO
 */
@property (nonatomic, assign) BOOL mirrorBackPreview;

/**
 * @brief Turn mirrorFrontStream means stream broadcasted for front camera will be mirrored
 * Default to NO
 */
@property (nonatomic, assign) BOOL mirrorFrontStream;

/**
 * @brief Turn mirrorBackStream means stream broadcasted for back camera will be mirrored
 * Default to NO
 */
@property (nonatomic, assign) BOOL mirrorBackStream;

/**
 * @brief You may use an AVCaptureSessionPreset to set the resolution for camera output data
 * Default to AVCaptureSessionPresetHigh
 */
@property (nonatomic, copy) AVCaptureSessionPreset cameraResolution;

/**
 * @brief Indicates the physical position of camera on the system.
 * Default to AVCaptureVideoOrientationPortrait
 */
@property (nonatomic, assign) AVCaptureDevicePosition cameraPosition;

/**
 * @brief The orientation of camera.
 * Default to AVCaptureDevicePositionFront
 */
@property (nonatomic, assign) AVCaptureVideoOrientation cameraOrientation;

/**
 * @brief The profile/level constants are specific to H.264 encoding, you can refer to https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC to find more detailed information about this property
 * Default to AVVideoProfileLevelH264HighAutoLevel
 */
@property (nonatomic, copy) NSString *profileLevel;

/**
 * @brief Size of broadcasted video
 * @discussion What need to pay attention to is that this size is encoded video stream size, not necessarily be the size of camera output video. When the aspect ratios of camera output and encoded video are not equal, encoder will use AVVideoScalingModeResizeAspectFill as the scaling mode.
 * Default to (640, 480)
 */
@property (nonatomic, assign) CGSize  size;

/**
 * @brief The max GOP for video encoding, refer to https://en.wikipedia.org/wiki/Group_of_pictures
 * Default to 3 times frameRate
 */
@property (nonatomic, assign) NSUInteger gop;

/**
 * @brief Average bitrate for the encoded video
 * Default to 1 mbps
 */
@property (nonatomic, assign) NSUInteger averageBitRate;

/**
 * @brief The default configuration
 * @return The initialized default configuration
 */
+ (instancetype)defaultConfiguration;

@end
```

### MLTypeDefines
```objectivec
/**
 * @brief Indicate video datasource type
 */
typedef NS_ENUM(NSInteger, MLVideoSource) {
    /**
     * @brief Video data from camera
     */
    MLVideoSourceCamera,
};

/**
 * @brief Indicate audio datasource type
 */
typedef NS_ENUM(NSInteger, MLAudioSource) {
    /**
     * @brief Audio data from microphone
     */
    MLAudioSourceMicrophone,
};

/**
 * @brief Audio bit rate use to encode audio
 */
typedef NS_ENUM(NSInteger, MLAudioBitRate) {
    /**
     * @brief 64 Kbps bit rate
     */
    MLAudioBitRate64Kbps = 64000,
    /**
     * @brief 96 Kbps bit rate
     */
    MLAudioBitRate96Kbps = 96000,
    /**
     * @brief 128 Kbps bit rate
     */
    MLAudioBitRate128Kbps = 128000,
};

/**
 * @brief Log level
 */
typedef NS_ENUM(NSUInteger, MLLogLevel){
    /**
     * @brief No logs
     */
    MLLogLevelOff       = 0,
    /**
     * @brief Error logs only
     */
    MLLogLevelError,
    /**
     * @brief Error and warning logs
     */
    MLLogLevelWarning,
    /**
     * @brief Error, warning and info logs
     */
    MLLogLevelInfo,
    /**
     * @brief Error, warning, info and debug logs
     */
    MLLogLevelDebug,
    /**
     * @brief Error, warning, info, debug and verbose logs
     */
    MLLogLevelVerbose,
};

typedef NS_ENUM(NSInteger, MLState) {
    /**
     * @brief Initial state
     */
    MLStateInitial,
    /**
     * @brief Controller is now connecting to server
     */
    MLStateConnecting,
    /**
     * @brief Controller is now connected to server
     */
    MLStateConnected,
    /**
     * @brief Controller is now disconnecting from server
     */
    MLStateDisconnecting,
    /**
     * @brief Controller is now disconnected from server
     */
    MLStateDisconnected,
    /**
     * @brief Controller is now reconnecting to server, this state will appear when you have enabled autoReconnect property and the controller have disconnected from server
     */
    MLStateReconnecting,
    /**
     * @brief Controller has encountered an error and stopped because of this error
     */
    MLStateError,
    /**
     * @brief Controller is now capturing audio and video if available
     */
    MLStateCapturing,
};

/**
 * @brief Network transition type
 */
typedef NS_ENUM(NSUInteger, MLNetworkStateTransition) {
    /**
     * @brief Unknown transition
     */
    MLNetworkStateTransitionUnknown = 0,
    /**
     * @brief Transition from no network to WiFi
     */
    MLNetworkStateTransitionUnconnectedToWiFi,
    /**
     * @brief Transition from no network to WWAN(4G, 3G, etc)
     */
    MLNetworkStateTransitionUnconnectedToWWAN,
    /**
     * @brief Transition from WiFi to no network
     */
    MLNetworkStateTransitionWiFiToUnconnected,
    /**
     * @brief Transition from WWAN(4G, 3G, etc) to no network
     */
    MLNetworkStateTransitionWWANToUnconnected,
    /**
     * @brief Transition from WiFi to WWAN(4G, 3G, etc)
     */
    MLNetworkStateTransitionWiFiToWWAN,
    /**
     * @brief Transition from WWAN(4G, 3G, etc) to WiFi
     */
    MLNetworkStateTransitionWWANToWiFi,
};

/**
 * @brief Handler to receive network change notification
 */
typedef BOOL(^ConnectionChangeActionHandler)(MLNetworkStateTransition transition);
```
