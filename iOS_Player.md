# iOS SDK

##  Release Note

### v1.0.1(2018-12-8)
- 修复自动重连无法正常播放的问题

### v1.0.0(2018-11-28)
- 支持主流直播、点播播放
- 支持常见播放协议

## 如何安装

### Cocoapods 集成

#### 安装 Cocoapods

如果您已安装 Cocoapods，则请直接跳过该步骤，直接进入下一步骤。
如果你未接触过 Cocoapods ，我们推荐您阅读 [唐巧的博客-用CocoaPods做iOS程序的依赖管理](https://blog.devtang.com/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/ "用CocoaPods做iOS程序的依赖管理") ，了解我们为何使用 Cocoapods 。另外文章中提及的淘宝源已经不再维护，需要使用 [Ruby-China RubyGems 镜像](https://gems.ruby-china.com/)替换。

如果觉得上面两个文章比较繁琐，可以直接根据我们提供的简要步骤，进行安装。
* 简要步骤：打开mac自带的 终端(terminal)，然后输入依次执行下述命令。

```bash
# 注释：Ruby-China 推荐2.6.x，实际 mac 自带的 ruby 也能用了
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
gem sources -l
# 注释：上面的命令，应该会输出以下内容，>>> 代表此处为输出
>>> https://gems.ruby-china.com
# 注释：确保只有 gems.ruby-china.com

sudo gem install cocoapods
# 注释：由于我们不需要使用官方库，所以可以不执行 pod setup。
```

#### 使用Podfile集成

通过 [CocoaPods](https://cocoapods.org/) 安装可以最大化地简化安装过程。
首先，在项目根目录下的 Podfile 文件中添加以下 pods（我们假设您的项目 target 名称为 `iOSDemo`）：

```ruby
target 'iOSDemo' do
    pod 'MovieousPlayer'
end
```

<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)">然后在项目根目录执行 </span></span>`pod install`<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)"> </span></span>命令，执行成功后，SDK 就集成到项目中了。
<em>如果长时间没有拉取过pod 仓库，可能出现无法找到我们的repo的情况，此时建议先使用 </em><code><em>pod repo update</em></code><em> 更新pod仓库。</em>

#### SDK 支持情况：

支持 iOS8 及其以上版本。

## 快速开始

### 创建播放器对象
您可以使用将要播放的媒体 URL 创建 `MovieousPlayerController` 对象，创建成功之后 实例对播放器进行控制，获取播放器的相关状态以及获取播放器事件的
```objectivec
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```

### 添加预览视图到当前视图的子视图
`MovieousPlayerController` 提供 `@property (nonatomic, readonly) UIView *playerView` 的属性用于展示媒体的视频数据，您可以将它加入到您当前视图的子视图并调整它的大小以展示媒体影像。
```objectivec
[self.view addSubview:player.playerView];
player.playerView.frame = self.view.bounds;
```

### 开始播放
下面可以开始播放媒体了
```objectivec
[player play];
```

### 设置 `MovieousPlayerController` 的 delegate
`MovieousPlayerController` 通过 delegate 的方式进行播放器相关状态的通知，您可以通过实现 `MovieousPlayerControllerDelegate` 中定义的方法来获得包括直播状态等相关的通知。
```objectivec
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
### 暂停播放
您可以使用如下方法来暂停播放
```objectivec
[player pause];
```
### 结束播放
您可以使用如下方法来停止播放
```objectivec
[player stop];
```

## 主要概念
### 播放器可选项（MovieousPlayerOptions）
主要用于进行播放器的一些可选项配置

### 播放器控制器（MovieousPlayerController）
对播放器的所有操作

## 使用指南
### 播放器可选项（MovieousPlayerOptions）
```objectivec
/**
 * @brief 解码器类型
 */
typedef NS_ENUM(NSInteger, MovieousPlayerDecoderType) {
    /**
     * @brief 使用软件解码器
     */
    MovieousPlayerDecoderTypeSoftware,
    /**
     * @brief 使用硬件解码器
     */
    MovieousPlayerDecoderTypeHardware,
};

@interface MovieousPlayerOptions : NSObject

/**
 * @brief 标识是否是直播流播放，通过此标记 MovieousPlayer 会尽最大努力保证低延迟，但是需要注意的是打开此选项将会造成更多的卡顿。
 * 默认关闭
 */
@property (nonatomic, assign) BOOL liveStreaming;

/**
 * @brief 是否开启直播流的累积延迟优化机制，只有当前流式直播流时才应当能开启累积延迟优化。
 * 默认关闭
 */
@property (nonatomic, assign) BOOL delayOptimization;

/**
 * @brief 是否允许和其他音频发生混合而不被打断。
 * 默认开启
 */
@property (nonatomic, assign) BOOL allowMixAudioWithOthers;

/**
 * @brief 使用的解码器类型
 * 默认为 MovieousPlayerDecoderTypeHardware
 */
@property (nonatomic, assign) MovieousPlayerDecoderType decoderType;

/**
 * @brief 播放器网络请求的超时时间（以微秒为单位）。
 * 默认为 10 * 1000 * 1000 (10秒)
 */
@property (nonatomic, assign) NSUInteger timeoutInterval;

/**
 * @brief 创建一个默认的 MovieousPlayerOptions 对象
 * @return 返回默认的 MovieousPlayerOptions 对象
 */
+ (instancetype)defaultOptions;

@end
```

### 播放器控制器（MovieousPlayerController）
```objectivec
/**
 * @brief 播放器内容的填充模式
 */
typedef NS_ENUM(NSInteger, MPScalingMode) {
    /**
     * @brief 保持比例填充到视图内，比例不一致会出现黑边
     */
    MPScalingModeAspectFit,
    /**
     * @brief 保持比例填充到视图内，比例不一致会发生裁剪
     */
    MPScalingModeAspectFill,
    /**
     * @brief 缩放后填充到视图内，比例不一致会出现变形
     */
    MPScalingModeFill
};

/**
 * @brief 播放器的状态
 */
typedef NS_ENUM(NSInteger, MPPlayerState) {
    /**
     * @brief 新初始化的播放器的状态
     */
    MPPlayerStateIdle,
    /**
     * @brief 播放器正在准备
     */
    MPPlayerStatePreparing,
    /**
     * @brief 播放器已经准备好播放
     */
    MPPlayerStatePrepared,
    /**
     * @brief 播放器正在播放
     */
    MPPlayerStatePlaying,
    /**
     * @brief 播放器因为网络问题正在缓冲
     */
    MPPlayerStateBuffering,
    /**
     * @brief 播放器正在查找位置进行播放
     */
    MPPlayerStateSeeking,
    /**
     * @brief 播放器已暂停
     */
    MPPlayerStatePaused,
    /**
     * @brief 媒体源已播放完成
     */
    MPPlayerStateCompleted,
    /**
     * @brief 播放器被停止
     */
    MPPlayerStateStopped,
    /**
     * @brief 播放器发生了一些错误
     */
    MPPlayerStateError,
};

/**
 * @brief 播放器停止的原因
 */
typedef NS_ENUM(NSInteger, MPFinishReason) {
    /**
     * @brief 由于播放停止播放停止
     */
    MPFinishReasonPlaybackEnded,
    /**
     * @brief 由于发生错误播放器停止
     */
    MPFinishReasonPlaybackError,
    /**
     * @brief 由于用户主动操作播放器停止
     */
    MPFinishReasonUserExited
};

/**
 * @brief 日志等级
 */
typedef NS_ENUM(NSInteger, MPLogLevel) {
    /**
     * @brief 未知日志等级
     */
    MPLogLevelUnknown,
    /**
     * @brief 打印 Default 及以上级别的日志
     */
    MPLogLevelDefault,
    /**
     * @brief 打印 Verbose 及以上级别的日志
     */
    MPLogLevelVerbose,
    /**
     * @brief 打印 Debug 及以上级别的日志
     */
    MPLogLevelDebug,
    /**
     * @brief 打印 Info 及以上级别的日志
     */
    MPLogLevelInfo,
    /**
     * @brief 打印 Warn 及以上级别的日志
     */
    MPLogLevelWarn,
    /**
     * @brief 打印 Error 及以上级别的日志
     */
    MPLogLevelError,
    /**
     * @brief 打印 Fatal 及以上级别的日志
     */
    MPLogLevelFatal,
    /**
     * @brief 打印 Silent 及以上级别的日志
     */
    MPLogLevelSilent
};

/**
 * @brief 播放器被其他音频打断时使用哪种策略
 */
typedef NS_ENUM(NSInteger, MPInterruptionOperation) {
    /**
     * @brief 暂停
     */
    MPInterruptionOperationPause,
    /**
    * @brief 停止
    MPInterruptionOperationStop,
};

@class MovieousPlayerController;

@protocol MovieousPlayerControllerDelegate<NSObject>

@optional

/**
 * @brief 通知代理对象当前播放器已经成功渲染首帧视频
 * @param playerController 调用代理方法的播放器实例
 */
- (void)movieousPlayerControllerFirstVideoFrameRendered:(MovieousPlayerController *)playerController;

/**
 * @brief 通知代理对象当前播放器已经成功渲染首帧音频
 * @param playerController 调用代理方法的播放器实例
 */
- (void)movieousPlayerControllerFirstAudioFrameRendered:(MovieousPlayerController *)playerController;

/**
 * @brief 通知代理对象当前播放器状态已变更
 * @param playerController 调用代理方法的播放器实例
 * @param playState 新的播放器状态
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChange:(MPPlayerState)playState;

/**
 * @brief 通知代理对象当前播放器状态已变更
 * @param playerController 调用代理方法的播放器实例
 * @param previousState 老的播放器状态
 * @param newState 新的播放器状态
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChangeWithPreviousState:(MPPlayerState)previousState newState:(MPPlayerState)newState;

/**
 * @brief Notify delegate that MovieousPlayerController's has begun to retry reconnecting
 * @param playerController 调用代理方法的播放器实例
 * @param currentCount Current retried count
 */
- (BOOL)movieousPlayerController:(MovieousPlayerController *)playerController playerBeginsToRetry:(NSUInteger)currentCount;

/**
 * @brief Notify delegate that MovieousPlayerController has finished playing
 * @param playerController 调用代理方法的播放器实例
 * @param finishReason The reason why MovieousPlayerController has finished
 */
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playFinished:(MPFinishReason)finishReason;

@end

@interface MovieousPlayerController : NSObject

/**
 * @brief 全局的日志级别
 */
@property (nonatomic, assign, class) MPLogLevel logLevel;

/**
 * @brief 初始化播放器使用的 MovieousPlayerOptions 对象
 */
@property (nonatomic, strong, readonly) MovieousPlayerOptions *options;

/**
 * @brief 展示视频内容的视图
 */
@property (nonatomic, readonly) UIView *playerView;

/**
 * @brief 播放的视频总时长
 */
@property (nonatomic, readonly) NSTimeInterval totalDuration;

/**
 * @brief 播放器状态
 */
@property (nonatomic, readonly) MPPlayerState playState;

/**
 * @brief 视频的原始大小
 */
@property (nonatomic, readonly) CGSize naturalSize;

/**
 * @brief 视频的快照
 */
@property (nonatomic, readonly) UIImage *snapshot;

/**
 * @brief 当前播放到的时间进度
 */
@property (nonatomic, assign) NSTimeInterval currentTime;

/**
 * @brief 当播放器内容和视图的比例不一致时如何填充内容
 * 默认为 MPScalingModeNone
 */
@property (nonatomic, assign) MPScalingMode scalingMode;

/**
 * @brief 播放器的音量
 * 默认为 1
 */
@property (nonatomic, assign) float volume;

/**
 * @brief 当应用进入后台时是否停止播放
 * 默认为 YES
 */
@property (nonatomic, assign) BOOL pauseInBackground DEPRECATED_ATTRIBUTE;

/**
 * @brief 当应用进入后台时是否会被打断，具体打断方式由 interruptionOperation 属性进行配置
 * 默认为 YES
 */
@property (nonatomic, assign) BOOL interruptInBackground;

/**
 * @brief 当应用进入后台后用什么操作打断当前播放
 * 默认为 MPInterruptionOperationPause
 */
@property (nonatomic, assign) MPInterruptionOperation interruptionOperation;

/**
 * @brief 如果发生错误是否重试
 * 默认为 YES
 */
@property (nonatomic, assign) BOOL retryIfError;

/**
 * @brief 播放器的代理对象
 */
@property (nonatomic, weak) id<MovieousPlayerControllerDelegate> delegate;

/**
 * @brief 播放结束后是否循环
 */
@property (nonatomic, assign) BOOL loop;

/**
 * @brief 是否开启 VR 播放模式
 */
@property (nonatomic, assign) BOOL VRMode;

/**
 * @brief 是否开启陀螺仪，只有在 VR 播放模式开启时才有效
 */
@property (nonatomic,assign) BOOL gyroscopeEnabled;

/**
 * @brief 是否开启双屏 VR 模式，只有在 VR 播放模式开启时才有效
 */
@property (nonatomic, assign) BOOL dualScreen;

/**
 * @brief 是否开启通过触摸来移动视角模式，只有在 VR 播放模式开启时才有效
 */
@property (nonatomic, assign) BOOL touchToMoveEnabled;

/**
 * @brief 使用媒体地址来创建一个 MovieousPlayerController 实例
 * @param URL 用于播放的媒体地址
 * @return 初始化完成的播放器对象
 */
+ (instancetype)playerControllerWithURL:(NSURL *)URL;

/**
 * @brief 使用媒体地址和配置选项来创建一个 MovieousPlayerController 实例
 * @param URL 用于播放的媒体地址
 * @param options 初始化播放器的选项
 * @return 初始化完成的播放器对象
 */
+ (instancetype)playerControllerWithURL:(NSURL *)URL options:(MovieousPlayerOptions *)options;

/**
 * @brief 使用媒体地址来初始化一个 MovieousPlayerController 实例
 * @param URL 用于播放的媒体地址
 * @return 初始化完成的播放器对象
 */
- (instancetype)initWithURL:(NSURL *)URL;

/**
 * @brief 使用媒体地址和配置选项来初始化一个 MovieousPlayerController 实例
 * @param URL 用于播放的媒体地址
 * @param options 初始化播放器的选项
 * @return 初始化完成的播放器对象
 */
- (instancetype)initWithURL:(NSURL *)URL options:(MovieousPlayerOptions *)options;

/**
 * @brief 准备播放
 */
- (void)prepareToPlay;

/**
 * @brief 开始播放
 */
- (void)play;

/**
 * @brief 暂停播放
 */
- (void)pause;

/**
 * @brief 停止播放
 */
- (void)stop;

@end
```