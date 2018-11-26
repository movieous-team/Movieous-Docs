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

## 快速开始
### 创建音视频配置对象

`MovieousLive` 分别使用 `MLAudioConfiguration` 和 `MLVideoConfiguration` 对象进行音频和视频的参数配置，这两个配置对象都支持使用 `-defaultConfiguration` 方法获取一个默认的配置对象，在获取到默认的配置对象之后我们可以根据业务需求对相关参数进行更改供后续使用。
```
// 创建默认配置对象
MLAudioConfiguration *audioConfiguration = [MLAudioConfiguration defaultConfiguration];
MLVideoConfiguration *videoConfiguration = [MLVideoConfiguration defaultConfiguration];
// 对需要配置的参数进行配置
audioConfiguration.audioBitRate = MLAudioBitRate128Kbps;
videoConfiguration.cameraResolution = AVCaptureSessionPresetHigh;
```
### 创建 `MLController`

使用在上一步创建好的 `MLAudioConfiguration` 和 `MLVideoConfiguration` 对象以及推流服务器的 URL 创建 `MLController` 对象，创建成功之后您可以使用该对象进行直播行为的控制，状态获取和参数调整。

```
MLController controller = [[MLController alloc] initWithURL:[NSURL URLWithString:_URL] audioConfiguration:_audioConfiguration videoConfiguration:_videoConfiguration];
```

- 注意：创建 `MLController` 对象需要保证您已经将推流所使用的域名备案到 [Movieous](https://movieous.cn/) 否则初始化方法将返回 `nil`

### 开始采集

开始进行音视频的采集，当开始采集接口被调用之后系统将会向用户申请摄像头和麦克风的权限（如果在相应配置中 `enable` 被配置为 `YES`）请确保您已经在项目的 `Info.plist` 文件中添加了 `Privacy - Camera Usage Description` 以及 `Privacy - Microphone Usage Description` 的描述，否则调用该接口将会导致程序崩溃。

```
[_movieousLiveController startCapturingWithCompletion:^(AVAuthorizationStatus cameraAuthorizationStatus, AVAuthorizationStatus microphoneAuthorizationStatus, NSError *error) {
    // 做出相应响应
}];
```

### 添加预览视图到当前视图的子视图

`MLController` 提供 `@property(nonatomic, strong, readonly) UIView *preview` 的属性用于预览摄像头采集的数据，您可以将它加入到您当前视图的子视图并调整它的大小以开始预览摄像头的输入。
```
[self.view addSubview:controller.preview];
controller.preview.frame = self.view.bounds;
```
- 注意：请在开始采集之后再获取预览视图，否则获取到的预览视图对象将为 `nil`

### 开始推流

下面可以开始进行推流了

```
[controller startBroadcastingWithCompletion:^(NSError *error) {
    // 做出相应响应
}];
```

### 设置 `MLController` 的 delegate

`MLController` 通过 delegate 的方式进行直播相关状态的通知，您可以通过实现 `MLControllerDelegate` 中定义的方法来获得包括直播状态等相关的通知。

```
{
    ...
    controller.delegate = self
    ...
}

// 获取直播相关状态改变的回调
- (void)controller:(MLController *)controller stateDidChange:(MLState)state error:(NSError *)error {
    // 针对相应直播状态改变做出相应响应
}
```

### 结束推流

当推流完成之后可以调用如下方法结束推流（结束之后依然可以使用该对象再次调用 `-startBroadcastingWithCompletion:` 方法再次开始推流）

```
[controller stopBroadcasting];
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
    pod 'MovieousLive'
end
```

<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)">然后在项目根目录执行 </span></span>`pod install`<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)"> </span></span>命令，执行成功后，SDK 就集成到项目中了。
<em>如果长时间没有拉取过pod 仓库，可能出现无法找到我们的repo的情况，此时建议先使用 </em><code><em>pod repo update</em></code><em> 更新pod仓库。</em>

### SDK 支持情况：

支持 iOS8 及其以上版本。
# 主要概念
# 使用指南