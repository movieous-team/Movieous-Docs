# iOS SDK

##  Release Note

### 2018-10-10 SDK 1.0.0 正式版发布
- 1.0.0
  - 发布 ushortvideo-1.0.0.jar
  - 发布 libcore.so
  - 发布 libmuxer.so
  - 发布 libamix.so
  - 发布 libencoder.so
  - 发布 assets
  - 音/视频采集
  - 音/视频编码（H.264 + AAC）
  - 实时美颜、滤镜
  - 支持第三方美颜、滤镜
  - 自定义录制时长、码率、分辨率
  - 支持 1:1 录制
  - 断点录制
  - 回删录制片段
  - 抖音特效
  - 背景替换
  - 手势识别
  - 表情识别
  - 视频保存为 mp4 格式
  - 支持 arm64, i386, x86_64 体系架构

# 快速开始

## 创建播放器对象
您可以使用将要播放的媒体 URL 创建 `MovieousPlayerController` 对象，创建成功之后 实例对播放器进行控制，获取播放器的相关状态以及获取播放器事件的
```
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```
## 添加预览视图到当前视图的子视图
`MovieousPlayerController` 提供 `@property (nonatomic, readonly) UIView *playerView` 的属性用于展示媒体的视频数据，您可以将它加入到您当前视图的子视图并调整它的大小以展示媒体影像。
```
[self.view addSubview:player.playerView];
player.playerView.frame = self.view.bounds;
```
## 开始播放
下面可以开始播放媒体了
```
[player play];
```
## 设置 `MovieousPlayerController` 的 delegate
`MovieousPlayerController` 通过 delegate 的方式进行播放器相关状态的通知，您可以通过实现 `MovieousPlayerControllerDelegate` 中定义的方法来获得包括直播状态等相关的通知。
```
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
## 暂停播放
您可以使用如下方法来暂停播放
```
[player pause];
```
## 结束播放
您可以使用如下方法来停止播放
```
[player stop];
```
# 如何安装

## Cocoapods 集成

### 安装 Cocoapods

如果您已安装 Cocoapods，则请直接跳过该步骤，直接进入下一步骤。
如果你未接触过 Cocoapods ，我们推荐您阅读 [唐巧的博客-用CocoaPods做iOS程序的依赖管理](https://blog.devtang.com/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/ "用CocoaPods做iOS程序的依赖管理") ，了解我们为何使用 Cocoapods 。另外文章中提及的淘宝源已经不再维护，需要使用 [Ruby-China RubyGems 镜像](https://gems.ruby-china.org/)替换。

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

### 使用Podfile集成

通过 [CocoaPods](https://cocoapods.org/) 安装可以最大化地简化安装过程。
首先，在项目根目录下的 Podfile 文件中添加以下 pods（我们假设您的项目 target 名称为 `iOSDemo`）：

```ruby
target 'iOSDemo' do
    pod 'MovieousPlayer'
end
```

<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)">然后在项目根目录执行 </span></span>`pod install`<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)"> </span></span>命令，执行成功后，SDK 就集成到项目中了。
<em>如果长时间没有拉取过pod 仓库，可能出现无法找到我们的repo的情况，此时建议先使用 </em><code><em>pod repo update</em></code><em> 更新pod仓库。</em>

### SDK 支持情况：

支持 iOS8 及其以上版本。

# 主要概念
# 使用指南