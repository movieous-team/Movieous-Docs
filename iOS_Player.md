# iOS SDK

## [版本更新日志](https://movieous-team.coding.net/p/MovieousPlayer-Cocoa-Release/git/blob/master/CHANGELOG.md)

## 快速开始

Hi, 亲爱的开发者，欢迎使用 Movieous 直播推流 SDK。本教程将引导你在自己的 iOS 工程中集成直播推流功能。不过首先，我们假定你已经了解 Objective-C 或 Swift 的基础语法。

### 添加依赖
* 首先，你需要参考 [iOS 播放器 SDK 安装](https://movieous-team.coding.net/p/MovieousPlayer-Cocoa-Release/git/blob/master/README.md) 将 MovieousPlayer 引入你的工程

### 引入相关头文件
在您需要集成直播推流 SDK 的页面源文件中添加如下语句

Objective-C
```objective-c
#import <MovieousPlayer/MovieousPlayer.h>
```

Swift
```swift
import MovieousPlayer
```

### 创建播放器对象
您可以使用将要播放的媒体 URL 创建 `MovieousPlayerController` 对象，创建成功之后 实例对播放器进行控制，获取播放器的相关状态以及获取播放器事件的

Objective-C
```objective-c
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```

Swift
```swift
let player = MovieousPlayerController(url: URL)
```

### 添加预览视图到当前视图的子视图
`MovieousPlayerController` 提供 `@property (nonatomic, readonly) UIView *playerView` 的属性用于展示媒体的视频数据，您可以将它加入到您当前视图的子视图并调整它的大小以展示媒体影像。

Objective-C
```objective-c
[self.view addSubview:player.playerView];
player.playerView.frame = self.view.bounds;
```

Swift
```swift
self.view.addSubview(player.playerView)
player.playerView.frame = self.view.bounds
```

### 开始播放
下面可以开始播放媒体了

Objective-C
```objective-c
[player play];
```

Swift
```swift
player.play()
```

### 设置 `MovieousPlayerController` 的 delegate
`MovieousPlayerController` 通过 delegate 的方式进行播放器相关状态的通知，您可以通过实现 `MovieousPlayerControllerDelegate` 中定义的方法来获得包括直播状态等相关的通知。

Objective-C
```objective-c
{
    ...
    player.delegate = self
    ...
}

// 获取播放器状态改变的回调
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChange:(MPPlayState)playState {
    // 对播放器的状态变化进行相关响应
}

// 获取播放器断开连接的回调
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playFinished:(MPFinishReason)finishReason {
    // 对播放器断开连接做出相关的响应
}
```

Swift
```swift
{
    ...
    player.delegate = self
    ...
}

// 获取播放器状态改变的回调
func movieousPlayerController(_ playerController: MovieousPlayerController, playStateDidChangeWithPreviousState previousState: MPPlayerState, newState: MPPlayerState) {
    // 对播放器的状态变化进行相关响应
}

// 获取播放器断开连接的回调
func movieousPlayerController(_ playerController: MovieousPlayerController, playFinished finishReason: MPFinishReason) {
    // 对播放器断开连接做出相关的响应
}
```

### 暂停播放
您可以使用如下方法来暂停播放

Objective-C
```objective-c
[player pause];
```

Swift
```swift
player.pause()
```

### 结束播放
您可以使用如下方法来停止播放

Objective-C
```objective-c
[player stop];
```

Swift
```swift
player.stop()
```

## [API Reference](https://developer.movieous.cn/ios/player/api/)