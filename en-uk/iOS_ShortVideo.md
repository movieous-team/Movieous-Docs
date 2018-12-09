# iOS SDK

##  Release Note

### v1.0.1(2018-12-3)
- support license authentication
- fix preview error when media service is resetted because of frequent backgroud audio switch operations, etc.

### v1.0.0(2018-11-28)
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
- Support core system framework as  arm64, i386, x86_64 

## Quict start
Dear developer, welcome to use the short video SDK from Movieous . This tutorial will guide you how to acheive integration of short video recording, editing and exporting functions in your iOS project. But first, we assume that you already know the underlying syntax of Objective-C.

### Add dependency
* First, the [iOS Short Video SDK Installation] (iOS_ShortVideo_Install.md) for your reference to introduce MovieousShortVideo to your project.

### Introduce related header files
Add the following statement to the page original source file where you need to integrate the short video SDK
```objectivec
#import <MovieousShortVideo/MovieousShortVideo.h>
```

Then, you can selectively integrate video recording, video editing or video export modules according to your requirements.

### Video recording
- Add microphone and camera usage permission
Open the project configuration page and add `Privacy - Camera Usage Description` and `Privacy - Microphone Usage ` description to the Info option.
![image](./images/authorization.png)

- Generate configuration objects for audio and video and make modification to related parameters as requirements.
```objectivec
    MSVRecorderAudioConfiguration *audioConfiguration = [MSVRecorderAudioConfiguration defaultConfiguration];
    MSVRecorderVideoConfiguration *videoConfiguration = [MSVRecorderVideoConfiguration defaultConfiguration];
    // Modify the parameters that need to be changed according to your requirements, use the default parameters if there is no change.
    videoConfiguration.cameraResolution = AVCaptureSessionPreset1280x720;
    videoConfiguration.cameraPosition = AVCaptureDevicePositionFront;
```

- Generate the recording core control object
```objectivec
    _recorder = [[MSVRecorder alloc] initWithAudioConfiguration:audioConfiguration videoConfiguration:videoConfiguration error:&error];
    if (error) {
        SHOW_ERROR_ALERT;
        return;
    }
    // Setup the maximum recording length
    _recorder.maxDuration = 10;
    // Setup the recording event callback
    _recorder.delegate = self;
    // Insert a preview view into the view stack to preview the camera acquisition results.
    _recorder.preview.frame = self.view.frame;
    [self.view insertSubview:_recorder.preview atIndex:0];
```

- Start audio and video acquisition
```objectivec
    [_recorder startCapturingWithCompletionHandler:^(BOOL audioGranted, NSError *audioError, BOOL videoGranted, NSError *videoError) {
        if (videoError) {
            SHOW_ALERT(@"error", videoError.localizedDescription, @"ok");
            return;
        }
        if (!videoGranted) {
            SHOW_ALERT(@"warning", @"video not authorized", @"ok");
            return;
        }
        dispatch_async(dispatch_get_main_queue(), ^{
            [self syncBeautyParams];
        });
        if (audioError) {
            SHOW_ALERT(@"error", audioError.localizedDescription, @"ok");
            return;
        }
        if (!audioGranted) {
            SHOW_ALERT(@"warning", @"audio not authorized", @"ok");
            return;
        }
    }];
```

- Start recording the video clip
```objectivec
    if (![_recorder startRecordingWithClipConfiguration:config error:&error]) {
        SHOW_ERROR_ALERT;
        return;
    }
```

- Complete current clip recording
```objectivec
    [_recorder finishRecordingWithCompletionHandler:^(MSVMainTrackClip *clip, NSError *error) {
        // The generated mp4 file and related description information can be obtained from the generated ’clip‘ object.
        if (error) {
            SHOW_ERROR_ALERT;
        }
    }];
```
After completing the current clip recording, you can return to the section `Start Recording Clips` to continue recording the next clip,All recorded clips will be merged sequentially into a video draft and saved in the `_recorder.draft` object, which can be passed to the editor for editing or passed to the exporter for directly export as a composite file.
### Video editing
- Create the editor core control class
```objectivec
    //  The 'draft' here can be created by using the generated video clip from recording phase or by using existing audio and video resources (such as system albums or downloading from a remote location, etc.)
    _editor = [MSVEditor createSharedInstanceWithDraft:_draft error:&error];
    if (error) {
        SHOW_ERROR_ALERT;
        return;
    }
    _editor.delegate = self;
    // Insert the preview view into the view stack to preview the edit results
    _previewContainer.preview = _editor.preview;
    _editor.loop = YES;
```

- Preview draft
```objectivec
    [_editor play];
```

- Video editing
`MovieousShortVideo`  use `MSVDraft` object to storage video edited information,You can adjust the parameters of `_editor.draft` arbitrarily according to your requirements. After the adjustment, the preview view will be refreshed in real time. The video editing operation demonstration as below.
```objectivec
    _editor.draft.timeRange = (MovieousTimeRange){
        // The start time of clip part
        startTime,
        // the duration time of clipped part
        duration,
    };
```

### Video export
- Create the export core control object
```objectivec
    NSError *error;
    // Create export control object.
    _exporter = [[MSVVideoExporter alloc] initWithDraft:_draft error:&error];
    if (error) {
        SHOW_ERROR_ALERT;
        return;
    }
    // Save the export results to an album
    _exporter.saveToPhotosAlbum = YES;
    __weak typeof(self) wSelf = self;
    //Progress callback
    _exporter.progressHandler = ^(float progress) {
        NSLog(@"progress: %f", progress);
    };
    // Complete callback
    _exporter.completionHandler = ^(NSURL *URL) {
        SHOW_ALERT_FOR(@"complete", @"export successfully", @"i understand", wSelf);
    };
    // Failure callback
    _exporter.failureHandler = ^(NSError *error) {
        SHOW_ERROR_ALERT_FOR(wSelf);
    };
```

- Start exporting
```objectivec
    [_exporter startExport];
```

## How to install
MovieousShortVideo supports multiple methods for installing the library in a project.

### Installation with CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like MovieousShortVideo in your projects. See the ["Getting Started" guide for more information](https://developer.movieous.cn/#/en-uk/iOS_ShortVideo?id=quict-start). You can install it with the following command:

```bash
$ gem install cocoapods
```

> CocoaPods 0.39.0+ is required to build MovieousShortVideo.

#### Podfile

To integrate MovieousShortVideo into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'

target 'TargetName' do
pod 'MovieousShortVideo'
end
```

Then, run the following command:

```bash
$ pod install
```
## Main concept
### Draft (MSVDraft)

The draft is the core data structure of MovieousShortVideo, which represents a video project during the process of production. It saves all audio and video data sources, clips, volume adjustments, special effects, etc., all of the elements here construct the final video. The draft objects include output of the recording phase, input and output of editing phase, and the input of the export phase. All video editing operations of the developer are completed by the draft object.

### Main Track Clip (MSVMainTrackClip)

The main track segment is the basic element of the draft. The main audio and video of the draft is generated sequentially by the audio and video of the main track segment.The duration of the draft is the sum of the durations of all the main track segments.
### Audio Clip (MSVAudioClip)

The Audio clip is the background audio added on the basis of the main track audio. It mixes with the specified volume and the main track and other overlapping parts of the audio to form the audio of the destination video.
### Effects

Special effects are superimposed on the main track of the draft, including filters, repeating effects, variable speed, etc.

### Recorder (MSVRecorder)

The recorder mainly provides video segmentation recording function, which generates multiple video segments to form the main track, and adds background music as an audio clip of the draft.

### Editor (MSVEditor)

The editor provides real-time preview of drafts and previews the effects of draft editing in real time.

### Exporter (MSVVideoExporter)

The exporter accepts draft objects as input, and exports target files in various formats.

## Usage guideline
### Type define(MSVTypeDefines)
```objectivec
/**
 * Video input souce type
 */
typedef NS_ENUM(NSInteger, MSVVideoSource) {
    /**
     * @brief No video，please don't set both video source and audio source to none.
     */
    MSVVideoSourceNone,
    /**
     * @brief Camera input source
     */
    MSVVideoSourceCamera,
    /**
     * @brief UIView input source, when you use this input source please specify viewForRecording property in `startRecordingWithClipConfiguration:error:` method
     */
    MSVVideoSourceUIView,
    /**
     * @brief External video source
     * @warning When you use this input source, please use `writeVideoData:error:` to write video data, if you choose external video source but don't input video data, unknown error may come out
     */
    MSVVideoSourceExtern,
};

/**
 * @brief Audio input source
 */
typedef NS_ENUM(NSInteger, MSVAudioSource) {
    /**
     * @brief No audio，please don't set both video source and audio source to none.
     */
    MSVAudioSourceNone,
    /**
     * @brief Microphone input source.
     */
    MSVAudioSourceMicrophone,
    /**
     * @brief External input source
     * @warning You must use `writeAudioData:error:` method to input audio data when use this input source, if you choose external audio source but don't input audio data, unknown error may come out
     */
    MSVAudioSourceExtern,
};

/**
 * @brief Action to take when enter background
 */
typedef NS_ENUM(NSInteger, MSVBackgroundAction) {
    /**
     * @brief Take no action, caller is responsible to take right actions such as cancel or finish recording current clip
     */
    MSVBackgroundActionContinue,
    /**
     * @brief Finish and save clip being recording when enter background
     */
    MSVBackgroundActionFinish,
};

/**
 * @brief Orientation of preview
 */
typedef NS_ENUM(NSInteger, MSVPreviewOrientation) {
    /**
     * @brief Portrait orientation
     */
    MSVPreviewOrientationPortrait = 0,
    /**
     * @brief Upside down orientation
     */
    MSVPreviewOrientationPortraitUpsideDown = 1,
    /**
     * @brief LandscapeRight orientation
     */
    MSVPreviewOrientationLandscapeRight = 2,
    /**
     * @brief LandscapeLeft orientation
     */
    MSVPreviewOrientationLandscapeLeft = 3,
};

#pragma mark - Audio SampleRate

/**
 * @brief Audio sample rate
 */
typedef NS_ENUM(NSUInteger, MSVAudioSampleRate) {
    /**
     * @brief 48000Hz sample rate
     */
    MSVAudioSampleRate_48000Hz = 48000,
    /**
     * @brief 44100Hz sample rate
     */
    MSVAudioSampleRate_44100Hz = 44100,
    /**
     * @brief 22050Hz sample rate
     */
    MSVAudioSampleRate_22050Hz = 22050,
    /**
     * @brief 11025Hz sample rate
     */
    MSVAudioSampleRate_11025Hz = 11025,
};

#pragma mark - Audio BitRate

/**
 * @brief Audio bitrate
 */

typedef NS_ENUM(NSInteger, MSVAudioBitRate) {
    /**
     * @brief 64Kbps bitrate
     */
    MSVAudioBitRate_64Kbps = 64000,
    /**
     * @brief 96Kbps bitrate
     */
    MSVAudioBitRate_96Kbps = 96000,
    /**
     * @brief 128Kbps bitrate
     */
    MSVAudioBitRate_128Kbps = 128000,
};

#pragma mark - Video File Type

/**
 * @brief Destination file type for recording
 */
typedef NS_ENUM(NSUInteger, MSVFileType) {
    /**
     * @brief mp4 file type，suffixed .mp4
     */
    MSVFileTypeMPEG4,
    /**
     * @brief QuickTime Movie file type，suffixed .mov
     */
    MSVFileTypeQuickTimeMovie, // .mov
    /**
     * @brief m4a file type，suffixed .m4a
     */
    MSVFileTypeM4A, // .m4a
};

/**
 * @brief Video transition type
 */
typedef NS_ENUM(NSInteger, MSVVideoTransitionType) {
    /**
     * @brief Dissolve transition type
     */
    MSVVideoTransitionTypeDissolve,
    /**
     * @brief Wipe right transition type
     */
    MSVVideoTransitionTypeWipeRight,
    /**
     * @brief Wipe left transition type
     */
    MSVVideoTransitionTypeWipeLeft,
    /**
     * @brief Wipe up transition type
     */
    MSVVideoTransitionTypeWipeUp,
    /**
     * @brief Wipe down transition type
     */
    MSVVideoTransitionTypeWipeDown,
    /**
     * @brief Slide right transition type
     */
    MSVVideoTransitionTypeSlideRight,
    /**
     * @brief Slide left transition type
     */
    MSVVideoTransitionTypeSlideLeft,
    /**
     * @brief Slide up transition type
     */
    MSVVideoTransitionTypeSlideUp,
    /**
     * @brief Slide down transition type
     */
    MSVVideoTransitionTypeSlideDown,
    /**
     * @brief Fade transition type
     */
    MSVVideoTransitionTypeFade
};
```

### Color lookup table filter effect(MSVLUTFilterEffect)
```objectivec
/**
 * @brief Color lookup table filter effect class
 */
@interface MSVLUTFilterEffect : NSObject

/**
 * @brief User defined ID field, used to store any information you want
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief LUT(look up table) file location, only local file is supported
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief Time range on main track in draft to use this LUT effect
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

@end
```

### Recording clip configuration object(MSVClipConfiguration)
```objectivec
/**
 * Recorder clip configuration class, used to configure current recording clip
 */
@interface MSVClipConfiguration : NSObject

/**
 * @brief Clip file URL, default to auto generated URL
 */
@property (nonatomic, strong) NSURL *URL;

/**
 * @brief Media file type of the clip to be recorded, default to MSVFileTypeMPEG4
 */
@property (nonatomic, assign) MSVFileType fileType;

/**
 * @brief Clip speed of the clip to be recorded, default to 1.0
 * Recommend configurations:
 * very fast：2.0
 * fast：1.5
 * normal：1.0
 * slow：0.75
 * very slow：0.5
 */
@property (nonatomic, assign) CGFloat speed;

/**
 * @brief Original sound volume of the clip to be recorded, default to 1.0
 */
@property (nonatomic, assign) float volume;

/**
 * @brief View used for recording, only valid when videoConfiguration.source = MSVVideoSourceUIView
 * Default to nil
 */
@property (nonatomic, strong) UIView *viewForRecording;

/**
 * @brief Create a default configuration instance
 * @return Created instance
 */
+ (instancetype)defaultConfiguration;

@end
```

### 背景音效的配置类(MSVBackgroundAudioConfiguration)
```objectivec
/**
 * @brief Background audio configuration class
 */
@interface MSVBackgroundAudioConfiguration : NSObject

/**
 * @brief Background audio source file URL
 * @warning Only local file URL is supported
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief The time range to use of the audio source
 */
@property (nonatomic, assign) MovieousTimeRange timeRange;

/**
 * @brief Volume of background audio, default to the preferredVolume in the audio file
 */
@property (nonatomic, assign) float volume;

/**
 * @brief NO Loop audio play untill recording finishes
 */
@property (nonatomic, assign) BOOL loop;

/**
 * @brief Create a new MSVBackgroundAudioConfiguration instance
 * @param URL Audio source file URL, only local file is supported
 * @param outError Return error if any
 * @return Created instance if success or else nil
 */
+ (instancetype)backgroundAudioConfigurationWithURL:(NSURL *)URL error:(NSError **)outError;

@end
```

### Media clip protocol(MSVClip)
```objectivec
/**
 * @brief Media clip protocol
 */
@protocol MSVClip <NSObject>

/**
 * @brief Media clip URL, only local file is supported
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief Time range on main track in draft
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

/**
 * @brief Check if the clip is valid
 * @param outError If an error occurs, return the error that occurred
 * @return Return YES if valid, or else NO
 */
- (BOOL)validateWithError:(NSError **)outError;

@end
```

### Draft object
```objectivec
/**
 * @brief Video drafts, callers can generate drafts themselves or edit them arbitrarily with drafts，Then use MSVEditor to generate a preview of the draft in real time, or you can use MSVExporter to export the draft。
 */
@interface MSVDraft : NSObject

/**
 * @brief The main track segment collection, the main track segment is arranged in the order of the duration specified by its durationAtMainTrack to form the main track of the video, and the length of the draft is determined by the length of the main track.
 */
@property (nonatomic, strong, readonly) NSArray<MSVMainTrackClip *> *mainTrackClips;

/**
 * @brief The audio clip collection, the audio of the draft is generated by mixing the audio in mainTrackClips and audioClips according to their respective volume.
 */
@property (nonatomic, strong, readonly) NSArray<MSVAudioClip *> *audioClips;

/**
 * @brief Effects collection，so far we support MSVExternalFilterEffect, MSVLUTFilterEffect, MSVImageEffect, MSVRepeatEffect, MSVSpeedEffect, all of this effects can use on the main track.
 */
@property (nonatomic, strong, readonly) NSArray *effects;

/**
 * @brief The original total duration of the draft (regardless of time effects, regardless of timeRange)
 */
@property (nonatomic, assign, readonly) NSTimeInterval originalDuration;

/**
 * @brief Draft duration in total,(regardless of time effects, regardless of timeRange)
 */
@property (nonatomic, assign, readonly) NSTimeInterval duration;

/**
 * @brief The size of the video. If the size of the window does not match the size of the videoSize when previewing,Scaling depends on method specified by editor.previewScalingMode
 */
@property (nonatomic, assign) CGSize videoSize;

/**
 * @brief Valid time area, other parts will be ignored
 * @warning The time interval here is the interval after the time effect is applied,if timeRange was setup by callers, after the main track was relevantly operated like added, deleted, or time effects, the timeRange should be adjusted to ensure that the correct time range is applied.
 */
@property (nonatomic, assign) MovieousTimeRange timeRange;

/**
 * @brief Initialize a draft using the audio and video file address, and the initial draft will contain an address that the MSVMainTrackClip points to.
 * @param URL Audio and video file address
 * @param outError If an error occurs, return the error that occurred
 * @return Initialized successful draft object
 */
+ (instancetype)draftWithAVURL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Create a draft object in using the image file address, and the initial draft will contain an address that the MSVMainTrackClip points to.
 * @param URL Image file address
 * @param outError If an error occurs, return the error that occurred
 * @return If the creation is successful, the draft object is returned, otherwise returns nil
 */
+ (instancetype)draftWithImageURL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Initialize the draft object
 * @param mainTrackClipType The Main track segment type
 * @param URL The Main track segment address
 * @param outError If an error occurs, return the error that occurred
 * @return If the initialization is successful, the draft object is returned, otherwise returns nil
 */
- (instancetype)initWithMainTrackClipType:(MSVMainTrackClipType)mainTrackClipType URL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Generate a preview screenshot of the draft
 * @param count The number of Preview screenshots
 * @param withinTimeRange Whether to generate screenshots in the range of timeRange only
 * @param handler Callback after complete，timestamp：screenshot timostamp，snapshot：Screen capture，error：An error that occurs
 */
- (void)generateSnapshotsWithCount:(NSUInteger)count withinTimeRange:(BOOL)withinTimeRange completionHanler:(void (^)(NSTimeInterval timestamp, UIImage *snapshot, NSError *error))handler;

/**
 * @brief Update the main track segment set. Non-readonly parameters can be adjusted directly in related objects. Other operations (add, delete, replace, etc.) need to be refreshed using this interface.
 * @param mainTrackClips The new main track segment set
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)updateMainTrackClips:(NSArray<MSVMainTrackClip *> *)mainTrackClips error:(NSError **)outError;

/**
 * @brief Update audio clip sets, non-readonly parameters can be adjusted directly in related objects, other operations (add, delete, replace, etc.) need to use this interface to refresh
 * @param audioClips The new audio clip sets
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)updateAudioClips:(NSArray<MSVAudioClip *> *)audioClips error:(NSError **)outError;

/**
 * @brief Update effect sets
 * @param effects the new effect sets
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)updateEffects:(NSArray *)effects error:(NSError **)outError;

/**
 * @brief Verify that the current draft is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateForRecorderWithError:(NSError **)outError;

/**
 * @brief Verify that the draft is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateForWithError:(NSError **)outError;

/**
* @brief The operation of updating the volume is performed in real time. When you need to modify the volume of many clips in batches, please call this method to improve the efficiency of the operation,then make the relevant volume modification, and finally call the -commitVolumeChange method to submit the changes.If you do not follow this paradigm, an update operation will be triggered each time when you modify volume, which will result in:
 */
- (void)beginVolumeChangeTransaction;

/**
 * @brief Submit volume update
 */
- (BOOL)commitVolumeChangeWithError:(NSError **)outError;

/**
 * @brief In addition to the volume updating, you can call this method to start a transaction when you need to manipulate the related properties in batches, and then call the -commitChangeWithError: method to commit all changes after all the operations are completed.
 */
- (void)beginChangeTransaction;

/**
 * @brief Cancel current batch update transaction
 */
- (void)cancelChangeTransaction;

/**
 * @brief Submit an update to the general properties
 */
- (BOOL)commitChangeWithError:(NSError **)outError;

/**
 * @brief Tool method, applying time effects to the original time point, mapping to the time point after applying the time effect
 * @param time Time point when no time effects are applied
 * @return Time point after applying time effects
 */
- (NSTimeInterval)applyEffectToTime:(NSTimeInterval)time;

/**
 * @brief Tool method, which maps the time point when the time effect has been applied to the time point when the time effect is removed
 * @param time Time point when the time effect has been applied
 * @return Time point after removing the time effect
 */
- (NSTimeInterval)removeEffectFromTime:(NSTimeInterval)time;

/**
 * @brief Tool method, applying time effects to the original time interval, mapping to the time interval after applying the time effect
 * @param timeRange Time interval when no time effects are applied
 * @return Time interval after applying time effects
 */
- (MovieousTimeRange)applyEffectToTimeRange:(MovieousTimeRange)timeRange;

/**
 * @brief Tool method that maps the time interval in which the time effect has been applied to the time interval in which the time effect is removed
 * @param timeRange Time interval after applying time effects
 * @return Time interval after removing the time effect
 */
- (MovieousTimeRange)removeEffectFromTimeRange:(MovieousTimeRange)timeRange;

@end
```

### Main track segment
```objectivec
/**
 * @brief Type of main track fragment
 */
typedef NS_ENUM(NSInteger, MSVMainTrackClipType) {
    /**
     * Audio and video media resource type
     */
    MSVMainTrackClipTypeAV,
    /**
     * Image resource type
     */
    MSVMainTrackClipTypeImage
};

/**
 * @brief Main track segment
 */
@interface MSVMainTrackClip : NSObject <NSCopying>

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief Type of main track fragment
 */
@property (nonatomic, assign, readonly) MSVMainTrackClipType type;

/**
 * @brief Media file address
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief At the end of the last segment, the transition time at which this segment begins to connect
 * @warning It should be noted that the sum of the time of the first and last transition effects of each clip cannot exceed the valid duration of the clip, for instance,transition A:clip 1->clip 2, the time duration is 2s; transition B: clip 2->clip 3, the time duration is 3s, so the time of clip 2 at least greater than 5s, otherwise the error will come out.
 */
@property (nonatomic, assign) NSTimeInterval transitionDuration;

/**
 * @brief At the end of the last segment, the type of transition effect that this segment starts to connect, defaults to MSVVideoTransitionTypeDissolve
 */
@property (nonatomic, assign) MSVVideoTransitionType videoTransitionType;

// The following parameters are only valid for the main track segment of the MSVMainTrackClipTypeAV type
#pragma mark - video properties
/**
 * @brief The AVAsset object generated by the audio and video media files, you can get some required parameters here.
 */
@property (nonatomic, strong, readonly) AVAsset *asset;

/**
 * @brief Intercepting the time range that used in the media clip.
 * @warning This time range refers to the time range without the fast and slow processing and reverses processing,Also, if timeRange.startTime + timeRange.duration > total media duration,  the excess part will be ignored.
 */
@property (nonatomic, assign) MovieousTimeRange timeRange;

/**
 * @brief Speed of the audio clip
 * Generally, the following configuration can be performed：
 * Extremely fast：2.0
 * Fast：1.5
 * Standard：1.0
 * Slow：0.75
 * Extremely slow：0.5
 */
@property (nonatomic, assign) float speed;

/**
 * @brief The volume of the media audio, defaults to the preferredVolume that comes with the media file.
 */
@property (nonatomic, assign) float volume;

/**
 * @brief Begin apply reverse effect to this clip
 * @discussion Reverse operation may take some time for the first time, you can psss `progressHandler` to get operation progress, pass `completionHandler` to get operation result. After reverse operation has completed for the first time, apply again will take effect immediately.
 * @param reverse Reverse or not
 * @param progressHandler Operation progress callback, parameter `progress` is operation progress ranges from 0 ~ 1
 * @param completionHandler error 为 nil。Operation result callback, if error comes out, error object will convey the error.
 */
- (void)setReverse:(BOOL)reverse progressHandler:(void(^)(float progress))progressHandler completionHandler:(void(^)(NSError *error))completionHandler;

/**
 * @brief Cancel the running reverse process
 * @warning Cancel reverse operation there is not recover previoud reversed video, if you want to clear applied reverse effect, call `-setReverse:progressHandler:completionHandler:` and pass NO to parameter `reverse`
 */
- (void)cancelReverse;

/**
 * @brief Whether to playback the video backforward
 */
@property (nonatomic, assign, readonly) BOOL reverse;

// The following parameters are only valid for the main track segment of the MSVMainTrackClipTypeAV type
#pragma mark - image properties
/**
 * @brief Image object
 */
@property (nonatomic, assign, readonly) UIImage *image;

/**
 * @brief The duration of the main track segment in the main track, the duration parameter and speed affect each other, the specific operational relationship is： speed = timeRange.duration / durationAtMainTrack
 
 */
@property (nonatomic, assign) NSTimeInterval durationAtMainTrack;

/**
 * @brief Create a main track segment
 * @param type The type of main track segment
 * @param URL The file path of the main track fragment supports local files only 
 * @param outError If an error occurs, return the error that occurred
 * @return If the creation is successful, the completed object is returned, otherwise returns nil
 */
+ (instancetype)mainTrackClipWithType:(MSVMainTrackClipType)type URL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief 使用 AVAsset Create the main track segment of audio and video type using AVAsset
 * @param asset AVAsset object used to create the main track segment
 * @param outError If an error occurs, return the error that occurred
 * @return If the creation is successful, the completed object is returned, otherwise returns nil
 */
+ (instancetype)mainTrackClipWithAsset:(AVAsset *)asset error:(NSError **)outError;

/**
 * @brief Create a main track segment of a image type using UIImage
 * @param image UIImage object used to create the main track segment
 * @param outError If an error occurs, return the error that occurred
 * @return If the creation is successful, the completed object is returned, otherwise returns nil
 */
+ (instancetype)mainTrackClipWithImage:(UIImage *)image duration:(NSTimeInterval)duration error:(NSError **)outError;

/**
 * @brief Initialize a main track segment
 * @param type Type of main track segment
 * @param URL The file path of the main track fragment supports local files only 
 * @param outError If an error occurs, return the error that occurred
 * @return If the initialization is successful, the completed object is returned, otherwise returns nil
 */
- (instancetype)initWithType:(MSVMainTrackClipType)type URL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Initialize a main track segment of an audio and video type using AVAsset
 * @param asset AVAsset object used to create the main track segment
 * @param outError If an error occurs, return the error that occurred
 * @return If the initialization is successful, the completed object is returned, otherwise returns nil
 */
- (instancetype)initWithAsset:(AVAsset *)asset error:(NSError **)outError;

/**
 * @brief Initialize a main track segment of a image type using UIImage
 * @param image UIImage object used to create the main track segment
 * @param duration Image duration
 * @param outError If an error occurs, return the error that occurred
 * @return If the initialization is successful, the completed object is returned, otherwise returns nil
 */
- (instancetype)initWithImage:(UIImage *)image duration:(NSTimeInterval)duration error:(NSError **)outError;

/**
 * @brief Verify that the main track segment is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateWithError:(NSError **)outError;

/**
 * @brief Refresh inner Asset objects，need to be called when received `AVAudioSessionMediaServicesWereResetNotification` notification
 */
- (void)refreshAsset;

@end
```

### Audio clip
```objectivec
/**
 * @brief Audio clip
 */
@interface MSVAudioClip<MSVClip> : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief The URL of the audio clip supports local files only 
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief The underlying AVAsset object can get media parameters such as duration
 */
@property (nonatomic, strong, readonly) AVAsset *asset;

/**
 * @brief The time when the audio clip starts in the main track,the actual duration of the audio clip in the main track is timeRange.duration / speed
 */
@property (nonatomic, assign) NSTimeInterval startTimeAtMainTrack;

/**
* @brief Intercepting the time range used in the audio clip, this time range refers to the time range without the fast and slow processing, please be note: timeRange.startTime + timeRange.duration <= total media duration
 */
@property (nonatomic, assign) MovieousTimeRange timeRange;

/**
 * @brief This time range refers to the time range without the fast and slow processing and reverses processing,Also, if timeRange.startTime + timeRange.duration > total media duration,  the excess part will be ignored.
 * Generally, the following configuration can be performed：
 * Extremely fast：2.0
 * Fast：1.5
 * Standard：1.0
 * Slow：0.75
 * Extremely slow：0.5
 */
@property (nonatomic, assign) float speed;

/**
 * @brief The volume of the audio clip defaults to the value comes with media
 */
@property (nonatomic, assign) float volume;

/**
 * @brief Create audio clips through using media addresses 
 * @param URL The address of the audio clip supports local files only 
 * @param outError If an error occurs, return the error that occurred
 * @return The initialized object will be returned if creation is successfully, otherwise returns nil
 */
+ (instancetype)audioClipWithURL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Use media address to initialize the audio clip
 * @param URL The address of the audio clip supports local files only 
 * @param outError If an error occurs, return the error that occurred
 * @return The initialized object will be returned if initialization is successfully, otherwise returns nil
 */
- (instancetype)initWithURL:(NSURL *)URL error:(NSError **)outError NS_DESIGNATED_INITIALIZER;

/**
 * @brief Verify that the draft is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateWithError:(NSError **)outError;

/**
 * @brief Refresh inner Asset objects，need to be called when received `AVAudioSessionMediaServicesWereResetNotification` notification
 */
- (void)refreshAsset;

@end
```

### Speed effects
```objectivec
/**
 * @brief Speed effects
 */
@interface MSVSpeedEffect : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief The time interval in which the speed effect needs to be applied in the main track. This time interval refers to the time interval without the speed effect applied.
 * @warning It should be noted that if multiple time-related effects (MSVRepeatEffect and MSVSpeedEffect) are inserted, the timeRangeAtMainTrack in the inserted time effect should be based on the timeline after all previous special effects are applied.
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

/**
 * @brief Speed, the actual speed in the corresponding effect time interval after adding the speed effect will become the speed of each MSVMainTrackClip object in the time interval multiplied by the special effect speed.
 */
@property (nonatomic, assign) float speed;

@end
```

### Repeat effects
```objectivec
/**
 * @brief Repeat effects
 */
@interface MSVRepeatEffect : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief The time interval for applying repeat effects in the main track
 * @warning It should be noted that if multiple time-related effects (MSVRepeatEffect and MSVSpeedEffect) are inserted, the timeRangeAtMainTrack in the inserted time effect should be based on the timeline after all previous special effects are applied.
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

/**
 * @brief Repeat times
 */
@property (nonatomic, assign) NSUInteger count;

@end
```

### Image sticker effects
```objectivec
/**
 * @brief Image sticker effects
 */
@interface MSVImagePasterEffect : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief Image which used in image stickers
 */
@property (nonatomic, strong, readonly) UIImage *image;

/**
 * @brief Target location and size of the sticker
 * @warning The sticker will be deformed when the aspect ratio and the aspect ratio of the sticker itself are inconsistent.
 */
@property (nonatomic, assign) CGRect destRect;

/**
 * @brief The time interval for applying an external filter in the main track
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

- (instancetype)initWithImage:(UIImage *)image;

+ (instancetype)pasterEffectWithImage:(UIImage *)image;

@end
```

### Color lookup table filter effects
```objectivec
/**
 * @brief Color lookup table filter effects
 */
@interface MSVLUTFilterEffect : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief LUT(look up table) Filter image file address supports local files only.
 */
@property (nonatomic, strong, readonly) NSURL *URL;

/**
 * @brief The time interval for applying color lookup table filter effects in the main track
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

@end
```

### The external filter effect
```objectivec
/**
 * @brief The external filter effect protocol, which should be followed by all of the external filters.
 */
@protocol MSVExternalFilter <NSObject>

@required
/**
 * @brief Generate a shared external filter object
 * @return Generated external filter object
 */
+ (instancetype)sharedInstance;
@optional

/**
 * @brief Processin video data  
 * @param pixelBuffer Pending video data
 * @param sampleTimingInfo Timestamp of pending video data
 * @return Video data after processing, can feedback the pending video data directly, which is, without any processing.
 */
- (CVPixelBufferRef)processPixelBuffer:(CVPixelBufferRef)pixelBuffer sampleTimingInfo:(CMSampleTimingInfo)sampleTimingInfo;

@end
@interface MSVExternalFilterEffect : NSObject

/**
 * @brief User-defined ID field, business usage is used to distinguish objects
 */
@property (nonatomic, strong) NSString *ID;

/**
 * @brief  The external filter class
 */
@property (nonatomic, assign) Class<MSVExternalFilter> externalFilterClass;

/**
 * @brief The time interval for applying an external filter in the main track
 */
@property (nonatomic, assign) MovieousTimeRange timeRangeAtMainTrack;

@end
```

### Recorder
```objectivec
@class MSVRecorder;

/**
 * @brief Recorder proxy interface
 */
@protocol MSVRecorderDelegate <NSObject>

@optional
/**
 * @brief Callback when the user clicks on the preview interface to focus
 * @param recorder Recorder object that generated the event
 * @param point User clicked location
 */
- (void)recorder:(MSVRecorder *)recorder didFocusAtPoint:(CGPoint)point;

/**
 * @brief The maximum time that the recorder reaches the configuration
 * @param recorder Recorder object that generated the event
 */
- (void)recorderDidReachMaxDuration:(MSVRecorder *)recorder;

/**
 * @brief An error occurred while the recorder was playing background music
 * @param recorder Recorder object that generated the event
 * @param error Specific error
 */
- (void)recorder:(MSVRecorder *)recorder didPlayBackgroundAudioError:(NSError *)error;

/**
 * @brief The callback of the duration of the current recorded clip has updated, the caller can update the UI feedback recording progress in this callback.
 * @param recorder Recorder object that generated the event
 * @param currentClipDuration The duration of current clip
 */
- (void)recorder:(MSVRecorder *)recorder currentClipDurationDidUpdated:(NSTimeInterval)currentClipDuration;

/**
 * @brief The camera obtains the callback of the video data, and the caller can customize and process the video data by using the method, and the processed data will be feedback to the preview image and encoded into the generated segment.
 * @param recorder Recorder object that generated the event
 * @param pixelBuffer The pending video data
 * @return Processed video data
 */
- (CVPixelBufferRef)recorder:(MSVRecorder *)recorder didGetPixelBuffer:(CVPixelBufferRef)pixelBuffer;

@end

/**
 * @brief Recorder
 */
@interface MSVRecorder : NSObject

/**
 * @brief Recorder Automatically generated draft object that can be used to obtain draft objects for editing or exporting.
 */
@property (nonatomic, strong, readonly) MSVDraft *draft;

/**
 * @brief Video parameter configuration object
 * @warning Please do not modify the obtained videoConfiguration object, otherwise, an undefined error will occur. If the parameters were modified, please use related running properties updating interface or destroy the current Recorder and re-generate.
 */
@property (nonatomic, strong, readonly) MSVRecorderVideoConfiguration *videoConfiguration;

/**
 * @brief Audio parameter configuration object
 * @warning Please do not modify the obtained videoConfiguration object, otherwise, an undefined error will occur. If the parameters were modified, please use related running properties updating interface or destroy the current Recorder and re-generate.
 */
@property (nonatomic, strong, readonly) MSVRecorderAudioConfiguration *audioConfiguration;

/**
 * @brief The duration has been recorded of the current clip
 */
@property (nonatomic, assign, readonly) NSTimeInterval currentClipDuration;

/**
 * @brief The duration of all the saved clips
 */
@property (nonatomic, assign, readonly) NSTimeInterval recordedClipsDuration;

/**
 * @brief The maximum recording duration,  the recording duration here refers to the sum of the duration of all the clips, when the recording duration reaches the maximum value, the delegate's `recorderDidReachMaxDuration` method will be called, when the value is less than or equal to 0, there is no maximum duration limit.
 */
@property (nonatomic, assign) NSTimeInterval maxDuration;

/**
 * @brief Preview view
 */
@property (nonatomic, strong, readonly) UIView *preview;

/**
 * @brief Proxy object for receiving event callbacks
 */
@property (nonatomic, weak) id<MSVRecorderDelegate> delegate;

/**
 * @brief  The queue used by the proxy method callback, if not specified, will be called back in the main thread.
 */
@property (nonatomic, strong) dispatch_queue_t delegateQueue;

/**
 * @brief The fill mode used when the preview window does not match the configured videoSize ratio, the default is MovieousScalingModeAspectFit.
 */
@property (nonatomic, assign) MovieousScalingMode previewScalingMode;

/**
 * @brief whether it is in the status of recording or not
 */
@property (nonatomic, assign) BOOL recording;

/**
 * @brief Whether to mirror the front camera preview, the default is YES
 */
@property (nonatomic, assign) BOOL mirrorFrontPreview;

/**
 * @brief Whether to mirror the rear camera preview, the default is NO
 */
@property (nonatomic, assign) BOOL mirrorBackPreview;

/**
 * @brief Whether to mirror the video encoded by the front camera, the default is NO
 */
@property (nonatomic, assign) BOOL mirrorFrontEncoded;

/**
 * @brief Whether to mirror the video encoded by the rear camera, the default is NO
 */
@property (nonatomic, assign) BOOL mirrorBackEncoded;

/**
 * @brief Whether to open the internal focus view, the default is NO
 */
@property (nonatomic, assign) BOOL innerFocusView;

/**
 * @brief Obtain a screenshot
 */
@property (nonatomic, strong, readonly) UIImage *snapshot;

/**
 * @brief The action is taken automatically during recording operation runs from the background. If the operation is not automatic, the caller needs to perform the appropriate processing (complete or cancel the recording of the current clip). The default is MSVBackgroundActionContinue.
 */
@property (nonatomic, assign) MSVBackgroundAction backgroundAction;

/**
 * @brief Whether the flashlight is turned on, the default is NO
 */
@property (nonatomic, assign, readonly) BOOL torchOn;

/**
 * @brief  the interface of switching flashlight
 * @param torchOn Turn it on or off
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setTorchOn:(BOOL)torchOn error:(NSError **)outError;

/**
 * @brief The frame rate of capturing video
 */
@property (nonatomic, assign, readonly) NSUInteger frameRate;

/**
 * @brief Setup the frame rate of capturing video
 * @param frameRate New frame rate
 * @param outError If an error occurs, return the error that occurred
 * @return  It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setFrameRate:(NSUInteger)frameRate error:(NSError **)outError;

/**
 * @brief The resolution captured by the camera, the default is AVCaptureSessionPresetHigh
 */
@property (nonatomic, strong, readonly) AVCaptureSessionPreset cameraResolution;

/**
 * @brief Setup the resolution captured by the camera
 * @param cameraResolution New resolution rate
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setCameraResolution:(AVCaptureSessionPreset)cameraResolution error:(NSError **)outError;

/**
 * @brief Camera position, the default is AVCaptureDevicePositionBack
 */
@property (nonatomic, assign, readonly) AVCaptureDevicePosition cameraPosition;

/**
 * @brief Set the position of the camera
 * @param cameraPosition New camera position
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setCameraPosition:(AVCaptureDevicePosition)cameraPosition error:(NSError **)outError;

/**
 * @brief The direction of the camera rotation, the default is AVCaptureVideoOrientationPortrait
 */
@property (nonatomic, assign, readonly) AVCaptureVideoOrientation cameraOrientation;

/**
 * @brief Setup the camera orientation
 * @param cameraOrientation New camera orientation
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setCameraOrientation:(AVCaptureVideoOrientation)cameraOrientation error:(NSError **)outError;

/**
 * @brief Camera zoom factor, default is 1
 */
@property (nonatomic, assign, readonly) CGFloat cameraZoomFactor;

/**
 * @brief Setup camera zoom factor
 * @param cameraZoomFactor New camera zoom factor
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setCameraZoomFactor:(CGFloat)cameraZoomFactor error:(NSError **)outError;

/**
 * @brief Focus and exposure reference points (the position of the point is referenced by the acquisition resolution specified by cameraResolution, the upper left corner is the origin, and the right and down orientations are the orientation in which the x value and the y value increase respectively)
 */
@property (nonatomic, assign, readonly) CGPoint pointOfInterest;

/**
 * @brief Set the reference point for focus and exposure
 * @param pointOfInterest New reference point for focus and exposure
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setPointOfInterest:(CGPoint)pointOfInterest error:(NSError **)outError;

/**
 * @brief Initialize an empty recorder
 * @param audioConfiguration Audio configuration that introducing nil will use the default configuration
 * @param videoConfiguration Video configuration that introducing nil will use the default configuration
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the initialization is successful, otherwise, it returns NO
 */
- (instancetype)initWithAudioConfiguration:(MSVRecorderAudioConfiguration *)audioConfiguration videoConfiguration:(MSVRecorderVideoConfiguration *)videoConfiguration error:(NSError **)outError;

/**
 * @brief Initialize a recorder with a saved draft, from the time on the recorder created with this method, all recorded clips and background music will be added to the draft object used for initialization.
 * @param draft Initialize the used draft object
 * @param audioConfiguration Audio configuration that introducing nil will use the default configuration
 * @param videoConfiguration Video configuration that introducing nil will use the default configuration
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the initialization is successful, otherwise, it returns NO
 */
- (instancetype)initWithDraft:(MSVDraft *)draft AudioConfiguration:(MSVRecorderAudioConfiguration *)audioConfiguration videoConfiguration:(MSVRecorderVideoConfiguration *)videoConfiguration error:(NSError **)outError;

/**
 * @brief Start collecting audio and video, call this method will request the usage permission of audio and video (if the specified audio and video data source is the camera or microphone)
 * @param completionHandler Callback is completed, audioGranted：Whether to obtain audio rights, audioError：Error in audio component initialization, videoGranted：Whether you have obtained the captured permissions of the video,videoError：Error in video component initialization
 */
- (void)startCapturingWithCompletionHandler:(void(^)(BOOL audioGranted, NSError *audioError, BOOL videoGranted, NSError *videoError))completionHandler;

/**
 * @brief Start collecting audio and video, call this method will request the usage permission of audio and video (if the specified audio and video data source is the camera or microphone)
 * @param compromise Whether to allow to compromise some parameters which are not supported by the current device to ensure component initialization is successful.
 * @param completionHandler Callback is completed, audioGranted：Whether to obtain audio rights, audioError：Error in audio component initialization, videoGranted：Whether you have obtained the captured permissions of the video,videoError：Error in video component initialization
 */
- (void)startCapturingWithCompromise:(BOOL)compromise completionHandler:(void(^)(BOOL audioGranted, NSError *audioError, BOOL videoGranted, NSError *videoError))completionHandler;

/**
 * @brief Stop capturing
 */
- (void)stopCapturing;

/**
 * @brief Start recording with the default configuration
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)startRecordingWithError:(NSError **)outError;

/**
 * @brief Start recording with the specified configuration
 * @param clipConfiguration Record the used configuration, the default configuration will be used after passing nil
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)startRecordingWithClipConfiguration:(MSVClipConfiguration *)clipConfiguration error:(NSError **)outError;

/**
 * @brief Complete the record
 * @param completionHandler Stop the successful callback, clip: record the generated main track segment object, error: error occurred
 */
- (void)finishRecordingWithCompletionHandler:(void(^)(MSVMainTrackClip *clip, NSError *error))completionHandler;

/**
 * @brief Delete the last recorded clip
 */
- (void)discardLastClip;

/**
 * @brief Delete the segment of the specified index
 * @param index The index of the segment that needs to be deleted
 */
- (void)discardClipAtIndex:(NSUInteger)index;

/**
 * @brief Delete all recorded clips
 */
- (void)discardAllClips;

/**
 * @brief Switch camera
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)switchCameraWithError:(NSError **)outError;

/**
 * @brief Setup watermark
 * @param waterMark Watermark image
 * @param position Watermark position
 */
- (void)setWaterMark:(UIImage *)waterMark position:(CGPoint)position;

/**
 * @brief Clear watermark
 */
- (void)clearWaterMark;

/**
 * @brief Turn on device orientation to auto detection
 * @param orientationUpdatedBlock The callback when the rotated orientation of the device changes
 */
- (void)enableAutoOrientationAdaptionWithOrientationUpdatedBlock:(void(^)(UIDeviceOrientation))orientationUpdatedBlock;

/**
 * @brief Turn off device orientation to auto detection
 */
- (void)disableAutoOrientationAdaption;

/**
 * @brief Update the draft object used by the recorder
 * @param draft New draft object
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)updateDraft:(MSVDraft *)draft error:(NSError **)outError;

/**
 * @brief Background audio configuration object
 */
@property(nonatomic, strong, readonly) MSVBackgroundAudioConfiguration *backgroundAudioConfiguration;

/**
 * @brief The background music can be set or canceled only after the recording has not started or all the recorded clips have been deleted, the background music information after set will be reflected in the draft.audioClips and will not be encoded into the generated recording file directly,  so that the background music can be replaced at any time during the editing phase. 
 * @param configuration The background audio configuration object after nil passed will clear the background audio
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)setBackgroundAudioWithConfiguration:(MSVBackgroundAudioConfiguration *)configuration error:(NSError **)outError;

/**
 * @brief The interface of externally write the video data, please ensure the configuration if this interface will be used.  videoConfiguration.source = MSVVideoSourceExtern
 * @param videoData Video data to be written
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)writeVideoData:(CMSampleBufferRef)videoData error:(NSError **)outError;

/**
 * @brief The interface of externally write the video data, please ensure the configuration if this interface will be used. audioConfiguration.source = MSVAudioSourceExtern
 * @param audioData Audio data to be written
 * @param outError  * @param outError If an error occurs, return the error that occurred
 * @return It returns YES when the setting is successful, otherwise, it returns NO
 */
- (BOOL)writeAudioData:(CMSampleBufferRef)audioData error:(NSError **)outError;

@end
```

### Audio configuration
```objectivec
/**
 * @brief Audio configuration class
 */
@interface MSVRecorderAudioConfiguration : NSObject
<
MovieousMicrophoneConfiguration
>

/**
 * @brief Audio input source, the default is MSVAudioSourceMicrophone
 */
@property (nonatomic, assign) MSVAudioSource source;

/**
 * @brief The number of channels for collecting audio data. The default is 1
 * @warning Not all capturing devices support multichannel data acquisition
 */
@property (assign, nonatomic) NSUInteger numberOfChannels;

/**
 * @brief Audio sample rate sampleRate The default is MSVAudioSampleRate_44100Hz
 */
@property (assign, nonatomic) MSVAudioSampleRate sampleRate;

/**
 * @brief Audio encoding rate bitRate The default is MSVAudioBitRate_128Kbps
 */
@property (assign, nonatomic) MSVAudioBitRate bitRate;

/**
 * @brief Create a default configuration of the MSVAudioConfiguration instance
 * @return Create a default object of MSVAudioConfiguration
 */
+ (instancetype)defaultConfiguration;

/**
 * @brief Verify the object is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateWithError:(NSError **)outError;

@end
```

## Video configuration
```objectivec
/**
 * @brief Video configuration class
 */
@interface MSVRecorderVideoConfiguration : NSObject
<
MovieousCameraConfiguration
>

/**
 * @brief Video input source, the default is MSVAudioSourceMicrophone
 */
@property (nonatomic, assign) MSVVideoSource source;

/**
 * @brief The frame rate of the video data captured when using MSVVideoSourceCamera, the default is 30
 */
@property (assign, nonatomic) NSUInteger frameRate;

/**
 * @brief The resolution of the video captured when using MSVVideoSourceCamera, the default is AVCaptureSessionPresetHigh
 */
@property (strong, nonatomic) AVCaptureSessionPreset cameraResolution;

/**
 * @brief Front preview mirror is enabled or not when using MSVVideoSourceCamera. The default is YES.
 */
@property (assign, nonatomic) BOOL mirrorFrontPreview;

/**
 * @brief Rear preview mirror is enabled or not when using MSVVideoSourceCamera, the default is NO
 */
@property (assign, nonatomic) BOOL mirrorBackPreview;

/**
 * @brief When using MSVVideoSourceCamera, Whether the recorded stream is enable to mirror in the front camera,  the default is NO
 */
@property (assign, nonatomic) BOOL mirrorFrontEncoded;

/**
 * @brief When using MSVVideoSourceCamera, whether the recorded stream is enabled to mirror in the rear camera, the default NO
 */
@property (assign, nonatomic) BOOL mirrorBackEncoded;

/**
 * @brief The captured camera position when using MSVVideoSourceCamera, the default is AVCaptureDevicePositionBack
 */
@property (assign, nonatomic) AVCaptureDevicePosition cameraPosition;

/**
 * @brief The camera rotation orientation captured when using MSVVideoSourceCamera, the default is AVCaptureVideoOrientationPortrait
 */
@property (assign, nonatomic) AVCaptureVideoOrientation cameraOrientation;

/**
 * @brief   Video resolution when encoding, default (1280, 720)
 * @discussion It should be noted that this parameter affects the resolution of the video encoding, rather than the preview size of the data captured by the camera. When the image size passed to the encoder is different from this size, it will be generated in the same way The video ensures that the image does not appear to be compressed (but the cropping phenomenon occurs when the ratio of the encoded video is different from the ratio of the captured video).
 */
@property (assign, nonatomic) CGSize size;

/**
 * @brief Average video encoding rate. The default is 1024*1000
 * @discussion unit is bps(Bits per Second). The parameters of the video encoding are not constant values in the actual process, so the average video encoding rate can be set only.
 */
@property (nonatomic, assign) NSUInteger averageVideoBitRate;

/**
 * @brief The Maximum Interval of video encoding Keyframe. (GOP)
 * @discussion During h.264 encoding, The maximum number of frames between two keyframes typically is twice or three times than the fps. The default is 2*fps
 */
@property (nonatomic, assign) NSUInteger videoMaxKeyframeInterval;

/**
 * @brief During H.264 encoding, the Profile Level will be used.
 * @discussion By default, AVVideoProfileLevelH264HighAutoLevel is used, if you have additional requirements for video encoding, you can change it yourself if understanding that the impacts  from this parameter.
 * @warning When you are not sure about the impact of this parameter change on resolution requirements, code rate, etc., please do not change it.
 */
@property (nonatomic, copy) NSString *videoProfileLevel;

/**
 * @brief Create a default configuration of the MSVVideoConfiguration instance.
 * @return The created default MSVVideoConfiguration object 
 */
+ (instancetype)defaultConfiguration;

/**
 * @brief Verify the object is valid or not
 * @param outError If an error occurs, return the error that occurred
 * @return Valid operation return YES, invalid operation return NO
 */
- (BOOL)validateWithError:(NSError **)outError;

@end
```

### Editer
```objectivec
extern NSString *kMSVEditorCurrentTimeUpdatedNotification;

@class MSVEditor;
@protocol MSVEditorDelegate <NSObject>

@optional
- (void)editor:(MSVEditor *)editor currentTimeDidUpdate:(NSTimeInterval)currentTime;

- (void)editor:(MSVEditor *)editor playStateChanged:(BOOL)playing;

@end

@interface MSVEditor : NSObject

/**
 * @brief Please operate the underlying, draft object and related edits through the draft object
 */
@property (nonatomic, strong, readonly) MSVDraft *draft;

/**
 * @brief Edit preview view
 */
@property (nonatomic, strong, readonly) UIView *preview;

/**
 * @brief The area where the video content is displayed in the preview
 */
@property (nonatomic, assign, readonly) CGRect contentFrame;

/**
 * @brief The fill mode of preview view , the default is MovieousScalingModeAspectFit
 */
@property (nonatomic, assign) MovieousScalingMode previewScalingMode;

/**
 * @brief Current playback progress
 */
@property (nonatomic, assign, readonly) NSTimeInterval currentTime;

/**
 * @brief Whether it currently playback 
 */
@property (nonatomic, assign, readonly) BOOL playing;

/**
 * @brief Whether to loop
 */
@property (nonatomic, assign) BOOL loop;

/**
 * @brief Editor proxy object
 */
@property (nonatomic, weak) id<MSVEditorDelegate> delegate;

/**
 * @brief The queue callback made by the proxy method, the default is the main queue
 */
@property (nonatomic, strong) dispatch_queue_t delegateQueue;

/**
 * @brief Initialize an MSVEditor object with an audio and video URL
 * @param URL Audio and video address
 * @param outError If an error occurs, return the error that occurred
 * @return It returns the draft object if the initialization is successful, returns nil if it fails.
 */
- (instancetype)initWithAVURL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Initialize an MSVEditor object with a image URL
 * @param URL Image address
 * @param outError If an error occurs, return the error that occurred
 * @return It returns the draft object if the initialization is successful, returns nil if it fails.
 */
- (instancetype)initWithImageURL:(NSURL *)URL error:(NSError **)outError;

/**
 * @brief Initialize an MSVEditor object with a draft object
 * @param draft Draft object
 * @param outError If an error occurs, return the error that occurred
 * @return It returns the draft object if the initialization is successful, returns nil if it fails.
 */
- (instancetype)initWithDraft:(MSVDraft *)draft error:(NSError **)outError;

/**
 * @brief Start previewing
 */
- (void)play;

/**
 * @brief Pause previewing
 */
- (void)pause;

/**
 * @brief The player fast forwards to the appropriate position
 * @param time Target position
 * @param completionHandler The callback at the end of fast-forwarding, finished: whether the fast forward is complete。
 */
- (void)seekToTime:(NSTimeInterval)time completionHandler:(void (^)(BOOL finished))completionHandler;

@end
```

### Exporter
```objectivec
NS_ASSUME_NONNULL_BEGIN

@interface MSVVideoExporter : NSObject

/**
 * @brief Draft object
 */
@property (nonatomic, strong, readonly, nullable) MSVDraft *draft;

/**
 * @brief Whether the export task is running
 */
@property (nonatomic, assign) BOOL running;

/**
 * @brief Whether to export the video to the album at the same time, the default is NO
 */
@property (assign, nonatomic) BOOL saveToPhotosAlbum;

/**
 * @brief The file type of the video export, the default is MSVFileTypeMPEG4(.mp4)
 */
@property (assign, nonatomic) MSVFileType outputFileType;

/**
 * @brief The path of the video export only supports the local file address. The default is the automatically generated address.
 */
@property (strong, nonatomic, nullable) NSURL *outputURL;

/**
 * @brief The bitrate of the video, the default is the bitrate of the original video.
 */
@property (assign, nonatomic) NSUInteger videoBitrate;

/**
 * @brief The number of channels of the exported video, the default number of channels use the original audio
 */
@property (nonatomic, assign) UInt32 numberOfChannels;

/**
 * @brief The audio sample rate of the video that exported, using the original audio sample rate by default.
 */
@property (nonatomic, assign) Float64 sampleRate;

/**
 * @brief The audio bitrate of the video that exported, using the original video's bitrate by default.
 */
@property (nonatomic, assign) Float64 audioBitRate;

/**
 * @brief Whether set up the transmission in the network environment, the default is YES
 */
@property (assign, nonatomic) BOOL shouldOptimizeForNetworkUse;

/**
 * @brief Export progress callback
 */
@property (nonatomic, copy, nullable) void(^progressHandler)(float progress);

/**
 * @brief Export failure callback
 */
@property (nonatomic, copy, nullable) void(^failureHandler)(NSError *error);

/**
 * @brief Export successful callback
 */
@property (nonatomic, copy, nullable) void(^completionHandler)(NSURL *URL);

/**
 * @brief Export objects with initialization draft
 * @param draft Draft object that needs to be exported
 * @param outError If an error occurs, return the error that occurred
 * @return It returns the initialized object if the initial succeeded, otherwise returns nil
 */
- (instancetype _Nullable)initWithDraft:(MSVDraft *_Nullable)draft error:(NSError * _Nullable * _Nullable)outError;

/**
 * @brief Update draft
 * @param draft New draft
 * @param outError If an error occurs, return the error that occurred
 * @return It returns YES if update successfully otherwise returns NO
 */
- (BOOL)updateDraft:(MSVDraft *)draft error:(NSError * _Nullable * _Nullable)outError;

/**
 * @brief Start exporting tasks
 */
- (void)startExport;

/**
 * @brief Cancel the export task
 */
- (void)cancelExport;

NS_ASSUME_NONNULL_END

@end
```
