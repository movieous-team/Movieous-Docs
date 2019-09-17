# iOS SDK

## [CHANGELOG](https://movieous-team.coding.net/p/MovieousPlayer-Cocoa-Release/git/blob/master/CHANGELOG.en-us.md)

## Quick start
Dear developer, welcome to use the player SDK from Movieous . This tutorial will guide you how to acheive integration of video player functions in your iOS project. But first, we assume that you already know the underlying syntax of Objective-C or Swift.

### Add dependency
* First, the [iOS player SDK installation guide](https://movieous-team.coding.net/p/MovieousPlayer-Cocoa-Release/git/blob/master/README.en-us.md) for your reference to introduce MovieousPlayer to your project.

### Introduce related header files
Add the following statement to the page original source file where you need to integrate the player SDK

Objective-C
```objective-c
#import <MovieousPlayer/MovieousPlayer.h>
```

Swift
```swift
import MovieousPlayer
```

### Create Player Object

You can create a `MovieousPlayerController` object using the media URL that will be played. The instance controls the player after the creation is successful, to obtain player status including  the player events and some relevant information.

Objective-C
```objective-c
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```

Swift
```swift
let player = MovieousPlayerController(url: URL)
```

### Adding a preview-view to the subview of the current one

`MovieousPlayerController`  provides  `@property (nonatomic, readonly) UIView *playerView`  property is used to show the media data, you can add it to the subview of your current view and resize it to start show the media video.

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

### Start Playback

Here you can start playing the media

Objective-C
```objective-c
[player play];
```

Swift
```swift
player.play()
```

### Setting delegate of `MovieousPlayerController` 

`MovieousPlayerController`  informs the status of the player by means of the delegation, You can obtain notifications including live broadcast status and other related information by implementing the method defined in  `MovieousPlayerControllerDelegate`.

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

### Pause Playback

Please pause playback based on following methods .

Objective-C
```objective-c
[player pause];
```

Swift
```swift
player.pause()
```

### Stop Playback

Please end playback based on following methods .

Objective-C
```objective-c
[player stop];
```

Swift
```swift
player.stop()
```

## [API Reference](https://developer.movieous.cn/ios/player/api/)