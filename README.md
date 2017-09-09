## DLABridging.framework

Simple Objective-C++ wrapper for Blackmagic DeckLink API (C++ APIs).

- __Requirement__: MacOS X 10.11 or later.
- __Capture Device__: Blackmagic Intensity Shuttle, or other DeckLink devices.
- __Restriction__: Only 8 or 10 bit yuv, or 8 bit rgb are supported.
- __Dependency__: DeckLinkAPI.framework from Blackmagic_Desktop_Video_Macintosh_10.9.5 or later.

NOTE: This framework is under development.

#### Basic usage (capture)

###### 1. Find DLABDevice using DLABBrowser
    import Cocoa
    import DLABridging
    var device :DLABDevice? = nil

    let browser = DLABBrowser()
    _ = browser.start()
    let deviceList = browser.allDevices
    device = deviceList.first!
    _ = browser.stop()
###### 2. Start input stream
    do {
      try device.setInputScreenPreviewTo(parentView)

      var displayModeSupportFlag:DLABDisplayModeSupportFlag = .notSupported
      var vSetting:DLABVideoSetting? = nil
      try vSetting = device.createInputVideoSetting(of: .modeNTSC,
                                                    pixelFormat: .format8BitYUV,
                                                    inputFlag: [],
                                                    supportedAs: &displayModeSupportFlag)
      var aSetting:DLABAudioSetting? = nil
      try aSetting = device.createInputAudioSetting(of: .type16bitInteger,
                                                    channelCount: 2,
                                                    sampleRate: .rate48kHz)

      if let vSetting = vSetting, let aSetting = aSetting, displayModeSupportFlag != .notSupported {
        device.inputDelegate = self
        try device.enableVideoInput(with: vSetting)
        try device.enableAudioInput(with: aSetting)
        try device.startStreams()
      }
    } catch {
      Swift.print("ERROR!!")
    }
###### 3. Handle CMSampleBuffer (Video/Audio)
    public func processCapturedVideoSample(_ sampleBuffer: CMSampleBuffer) {
      Swift.print("video")
    }
    public func processCapturedAudioSample(_ sampleBuffer: CMSampleBuffer) {
      Swift.print("audio")
    }
    public func processCapturedVideoSample(_ sampleBuffer: CMSampleBuffer,
                                           timecodeSetting setting: DLABTimecodeSetting) {
      Swift.print("video/timecode")
    }
###### 4. Stop input stream
    do {
      try device.stopStreams()
      try device.disableVideoInput()
      device.inputDelegate = nil

      try device.setInputScreenPreviewTo(nil)
    } catch {
        Swift.print("ERROR!!")
    }
    device = nil

#### Development environment
- MacOS X 10.12.6 Sierra
- Xcode 8.3.3
- Objective-C++, Swift 3.1.0

#### License
    - The MIT License

Copyright © 2017年 MyCometG3. All rights reserved.