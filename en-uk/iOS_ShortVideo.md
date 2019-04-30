# iOS SDK
## [CHANGELOG](https://github.com/movieous-team/MovieousShortVideo-Cocoa-Release/blob/master/CHANGELOG.zh-cn.md)

## Quict start
Dear developer, welcome to use the short video SDK from Movieous . This tutorial will guide you how to acheive integration of short video recording, editing and exporting functions in your iOS project. But first, we assume that you already know the underlying syntax of Objective-C or Swift.

### Add dependency
* First, the [iOS Short Video SDK installation guide](https://github.com/movieous-team/MovieousShortVideo-Cocoa-Release/blob/master/README.md#how-to-install) for your reference to introduce MovieousShortVideo to your project.

### Introduce related header files
Add the following statement to the page original source file where you need to integrate the short video SDK

```objective-c
#import <MovieousShortVideo/MovieousShortVideo.h>
```

Swift
```swift
import MovieousShortVideo
```

Then, you can selectively integrate video recording, video editing or video export modules according to your requirements.

### Video recording
- Add microphone and camera usage permission
Open the project configuration page and add `Privacy - Camera Usage Description` and `Privacy - Microphone Usage ` description to the Info option.
![image](./images/authorization.png)

- Generate configuration objects for audio and video and make modification to related parameters as requirements.


Objective-C
```objective-c
MSVRecorderAudioConfiguration *audioConfiguration = [MSVRecorderAudioConfiguration defaultConfiguration];
MSVRecorderVideoConfiguration *videoConfiguration = [MSVRecorderVideoConfiguration defaultConfiguration];
// You can change parameters according to your requirements.
audioConfiguration.mute = NO;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPreset1280x720;
```

Swift
```swift
let audioConfiguration = MSVRecorderAudioConfiguration.default()
let videoConfiguration = MSVRecorderVideoConfiguration.default()
// You can change parameters according to your requirements.
audioConfiguration.mute = false
vide

- Generate the recording core control object

Objective-C
```objective-c
_recorder = [[MSVRecorder alloc] initWithAudioConfiguration:audioConfiguration videoConfiguration:videoConfiguration error:&error];
if (error) {
    // show error alert
    SHOW_ERROR_ALERT;
    return;
}
// set recorder event delegate
_recorder.delegate = self;
// insert previewView to view stack to preview camera capturing result.
_recorder.preview.frame = self.view.frame;
[self.view insertSubview:_recorder.previewView atIndex:0];
```

Swift
```swift
do {
    recorder = try MSVRecorder(audioConfiguration: audioConfiguration, videoConfiguration: videoConfiguration)
} catch {
    // show error alert
    ShowAlert(error: error, controller: self)
}
guard recorder != nil else {
    return
}
// set recorder event delegate
recorder!.delegate = self
recorder!.previewView.frame = self.view.frame
// insert previewView to view stack to preview camera capturing result.
self.view.insertSubview(recorder!.previewView, at: 0)
```

- Start audio and video acquisition

Objective-C
```objective-c
[_recorder startCapturingWithCompletion:^(BOOL audioGranted, NSError *audioError, BOOL videoGranted, NSError *videoError) {
    if (videoError) {
        // show error alert
        SHOW_ALERT(@"error", videoError.localizedDescription, @"ok");
        return;
    }
    if (!videoGranted) {
        // show error alert
        SHOW_ALERT(@"warning", @"video not authorized", @"ok");
        return;
    }
    if (audioError) {
        // show error alert
        SHOW_ALERT(@"error", audioError.localizedDescription, @"ok");
        return;
    }
    if (!audioGranted) {
        // show error alert
        SHOW_ALERT(@"warning", @"audio not authorized", @"ok");
        return;
    }
}];
```

Swift
```swift
weak var wSelf = self
recorder.startCapturing { (audioGranted: Bool, audioError: Error?, videoGranted: Bool, videoError: Error?) in
    guard let strongSelf = wSelf else { return }
    if let videoError = videoError {
        // show error alert
        ShowAlert(error: videoError, controller: strongSelf)
        return
    }
    if (!videoGranted) {
        // show error alert
        ShowAlert(@"video not authorized", self);
        return;
    }
    if let audioError = audioError {
        // show error alert
        ShowAlert(error: audioError, controller: strongSelf)
        return
    }
        if (!audioGranted) {
        // show error alert
        ShowAlert(@"audio not authorized", self);
        return;
    }
}
```

- Start recording the video clip

Objective-C
```objective-c
if (![_recorder startRecordingWithError:&error]) {
    SHOW_ERROR_ALERT;
    return;
}
```

Swift
```swift
do {
    try recorder!.startRecording()
} catch {
    ShowAlert(error: error, controller: self)
}
```

- Complete current clip recording

Objective-C
```objective-c
[_recorder finishRecordingWithCompletionHandler:^(MSVMainTrackClip *clip, NSError *error) {
    // you can acquire information of current recorded clip from the generated MSVMainTrackClip object.
    if (error) {
        SHOW_ERROR_ALERT;
    }
}];
```

Swift
```swift
recorder!.finishRecording { (clip, error) in
    // you can acquire information of current recorded clip from the generated MSVMainTrackClip object.
    if (error != nil) {
        ShowAlert(error: error, controller: self)
    }
}
```

After completing the current clip recording, you can return to the section `Start Recording Clips` to continue recording the next clip,All recorded clips will be merged sequentially into a video draft and saved in the `_recorder.draft` object, which can be passed to the editor for editing or passed to the exporter for directly export as a composite file.

### Video editing
- Create the editor core control class

Objective-C
```objective-c
// You can use media resources generated during recording or existing resources(like from photo album or downloaded from remote server) to generate this MSVDraft object. 
_editor = [MSVEditor editorWithDraft:draft error:&error];
if (error) {
    SHOW_ERROR_ALERT;
    return;
}
_editor.delegate = self;
_editor.loop = YES;
// insert previewView to view stack to preview edit result.
[self.view insertSubview:_recorder.previewView atIndex:0];
```

Swift
```swift
do {
// You can use media resources generated during recording or existing resources(like from photo album or downloaded from remote server) to generate this MSVDraft object. 
    try editor = MSVEditor(draft: draft)
} catch {
    ShowAlert(error: error, controller: self)
}
guard editor != nil else {
    return
}
editor!.delegate = self
editor!.loop = true
// insert previewView to view stack to preview edit result.
self.view.insertSubview(editor?.previewView, at: 0)
```

- Preview draft

Objective-C
```objective-c
[_editor play];
```

Swift
```swift
editor!.play()
```

- Video editing
`MovieousShortVideo`  use `MSVDraft` object to storage video edited information,You can adjust the parameters of `_editor.draft` arbitrarily according to your requirements. After the adjustment, the preview view will be refreshed in real time. The video editing operation demonstration as below.

Objective-C
```objective-c
_editor.draft.timeRange = (MovieousTimeRange){
    // trim start time
    startTime,
    // trim time duration
    duration,
};
```

Swift
```swift
editor!.draft.timeRange = MovieousTimeRange(startTime: startTime, duration: duration)
```

### Video export
- Create the export core control object

Objective-C
```objective-c
NSError *error;
// You can use media resources generated during recording or existing resources(like from photo album or downloaded from remote server) to generate this MSVDraft object. 
_exporter = [[MSVExporter alloc] initWithDraft:_draft error:&error];
if (error) {
    SHOW_ERROR_ALERT;
    return;
}
_exporter.saveToPhotosAlbum = YES;
__weak typeof(self) wSelf = self;
_exporter.progressHandler = ^(float progress) {
    NSLog(@"progress: %f", progress);
};
_exporter.completionHandler = ^(NSURL *URL) {
    SHOW_ALERT_FOR(@"completed", @"export succeeded", @"ok", wSelf);
};
_exporter.failureHandler = ^(NSError *error) {
    SHOW_ERROR_ALERT_FOR(wSelf);
};
```

Swift
```swift
do {
    // You can use media resources generated during recording or existing resources(like from photo album or downloaded from remote server) to generate this MSVDraft object. 
    try exporter = MSVVideoExporter(draft: draft)
} catch {
    ShowAlert(error: error, controller: self)
}
guard exporter != nil else {
    return
}
exporter!.saveToPhotosAlbum = true
weak var wSelf = self
exporter!.progressHandler = {(progress) in
    print(progress)
}
exporter!.completionHandler = {(URL) in
    ShowAlert(message: URL.absoluteString, controller: self)
}
exporter!.failureHandler = {(error)
    ShowAlert(error: error, controller: self)
}

- Start exporting

Objective-C
```objective-c
[_exporter startExport];
```

Swift
```swift
exporter!.startExport();
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

## [API Reference](https://developer.movieous.cn/ios/shortvideo/api/)