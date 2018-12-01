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
  
  ## Quick start
  ### Create an audio and video configuration Object
  `MovieousLive` respectively uses the `MLAudioConfiguration` and `MLVideoConfiguration` objects for audio and video parameter configuration. Both configuration objects support the use of the `-defaultConfiguration` method to get a default configuration object. After getting the default configuration object, we can change the relevant parameters for subsequent use according to business requirements.
  ```
  // Create default object
  MLAudioConfiguration *audioConfiguration = [MLAudioConfiguration defaultConfiguration];
  MLVideoConfiguration *videoConfiguration = [MLVideoConfiguration defaultConfiguration];
  // Configure the parameters that need to be configured
  audioConfiguration.audioBitRate = MLAudioBitRate128Kbps;
  videoConfiguration.cameraResolution = AVCaptureSessionPresetHigh;
  ```
  ### Create `MLController`
  Create the `MLController` object using the URLs of the push server and the `MLAudioConfiguration` and `MLVideoConfiguration` objects created in the previous step. After the creation is successful, you can use this object to control the live behavior, state acquisition and parameter adjustment.
  ```
  MLController controller = [[MLController alloc] initWithURL:[NSURL URLWithString:_URL] audioConfiguration:_audioConfiguration videoConfiguration:_videoConfiguration];
  ```
  - Note: Creating the `MLController` object requires that you have filed the domain name used by the push stream to [movie] (https://movieous.cn/) otherwise the initialization method will return `nil`
  ### Start collecting
  Start the acquisition of audio and video, After the acquisition interface is called, the system will apply for the camera and microphone permissions to users (if `enable` is configured as `YES` in the corresponding configuration), make sure that you have  already added  `Privacy - Camera Usage Description` and `Privacy - Microphone Usage Description` description in the project of  `Info.plist` otherwise calling this interface will cause the program to crash.
  ```
  [_movieousLiveController startCapturingWithCompletion:^(AVAuthorizationStatus cameraAuthorizationStatus, AVAuthorizationStatus microphoneAuthorizationStatus, NSError *error) {
  // To make respond accordingly
  }];
  ```
  ### Adding a preview-view to the subview of the current one
  `MLController` provides `@property(nonatomic, strong, readonly) UIView *preview` property is used to preview the data collected by the camera, you can add it to the subview of your current view and resize it to start previewing the camera enter.
  ```
  [self.view addSubview:controller.preview];
  controller.preview.frame = self.view.bounds;
  ```
  - Note: Please get the preview view after starting the acquisition, otherwise the previous preview-view object will be `nil`
  ### Start pushing stream 
  Please push stream as follows
  ```
  [controller startBroadcastingWithCompletion:^(NSError *error) {
  // To make respond accordingly
  }];
  ```
  ### Setting delegate of `MLController` 
  `MLController` informs the status of the live broadcast by means of the delegation, You can obtain notifications including live broadcast status and other related information by implementing the method defined in `MLControllerDelegate`.
  ```
  {
  ...
  controller.delegate = self
  ...
  }
  
  // Obtain callbacks related to live broadcast status changes.
  - (void)controller:(MLController *)controller stateDidChange:(MLState)state error:(NSError *)error {
  // Make a respond accordingly based on live broadcast status.
  }
  ```
  ### End pushing stream
  After  pushing stream  is completed, you can call the following method to end the pushing stream  (you can still use this object to call the `-startBroadcastingWithCompletion:` method again to start the pushing stream again after the end)
  ```
  [controller stopBroadcasting];
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
# Note：Make sure that there is only gems.ruby-china.com

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
