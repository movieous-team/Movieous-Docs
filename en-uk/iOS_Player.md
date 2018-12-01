# iOS SDK

  ##  Release Note
  
  ### 2018-10-10 SDK 1.0.0 Official version has been released
  - 1.0.0
  - Release ushortvideo-1.0.0.jar
  - Release libcore.so
  - Release libmuxer.so
  - Release libamix.so
  - Release libencoder.so
  - Release assets
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

# Quick start

## Create Player Object

You can create a `MovieousPlayerController` object using the media URL that will be played. The instance controls the player after the creation is successful, to obtain player status including  the player events and some relevant information.

```
MovieousPlayerController *player = [MovieousPlayerController playerControllerWithURL:URL]
```

## Adding a preview-view to the subview of the current one

`MovieousPlayerController`  provides  `@property (nonatomic, readonly) UIView *playerView`  property is used to show the media data, you can add it to the subview of your current view and resize it to start show the media video.

```
[self.view addSubview:player.playerView];
player.playerView.frame = self.view.bounds;
```

## Start Playback

Here you can start playing the media

```
[player play];
```

## Setting delegate of `MovieousPlayerController` 

`MovieousPlayerController`  informs the status of the player by means of the delegation, You can obtain notifications including live broadcast status and other related information by implementing the method defined in  `MovieousPlayerControllerDelegate`.

```
{
...
player.delegate = self
...
}

// Obtain callbacks related to Player status changes.
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playStateDidChange:(MPPlayState)playState {
// Make a respond accordingly based on Player status.
}

// Obtain callbacks related to Player pause connection changes.
- (void)movieousPlayerController:(MovieousPlayerController *)playerController playFinished:(MPFinishReason)finishReason {
// Make a respond accordingly based on Player pause connection.
}
```
## Pause Playback

Please pause playback based on following methods .

```
[player pause];
```

## Stop Playback

Please end playback based on following methods .

```
[player stop];
```

# How to install

## Cocoapods integration

### Install Cocoapods

If you have installed Cocoapods, skip this step and go straight to the next step.
If you have not heard of Cocoapods, we recommend reading[Dependency management of iOS programs with CocoaPods](https://blog.devtang.com/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/ "Dependency management of iOS programs with CocoaPods)" to understand why we use Cocoapods. About the Taobao original source has no longer maintained, it has been switched to [Ruby-China RubyGems mirror](https://gems.ruby-china.org/).
If you think that the above two articles are cumbersome, you can install them directly according to the brief steps we provide.
* Brief steps: Open the terminal that comes with the mac, and then enter the following commands in sequence.

```bash
# Note：Ruby-China recommend 2.6.x, actually, ruby which comes with mac can be use either.
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
gem sources -l
# Note：The commands aboved should output the following,  >>> represents output here.
>>> https://gems.ruby-china.com
# 注释：确保只有 gems.ruby-china.com

sudo gem install cocoapods
# Note：Since we don't need to use the official library, we don't execute the pod setup.
```

### Integrate with Podfile

Installation through [CocoaPods] (https://cocoapods.org/) can maximum reduce the installation process.
First, add the following pods to the Podfile file in the project root directory (we assume your project target name is `iOSDemo`):

```ruby
target 'iOSDemo' do
pod 'MovieousLive'
end
```

<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)">Then execute command in the project root directory </span></span>`pod install`<span data-type="color" style="color:rgb(51, 51, 51)"><span data-type="background" style="background-color:rgb(255, 255, 255)"> </span></span>command, After the execution is successful, the SDK has integrated into the project.
<em>If you have not fetched the pod warehouse for a long time, you may not be able to find out our repo. It is recommended to use </em><code><em>pod repo update</em></code><em> to update pod warehouse.</em>

### SDK support situation:

Support iOS 8 and later.

# Main concept

# Usage guideline
