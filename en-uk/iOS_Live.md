# iOS SDK
## [CHANGELOG](https://github.com/movieous-team/MovieousLive-Cocoa-Release/blob/master/CHANGELOG.en-us.md)
  
## Quick start
Dear developer, welcome to use the live streaming SDK from Movieous . This tutorial will guide you how to acheive integration of live video streaming functions in your iOS project. But first, we assume that you already know the underlying syntax of Objective-C or Swift.

### Add dependency
* First, the [iOS live streaming SDK installation guide](https://github.com/movieous-team/MovieousLive-Cocoa-Release/blob/master/README.en-us.md#how-to-install) for your reference to introduce MovieousLive to your project.

### Introduce related header files
Add the following statement to the page original source file where you need to integrate the live streaming SDK

Objective-C
```objective-c
#import <MovieousLive/MovieousLive.h>
```

Swift
```swift
import MovieousLive
```

### Create an audio and video configuration Object
`MovieousLive` respectively uses the `MLAudioConfiguration` and `MLVideoConfiguration` objects for audio and video parameter configuration. Both configuration objects support the use of the `-defaultConfiguration` method to get a default configuration object. After getting the default configuration object, we can change the relevant parameters for subsequent use according to business requirements.


Objective-C
```objectivec
// create default configuration object
MLAudioConfiguration *audioConfiguration = [MLAudioConfiguration defaultConfiguration];
MLVideoConfiguration *videoConfiguration = [MLVideoConfiguration defaultConfiguration];
// configure what needs to be changed
audioConfiguration.bitrate = 128 * 1024;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPresetHigh;
```

Swift
```swift
// create default configuration object
let audioConfiguration = MLAudioConfiguration.default();
let videoConfiguration = MLVideoConfiguration.default();
// configure what needs to be changed
audioConfiguration.bitrate = 128 * 1024;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPresetHigh;
```

### Create `MLStreamer`
Create the `MLStreamer` object using the `MLAudioConfiguration` and `MLVideoConfiguration` objects created in the previous step. After the creation is successful, you can use this object to control the live behavior, state acquisition and parameter adjustment.

Objective-C
```objectivec
NSError *error;
MLStreamer *streamer = [MLStreamer streamerWithAudioConfiguration:audioConfiguration videoConfiguration:videoConfiguration error:&error];
if (error) {
    // error handling
}
```

Swift
```swift
do {
    try streamer = MLStreamer(audioConfiguration: audioConfiguration, videoConfiguration: videoConfiguration)
} catch {
    // error handling
}
```

### Start capturing
Start the capturing of audio and video, After the capturing interface is called, the system will apply for the camera and microphone permissions to users (if `enable` is configured to `YES` in the corresponding configuration), make sure that you have already added  `Privacy - Camera Usage Description` and `Privacy - Microphone Usage Description` description in `Info.plist` in the project otherwise calling this interface will cause the program to crash.

Objective-C
```objectivec
[streamer startCapturingWithCompletion:^(BOOL audioGranted, NSError *_Nullable audioError, BOOL videoGranted, NSError *_Nullable videoError) {
    // response as needed
}];
```

Swift
```swift
streamer.startCapturing { (audioGranted: Bool, audioError: Error?, videoGranted: Bool, videoError: Error?) in
    // response as needed
}
```

### Adding a preview-view to the subview of the current one
`MLStreamer` provides `@property(nonatomic, strong, readonly) UIView *previewView` property to preview the data captured by the camera, you can add it to the subview of your current view and resize it to start previewing the camera enter.

Objective-C
```objectivec
[self.view addSubview:streamer.previewView];
streamer.previewView.frame = self.view.bounds;
```

Swift
```swift
view.addSubview(streamer.previewView)
streamer.previewView.frame = view.bounds
```

### Start pushing stream 
push stream with remote server URL

Objective-C
```objectivec
NSError *error;
[controller startPushingWithServerURL:serverURL error:&error];
if (error) {
    // do error handling
}
```

Swift
```swift
do {
    try streamer?.startPushing(withServerURL: serverURL)
} catch {
    // do error handling
}
```

- Note: start pushing requires that you have filed the domain name used by the push stream to [movieous] (https://movieous.cn/) otherwise you will be unable to push your stream.

### Setting delegate of `MLStreamer` 
`MLStreamer` informs the status of the live broadcast by  delegate, You can obtain notifications including live broadcast status and other related information by implementing the method defined in `MLStreamerDelegate`.

Objective-C
```objectivec
{
    ...
    streamer.delegate = self
    ...
}

// get live streaming error callback
- (void)streamer:(MLStreamer *)streamer didFailedWithError:(NSError *)error {
    // do error handling
}
```

Swift
```swift
{
    ...
    streamer.delegate = self
    ...
}

// get live streaming error callback
func streamer(_ streamer: MLStreamer, didFailedWithError error: Error) {
    // do error handling
}
```

### End pushing stream
After  pushing stream  is completed, you can call the following method to end the pushing stream  (you can still use this object to call the `-startPushingWithServerURL:error:` method again to start the pushing stream again after the end)

Objective-C
```objectivec
[streamer stopPushing];
```

Swift
```swift
streamer.stopPushing();
```

## [API Reference](https://developer.movieous.cn/ios/live/api/)