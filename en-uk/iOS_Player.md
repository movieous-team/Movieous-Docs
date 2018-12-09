# iOS SDK

##  Release Note

### v1.0.1(2018-12-8)
- fix error when autoreconnect is triggered

### v1.0.0(2018-11-28)
- support common Live、VOD format play

## Quick start

### Create Player Object

You can create a `MovieousPlayerController` object using the media URL that will be played. The instance controls the player after the creation is successful, to obtain player status including  the player events and some relevant information.

```
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```

### Adding a preview-view to the subview of the current one

`MovieousPlayerController`  provides  `@property (nonatomic, readonly) UIView *playerView`  property is used to show the media data, you can add it to the subview of your current view and resize it to start show the media video.

```
[self.view addSubview:player.playerView];
player.playerView.frame = self.view.bounds;
```

### Start Playback

Here you can start playing the media

```
[player play];
```

### Setting delegate of `MovieousPlayerController` 

`MovieousPlayerController`  informs the status of the player by means of the delegation, You can obtain notifications including live broadcast status and other related information by implementing the method defined in  `MovieousPlayerControllerDelegate`.

```
{
...
player.delegate = self
...
}

// Obtain callbacks related to Player status changes.
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChange:(MPPlayState)playState {
// Make a respond accordingly based on Player status.
}

// Obtain callbacks related to Player pause connection changes.
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playFinished:(MPFinishReason)finishReason {
// Make a respond accordingly based on Player pause connection.
}
```
### Pause Playback

Please pause playback based on following methods .

```
[player pause];
```

### Stop Playback

Please end playback based on following methods .

```
[player stop];
```

## How to install
MovieousPlayer supports multiple methods for installing the library in a project.

### Installation with CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like MovieousPlayer in your projects. See the ["Getting Started" guide for more information](https://developer.movieous.cn/#/en-uk/iOS_Player?id=quick-start). You can install it with the following command:

```bash
$ gem install cocoapods
```

> CocoaPods 0.39.0+ is required to build MovieousPlayer.

#### Podfile

To integrate MovieousPlayer into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'

target 'TargetName' do
pod 'MovieousPlayer'
end
```

Then, run the following command:

```bash
$ pod install
```

## Main concept

## Usage guideline
### MovieousPlayerOptions
```objectivec
/**
 * @brief Decoder type
 */
typedef NS_ENUM(NSInteger, MovieousPlayerDecoderType) {
    /**
     * @brief Use Software decoder
     */
    MovieousPlayerDecoderTypeSoftware,
    /**
     * @brief Use Hardware decoder
     */
    MovieousPlayerDecoderTypeHardware,
};

@interface MovieousPlayerOptions : NSObject

/**
 * @brief Indicates that this is a live streaming video, so that the MovieousPlayer will try it's best to keep low delay to the source. What need to pay attention is that turn this property on can cause more lag.
 * Default to NO
 */
@property (nonatomic, assign) BOOL liveStreaming;

/**
 * @brief Indicates whether Movieous Player should try to catch up with the live source, you should only enable this option when you are playing live broadcast source
 * Default to NO
 */
@property (nonatomic, assign) BOOL delayOptimization;

/**
 * @brief Indicates that MovieousPlayer will be allowed to mix audio with other app
 * Default to YES
 */
@property (nonatomic, assign) BOOL allowMixAudioWithOthers;

/**
 * @brief The type of decoder you want to use
 * Default to MovieousPlayerDecoderTypeHardware
 */
@property (nonatomic, assign) MovieousPlayerDecoderType decoderType;

/**
 * @brief Timeout interval in microsecond for network request proceeeded by MovieousPlayer
 * Default to 10 * 1000 * 1000 (10s)
 */
@property (nonatomic, assign) NSUInteger timeoutInterval;

/**
 * @brief Return the default option
 * @return The initialized default option
 */
+ (instancetype)defaultOptions;

@end
```

### MovieousPlayerController
/**
 * @brief Scaling mode of MovieousPlayer content
 */
typedef NS_ENUM(NSInteger, MPScalingMode) {
    /**
     * @brief Uniform scale until one dimension fits
     */
    MPScalingModeAspectFit,
    /**
     * @brief Uniform scale until the movie fills the visible bounds. One dimension may have clipped contents
     */
    MPScalingModeAspectFill,
    /**
     * @brief Non-uniform scale. Both render dimensions will exactly match the visible bounds
     */
    MPScalingModeFill
};

/**
 * @brief State of the MovieousPlayer
 */
typedef NS_ENUM(NSInteger, MPPlayerState) {
    /**
     * @brief State of fresh initialized player
     */
    MPPlayerStateIdle,
    /**
     * @brief Preparing to play
     */
    MPPlayerStatePreparing,
    /**
     * @brief Prepared to play
     */
    MPPlayerStatePrepared,
    /**
     * @brief Player is now playing
     */
    MPPlayerStatePlaying,
    /**
     * @brief Player is now buffering due to slow network connection
     */
    MPPlayerStateBuffering,
    /**
     * @brief Player is now seeking to a new place
     */
    MPPlayerStateSeeking,
    /**
     * @brief Player is paused
     */
    MPPlayerStatePaused,
    /**
     * @brief Play of current media source has completed
     */
    MPPlayerStateCompleted,
    /**
     * @brief Player has stopped, Player will turn to this state after -stop is called
     */
    MPPlayerStateStopped,
    /**
     * @brief Some error has occured
     */
    MPPlayerStateError,
};

/**
 * @brief The reason why MovieousPlayer finishes
 */
typedef NS_ENUM(NSInteger, MPFinishReason) {
    /**
     * @brief MovieousPlayer finishes because of play ended, this state may only not appear when play live video
     */
    MPFinishReasonPlaybackEnded,
    /**
     * @brief MovieousPlayer finishes because of some error
     */
    MPFinishReasonPlaybackError,
    /**
     * @brief MovieousPlayer finishes because user stops it
     */
    MPFinishReasonUserExited
};

/**
 * @brief The level of log
 */
typedef NS_ENUM(NSInteger, MPLogLevel) {
    /**
     * @brief Unknown log level
     */
    MPLogLevelUnknown,
    /**
     * @brief Default log level
     */
    MPLogLevelDefault,
    /**
     * @brief Verbose log level
     */
    MPLogLevelVerbose,
    /**
     * @brief Debug log level
     */
    MPLogLevelDebug,
    /**
     * @brief Info log level
     */
    MPLogLevelInfo,
    /**
     * @brief Warn log level
     */
    MPLogLevelWarn,
    /**
     * @brief Error log level
     */
    MPLogLevelError,
    /**
     * @brief Fatal log level
     */
    MPLogLevelFatal,
    /**
     * @brief Silent log level
     */
    MPLogLevelSilent
};

/**
 * @brief What to do when MovieousPlayer is interrupted by other audio source
 */
typedef NS_ENUM(NSInteger, MPInterruptionOperation) {
    /**
     * @brief Call -pause when interrupted by other audio source
     */
    MPInterruptionOperationPause,
    /**
    * @brief Call -stop when interrupted by other audio source
    */
    MPInterruptionOperationStop,
};

@class MovieousPlayerController;

@protocol MovieousPlayerControllerDelegate<NSObject>

@optional

/**
 * @brief Notify delegate that MovieousPlayerController's first video frame has already rendered
 * @param playerController The MovieousPlayerController whose calls the method
 */
- (void)movieousPlayerControllerFirstVideoFrameRendered:(MovieousPlayerController *)playerController;

/**
 * @brief Notify delegate that MovieousPlayerController's first audio frame has already rendered
 * @param playerController The MovieousPlayerController whose calls the method
 */
- (void)movieousPlayerControllerFirstAudioFrameRendered:(MovieousPlayerController *)playerController;

/**
 * @brief Notify delegate that MovieousPlayerController's state has changed
 * @param playerController The MovieousPlayerController whose state has changed
 * @param playState The new state of MovieousPlayerController
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChange:(MPPlayerState)playState;

/**
 * @brief Notify delegate that MovieousPlayerController's state has changed
 * @param playerController The MovieousPlayerController whose state has changed
 * @param previousState The previous state of MovieousPlayerController
 * @param newState The new state of MovieousPlayerController
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChangeWithPreviousState:(MPPlayerState)previousState newState:(MPPlayerState)newState;

/**
 * @brief Notify delegate that MovieousPlayerController's has begun to retry reconnecting
 * @param playerController The MovieousPlayerController trying to reconnect
 * @param currentCount Current retried count
 */
- (BOOL)movieousPlayerController:(MovieousPlayerController *)playerController playerBeginsToRetry:(NSUInteger)currentCount;

/**
 * @brief Notify delegate that MovieousPlayerController has finished playing
 * @param playerController The MovieousPlayerController that has finished
 * @param finishReason The reason why MovieousPlayerController has finished
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playFinished:(MPFinishReason)finishReason;

@end

@interface MovieousPlayerController : NSObject

/**
 * @brief The global log level
 */
@property (nonatomic, assign, class) MPLogLevel logLevel;

/**
 * @brief Options which initialize the controller
 */
@property (nonatomic, strong, readonly) MovieousPlayerOptions *options;

/**
 * @brief This view displays visual content of the video
 */
@property (nonatomic, readonly) UIView *playerView;

/**
 * @brief Total duration of the video you are playing
 */
@property (nonatomic, readonly) NSTimeInterval totalDuration;

/**
 * @brief State of the controller
 */
@property (nonatomic, readonly) MPPlayerState playState;

/**
 * @brief Size of the video you are playing
 */
@property (nonatomic, readonly) CGSize naturalSize;

/**
 * @brief Take a snapshot of the current scene
 * @return The snapshot captured
 */
@property (nonatomic, readonly) UIImage *snapshot;

/**
 * @brief Current time in the time range of your video
 */
@property (nonatomic, assign) NSTimeInterval currentTime;

/**
 * @brief How to scale content when aspect ratios of the content and preview are not equal
 * Default to MPScalingModeNone
 */
@property (nonatomic, assign) MPScalingMode scalingMode;

/**
 * @brief Volume of the player
 * Default to 1
 */
@property (nonatomic, assign) float volume;

/**
 * @brief Whether to pause when enter background, you should set this property to NO when you want to keep playing in background
 * Default to YES
 */
@property (nonatomic, assign) BOOL pauseInBackground DEPRECATED_ATTRIBUTE;

/**
 * @brief Whether to get interrupted when enter background, you should set this property to NO when you want to keep playing in background
 * Default to YES
 */
@property (nonatomic, assign) BOOL interruptInBackground;

/**
 * @brief What to do when interrupted by other audio source
 * Default to MPInterruptionOperationPause
 */
@property (nonatomic, assign) MPInterruptionOperation interruptionOperation;

/**
 * @brief Retry playing when error occurs
 * Default to YES
 */
@property (nonatomic, assign) BOOL retryIfError;

/**
 * @brief Delegate of the player controller
 */
@property (nonatomic, weak) id<MovieousPlayerControllerDelegate> delegate;

/**
 * @brief Whether to loop when play completed
 */
@property (nonatomic, assign) BOOL loop;

/**
 * @brief Is VRMode enabled
 */
@property (nonatomic, assign) BOOL VRMode;

/**
 * @brief Is gyroscope enabled, only valid when VRMode is on
 */
@property (nonatomic,assign) BOOL gyroscopeEnabled;

/**
 * @brief Is dualScreen Enabled, only valid when VRMode is ON
 */
@property (nonatomic, assign) BOOL dualScreen;

/**
 * @brief Is touch to move Enabled, only valid when VRMode is ON
 */
@property (nonatomic, assign) BOOL touchToMoveEnabled;

/**
 * @brief Create a MovieousPlayerController instance with media URL
 * @param URL The URL of URL you want to play
 * @return The initialized playerController
 */
+ (instancetype)playerControllerWithURL:(NSURL *)URL;

/**
 * @brief Create a MovieousPlayerController instance with media URL and options
 * @param URL The URL of URL you want to play
 * @param options The options to use
 * @return The initialized playerController
 */
+ (instancetype)playerControllerWithURL:(NSURL *)URL options:(MovieousPlayerOptions *)options;

/**
 * @brief Initialize a MovieousPlayerController instance with media URL
 * @param URL The URL you want to play
 * @return The initialized playerController
 */
- (instancetype)initWithURL:(NSURL *)URL;

/**
 * @brief Initialize a MovieousPlayerController instance with media URL and options
 * @param URL The URL you want to play
 * @param options The options to use
 * @return The initialized playerController
 */
- (instancetype)initWithURL:(NSURL *)URL options:(MovieousPlayerOptions *)options;

/**
 * @brief prepareToPlay
 */
- (void)prepareToPlay;

/**
 * @brief Play
 */
- (void)play;

/**
 * @brief Pause
 */
- (void)pause;

/**
 * @brief Stop
 */
- (void)stop;

@end
```