# iOS SDK

## [版本更新日志](https://github.com/movieous-team/MovieousShortVideo-Cocoa-Release/blob/master/CHANGELOG.md)

## 快速开始
Hi, 亲爱的开发者，欢迎使用 Movieous 短视频 SDK。本教程将引导你在自己的 iOS 工程中集成短视频录制，编辑及导出功能。不过首先，我们假定你已经了解 Objective-C 或 Swift 的基础语法。

### 添加依赖
* 首先，你需要参考 [iOS 短视频 SDK 安装](https://github.com/movieous-team/MovieousShortVideo-Cocoa-Release/blob/master/README.md#%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85) 将 MovieousShortVideo 引入你的工程

### 引入相关头文件
在您需要集成短视频 SDK 的页面源文件中添加如下语句

Objective-C
```objective-c
#import <MovieousShortVideo/MovieousShortVideo.h>
```

Swift
```swift
import MovieousShortVideo
```

下面，你可以根据自己的需求，选择性地集成视频录制、视频编辑或视频导出模块

### 视频录制
- 添加麦克风和摄像头使用权限
打开项目配置页面，Info 选项添加 `Privacy - Camera Usage Description` 和 `Privacy - Microphone Usage Description` 描述
![image](./images/authorization.png)

- 生成音频和视频的配置对象并根据需求进行相关参数的更改

Objective-C
```objective-c
MSVRecorderAudioConfiguration *audioConfiguration = [MSVRecorderAudioConfiguration defaultConfiguration];
MSVRecorderVideoConfiguration *videoConfiguration = [MSVRecorderVideoConfiguration defaultConfiguration];
// 根据需求更改需要更改的参数，不更改则使用默认参数
audioConfiguration.mute = NO;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPreset1280x720;
```

Swift
```swift
let audioConfiguration = MSVRecorderAudioConfiguration.default()
let videoConfiguration = MSVRecorderVideoConfiguration.default()
// 根据需求更改需要更改的参数，不更改则使用默认参数
audioConfiguration.mute = false
videoConfiguration.preferredSessionPreset = .hd1280x720
```

- 生成录制核心控制对象

Objective-C
```objective-c
_recorder = [[MSVRecorder alloc] initWithAudioConfiguration:audioConfiguration videoConfiguration:videoConfiguration error:&error];
if (error) {
    // 进行错误提示
    SHOW_ERROR_ALERT;
    return;
}
// 设置录制事件回调
_recorder.delegate = self;
// 将预览视图插入视图栈中，以便预览摄像头采集结果
_recorder.previewView.frame = self.view.frame;
[self.view insertSubview:_recorder.previewView atIndex:0];
```

Swift
```swift
do {
    recorder = try MSVRecorder(audioConfiguration: audioConfiguration, videoConfiguration: videoConfiguration)
} catch {
    // 进行错误提示
    ShowAlert(error: error, controller: self)
}
guard recorder != nil else {
    return
}
// 设置录制事件回调
recorder!.delegate = self
recorder!.previewView.frame = self.view.frame
// 将预览视图插入视图栈中，以便预览摄像头采集结果
self.view.insertSubview(recorder!.previewView, at: 0)
```

- 开始音视频采集

Objective-C
```objective-c
[_recorder startCapturingWithCompletion:^(BOOL audioGranted, NSError *audioError, BOOL videoGranted, NSError *videoError) {
    if (videoError) {
        // 进行错误提示
        SHOW_ALERT(@"error", videoError.localizedDescription, @"ok");
        return;
    }
    if (!videoGranted) {
        // 进行错误提示
        SHOW_ALERT(@"warning", @"video not authorized", @"ok");
        return;
    }
    if (audioError) {
        // 进行错误提示
        SHOW_ALERT(@"error", audioError.localizedDescription, @"ok");
        return;
    }
    if (!audioGranted) {
        // 进行错误提示
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
        ShowAlert(error: videoError, controller: strongSelf)
        return
    }
    if (!videoGranted) {
        // 进行错误提示
        ShowAlert(@"video not authorized", self);
        return;
    }
    if let audioError = audioError {
        ShowAlert(error: audioError, controller: strongSelf)
        return
    }
        if (!audioGranted) {
        // 进行错误提示
        ShowAlert(@"audio not authorized", self);
        return;
    }
}
```

- 开始录制片段

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

- 完成当前片段录制

Objective-C
```objective-c
[_recorder finishRecordingWithCompletionHandler:^(MSVMainTrackClip *clip, NSError *error) {
    // 从生成的 clip 对象中可以获取到当前录制片段生成的 mp4 文件及相关描述信息
    if (error) {
        SHOW_ERROR_ALERT;
    }
}];
```

Swift
```swift
recorder!.finishRecording { (clip, error) in
    // 从生成的 clip 对象中可以获取到当前录制片段生成的 mp4 文件及相关描述信息
    if (error != nil) {
        ShowAlert(error: error, controller: self)
    }
}
```

完成当前片段录制之后还可以回到`开始录制片段`的步骤继续进行下一个片段的录制，所有录制好的片段将会顺序拼接为一个视频草稿保存在 `recorder.draft` 对象中，可传入编辑器进行编辑或传入导出器直接导出为一个合成后的文件

### 视频编辑
- 创建编辑器核心控制类

Objective-C
```objective-c
// 此处的 draft 可以使用在录制阶段生成的也可以使用已有音视频资源（例如系统相册或从远端下载等）创建
_editor = [MSVEditor editorWithDraft:draft error:&error];
if (error) {
    SHOW_ERROR_ALERT;
    return;
}
_editor.delegate = self;
_editor.loop = YES;
// 将预览视图插入视图栈中，以便预览编辑结果
[self.view insertSubview:_recorder.previewView atIndex:0];
```

Swift
```swift
do {
    // 此处的 draft 可以使用在录制阶段生成的也可以使用已有音视频资源（例如系统相册或从远端下载等）创建
    try editor = MSVEditor(draft: draft)
} catch {
    ShowAlert(error: error, controller: self)
}
guard editor != nil else {
    return
}
editor!.delegate = self
editor!.loop = true
// 将预览视图插入视图栈中，以便预览编辑结果
self.view.insertSubview(editor?.previewView, at: 0)
```

- 预览草稿

Objective-C
```objective-c
[_editor play];
```

Swift
```swift
editor!.play()
```

- 进行视频编辑
`MovieousShortVideo` 使用 `MSVDraft` 对象存储视频的编辑信息，你可以根据需求对 `_editor.draft` 的参数进行任意调整，调整之后预览视图会实时刷新为调整参数之后的内容，下面演示一下对视频进行剪辑操作

Objective-C
```objective-c
_editor.draft.timeRange = (MovieousTimeRange){
    // 需要剪辑的部分的开始时间
    startTime,
    // 需要剪辑的部分的时长
    duration,
};
```

Swift
```swift
editor!.draft.timeRange = MovieousTimeRange(startTime: startTime, duration: duration)
```

### 视频导出
- 创建导出核心控制对象

Objective-C
```objective-c
NSError *error;
// 创建导出控制对象
_exporter = [[MSVExporter alloc] initWithDraft:_draft error:&error];
if (error) {
    SHOW_ERROR_ALERT;
    return;
}
// 将导出结果保存到相册中
_exporter.saveToPhotosAlbum = YES;
__weak typeof(self) wSelf = self;
// 进度回调
_exporter.progressHandler = ^(float progress) {
    NSLog(@"progress: %f", progress);
};
// 完成回调
_exporter.completionHandler = ^(NSURL *URL) {
    SHOW_ALERT_FOR(@"完成", @"成功导出", @"知道啦", wSelf);
};
// 失败回调
_exporter.failureHandler = ^(NSError *error) {
    SHOW_ERROR_ALERT_FOR(wSelf);
};
```

Swift
```swift
do {
    // 此处的 draft 可以使用在录制阶段生成的也可以使用已有音视频资源（例如系统相册或从远端下载等）创建
    try exporter = MSVVideoExporter(draft: draft)
} catch {
    ShowAlert(error: error, controller: self)
}
guard exporter != nil else {
    return
}
// 将导出结果保存到相册中
exporter!.saveToPhotosAlbum = true
weak var wSelf = self
// 进度回调
exporter!.progressHandler = {(progress) in
    print(progress)
}
// 完成回调
exporter!.completionHandler = {(URL) in
    ShowAlert(message: URL.absoluteString, controller: self)
}
// 失败回调
exporter!.failureHandler = {(error)
    ShowAlert(error: error, controller: self)
}
```

- 开始导出

Objective-C
```objective-c
[_exporter startExport];
```

Swift
```swift
exporter!.startExport();
```

## 主要概念
### 草稿(MSVDraft)

草稿是 MovieousShortVideo 的核心数据结构，它代表一个正在制作过程当中的视频项目，它保存了所有的音视频数据源，剪辑，音量调整，特效等等构成最终视频的所有元素，录制阶段的输出、编辑阶段的输入及输出、导出阶段的输入均为草稿对象，开发者所有的视频编辑操作都通过草稿对象来完成。

### 主轨片段(MSVMainTrackClip)

主轨片段是构成草稿的基本要素，草稿的主音视频由主轨片段的音视频顺序拼接产生，草稿的时长为所有主轨片段的时长总和。

### 混合轨道片段(MSVMixTrackClip)

混合轨道片段是在主轨音频的基础上增加的混合音视频片段，它按照指定的音量和主轨及其他有重合部分的音频进行混合形成目的视频的音频，按照指定的位置和填充模式将片段视频合成到目的视频中。

### 特效

特效是在草稿的主轨的基础之上叠加的包括滤镜，反复，变速等在内多种效果。

### 录制器(MSVRecorder)

录制器主要提供视频分段录制功能，产生多个视频片段构成主轨，添加的背景音乐作为草稿的音效片段。

### 编辑器(MSVEditor)

编辑器提供对草稿的实时预览功能，能够实时预览草稿编辑的效果。

### 导出器(MSVVideoExporter)

导出器接受草稿对象作为输入，导出为各种格式的目标文件

## [API Reference](https://developer.movieous.cn/ios/shortvideo/api/)