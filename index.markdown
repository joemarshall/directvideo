---
title: DirectVideo Android for Unreal
layout: page
---

## What is it?

DirectVideo Android enables high performance video playback within Unreal Engine on Android devices, including phones and VR Headsets such as Meta Quest 2 & 3. It works through direct use of the Vulkan graphics API, keeping all video handling in GPU, with frames being decompressed directly into Unreal textures. This means you can play 4K and 8K videos, including 360 degree content without frame drops on mid-level phones and VR headsets.

Buy now on Unreal Marketplace:
[https://www.unrealengine.com/marketplace/en-US/product/direct-video-android-beta](https://www.unrealengine.com/marketplace/en-US/product/direct-video-android-beta)

Sample project (you need the plugin obviously):
[https://github.com/joemarshall/directvideoexample](https://github.com/joemarshall/directvideoexample)

For larger developers wanting full source code access etc. contact me direct at [unreal@joemarshall.org.uk](mailto:unreal@joemarshall.org.uk)

## Demo and test APKs

The following video is captured on Meta Quest 3, playing a 7680x4096 x 30 FPS H265 MP4 file encoded at a bitrate of 50000 kb/s. The FPS displayed is the headset / game FPS. As you can see, when using the standard media player, the headset drops to an unusable 15 FPS, which on a VR headset is sickness inducing and tears as you move your head. With DirectVideo Android, the headset keeps a steady 72FPS, and head tracking remains smooth and lovely. 

<video width="100%"  controls autoplay muted loop>
<source src="images/directvideo_comparison.mp4" type="video/mp4">
</video>

You can also download demo APKs to test on your own devices from [https://github.com/joemarshall/directvideo/releases](https://github.com/joemarshall/directvideo/releases).

For the Meta Quest demo, press A and B to switch between DirectVideo Android and the built in media player. For the phone demo, touch 3 fingers to switch player, 2 fingers to pause and play.


## How to use

Install and enable the plugin. Enable it for your video sources, either by disabling the built in "Android Media Player" plugin, or manually by selecting "AndroidVulkanVideo" as your player in your video source, as shown in the image below:

![Select AndroidVulkanVideo in your video source under Player Overrides ](images/select_player.png)

## Frequently Answered Questions

### Does this support streaming?
Streaming is not yet supported; the current version of the plugin supports playback from assets or local storage only. For remote files, you will need to download to local storage first. 

### What formats should I use?
As is the same with the default Unreal media player, this relies on decoding support on the Android device. Typically on pretty much any recent device you are safe using H264 or H265 mp4 files, with one caveat; if you are developing for Meta Quest 3, there is a bug on the device which means it crashes decoding some 4k/8K H264 files; if you are targeting 4K or 8K, I recommend encoding as H265, which has both improved compression performance, and doesn't crash the Quest 3.

### Why is this plugin neaded?

On Android platform video playback in Unreal is **broken** when using Vulkan. This becomes increasingly obvious when you are building for standalone VR platforms such as Meta Quest 3, and you try to play a 360 degree video, which are typically filmed at 6k or greater resolution. If you try to do this while building in recent Unreal using Vulkan for graphics, you will find frame rate drops massively, if you are on VR, the headset is unusable, and everything heats up. With my plugin, videos play smoothly in Vulkan and the device no longer drops frames. 

### Why is Android video so bad in Unreal?
In Unreal, video is played using Android Java. Each frame is then copied to Unreal C++. In Vulkan, each video frame is first copied to a CPU buffer, which is then loaded back into a vulkan GPU texture. Unsurprisingly when you start playing with typical 360 video resolution such as 6016x2560@25fps, this means transferring approx 1.5 gigabytes of data per second across the CPU / GPU boundary. This also removes the ability to make use of hardware support for colour space conversions, which again kills performance.


