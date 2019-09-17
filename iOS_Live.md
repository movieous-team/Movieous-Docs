# iOS SDK
## [版本更新日志](https://github.com/movieous-team/MovieousLive-Cocoa-Release/blob/master/CHANGELOG.md)

## 快速开始
Hi, 亲爱的开发者，欢迎使用 Movieous 直播推流 SDK。本教程将引导你在自己的 iOS 工程中集成直播推流功能。不过首先，我们假定你已经了解 Objective-C 或 Swift 的基础语法。

### 添加依赖
* 首先，你需要参考 [iOS 直播 SDK 安装](https://github.com/movieous-team/MovieousLive-Cocoa-Release/blob/master/README.md#%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85) 将 MovieousLive 引入你的工程

### 引入相关头文件
在您需要集成直播推流 SDK 的页面源文件中添加如下语句

Objective-C
```objective-c
#import <MovieousLive/MovieousLive.h>
```

Swift
```swift
import MovieousLive
```

### 创建音视频配置对象
`MovieousLive` 分别使用 `MLAudioConfiguration` 和 `MLVideoConfiguration` 对象进行音频和视频的参数配置，这两个配置对象都支持使用 `-defaultConfiguration` 方法获取一个默认的配置对象，在获取到默认的配置对象之后我们可以根据业务需求对相关参数进行更改供后续使用。

Objective-C
```objectivec
// 创建默认配置对象
MLAudioConfiguration *audioConfiguration = [MLAudioConfiguration defaultConfiguration];
MLVideoConfiguration *videoConfiguration = [MLVideoConfiguration defaultConfiguration];
// 对需要配置的参数进行配置
audioConfiguration.bitrate = 128 * 1024;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPresetHigh;
```

Swift
```swift
// 创建默认配置对象
let audioConfiguration = MLAudioConfiguration.default();
let videoConfiguration = MLVideoConfiguration.default();
// 对需要配置的参数进行配置
audioConfiguration.bitrate = 128 * 1024;
videoConfiguration.preferredSessionPreset = AVCaptureSessionPresetHigh;
```

### 创建 `MLStreamer`
使用在上一步创建好的 `MLAudioConfiguration` 和 `MLVideoConfiguration` 对象创建 `MLStreamer` 对象，创建成功之后您可以使用该对象进行直播行为的控制，状态获取和参数调整。

Objective-C
```objectivec
NSError *error;
MLStreamer *streamer = [MLStreamer streamerWithAudioConfiguration:audioConfiguration videoConfiguration:videoConfiguration error:&error];
if (error) {
    // 处理错误
}
```

Swift
```swift
do {
    try streamer = MLStreamer(audioConfiguration: audioConfiguration, videoConfiguration: videoConfiguration)
} catch {
    // 处理错误
}
```

### 开始采集
开始进行音视频的采集，当开始采集接口被调用之后系统将会向用户申请摄像头和麦克风的权限（如果在相应配置中 `enable` 被配置为 `YES`）请确保您已经在项目的 `Info.plist` 文件中添加了 `Privacy - Camera Usage Description` 以及 `Privacy - Microphone Usage Description` 的描述，否则调用该接口将会导致程序崩溃。

Objective-C
```objectivec
[streamer startCapturingWithCompletion:^(BOOL audioGranted, NSError *_Nullable audioError, BOOL videoGranted, NSError *_Nullable videoError) {
    // 做出相应响应
}];
```

Swift
```swift
streamer.startCapturing { (audioGranted: Bool, audioError: Error?, videoGranted: Bool, videoError: Error?) in
    // 做出相应响应
}
```

### 添加预览视图到当前视图的子视图
`MLStreamer` 提供 `@property(nonatomic, strong, readonly) UIView *previewView` 的属性用于预览摄像头采集的数据，您可以将它加入到您当前视图的子视图并调整它的大小以开始预览摄像头的输入。

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

### 开始推流
指定推流服务器的 URL 开始推流。

Objective-C
```objectivec
NSError *error;
[controller startPushingWithServerURL:serverURL error:&error];
if (error) {
    // 处理错误
}
```

Swift
```swift
do {
    try streamer?.startPushing(withServerURL: serverURL)
} catch {
    // 处理错误
}
```

- 注意：进行推流需要保证您已经将推流所使用的域名报备到 [Movieous](https://movieous.cn/) 否则无法进行推流

### 设置 `MLStreamer` 的 delegate
`MLStreamer` 通过 delegate 的方式进行直播相关状态的通知，您可以通过实现 `MLStreamerDelegate` 中定义的方法来获得包括直播状态等相关的通知。

Objective-C
```objectivec
{
    ...
    streamer.delegate = self
    ...
}

// 获取直播错误的回调
- (void)streamer:(MLStreamer *)streamer didFailedWithError:(NSError *)error {
    // 进行错误处理。
}
```

Swift
```swift
{
    ...
    streamer.delegate = self
    ...
}

// 获取直播错误的回调
func streamer(_ streamer: MLStreamer, didFailedWithError error: Error) {
    // 进行错误处理。
}
```

### 结束推流
当推流完成之后可以调用如下方法结束推流（结束之后依然可以使用该对象再次调用 `-startPushingWithServerURL:error:` 方法再次开始推流）

Objective-C
```objectivec
[streamer stopPushing];
```

Swift
```swift
streamer.stopPushing();
```

## [API Reference](https://developer.movieous.cn/ios/live/api/)