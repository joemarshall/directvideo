---
title: DirectVideo Android for Unreal
layout: page
---

## What is it?

DirectVideo Android enables high performance video playback within Unreal Engine on Android devices, including phones and VR Headsets such as Meta Quest 2 & 3. It works through direct use of the Vulkan graphics API, keeping all video handling in GPU, with frames being decompressed directly into Unreal textures. This means you can play 4K and 8K videos, including 360 degree content without frame drops on mid-level phones and VR headsets.

Buy now on Fab Store:
[https://www.fab.com/listings/3259a389-1214-4312-a6aa-14fc8012ce7b](https://www.fab.com/listings/3259a389-1214-4312-a6aa-14fc8012ce7b)

Sample project (you need the plugin obviously):
[https://github.com/joemarshall/directvideoexample](https://github.com/joemarshall/directvideoexample)

For larger developers wanting full source code access etc. contact me direct at [unreal@joemarshall.org.uk](mailto:unreal@joemarshall.org.uk)

## Demo and test APKs

The following video is captured on Meta Quest 3, playing a 7680x4096 x 30 FPS H265 MP4 file encoded at a bitrate of 50000 kb/s. The FPS displayed is the headset / game FPS. As you can see, when using the standard media player, the headset drops to an unusable 15 FPS, which on a VR headset is sickness inducing and tears as you move your head. With DirectVideo Android, the headset keeps a steady 72FPS, and head tracking remains smooth and lovely. 

<video width="100%"  controls autoplay muted loop>
<source src="images/directvideo_comparison.mp4" type="video/mp4">
</video>

You can also download demo APKs to test on your own devices from [https://github.com/joemarshall/DirectVideoExample/releases/latest](https://github.com/joemarshall/DirectVideoExample/releases/latest).

## How to use

Install and enable the plugin. Enable it for your video sources, either by disabling the built in "Android Media Player" plugin, or manually by selecting "DirectVideo" as your player in your video source, as shown in the image below:

![Select DirectVideo in your video source under Player Overrides ](images/select_player.png)

## DirectVideoMeshRenderer (experimental)

If you want the ultimate in performance, instead of rendering to a material as normal
set a static mesh to render to a plain material (or whatever you want it to look
like when you have no video showing), and add the Direct Video Mesh Renderer component
to the StaticMesh Actor, pointed at your video. This will render the video directly to that mesh
on the main framebuffer. On my Quest demo app this uses less than 50% of the number of fragment
shader operations compared to rendering to texture first. This works in stereo multiview environments as well as standard Android, and supports MSAA antialiasing.

There are some limitations however - 

1) Only static meshes are supported.
2) Videos are rendered after the main base pass, without lighting, and
   any clever effects like reflections is unlikely to work with them.

## Advanced Player Options

### Demo APK log dump

If you find the demo apks don't work on your device, follow the instructions at (https://github.com/joemarshall/DirectVideoExample/) to send me a logfile dump and I'll try and fix it.

### Logging

If something doesn't work, and you want to look at logs (or send me some log output), there are two levels of logs available - Verbose and VeryVerbose. To enable these, add a section to DefaultEngine.ini (or add a value into Core.Log if you already set logging levels for anything else), like below.

```
[Core.Log]
LogDirectVideo=VeryVerbose
```

Then it will print tons of stuff (or a bit of stuff if you set it to **Verbose**) to the Android LogCat. You can read a logcat file from command prompt / terminal like so:

1. Clear the logs, so that you don't log a million lines of data about whatever has happened before: 
`adb logcat -c `

2. Now start your app. Once it is running, save the logcat output to a file:
`adb logcat > output.txt`
3. When your app has finished, output.txt should be full of lovely logs and you can press CTRL+C to stop writing logcat.

### Colour format

By default the player uses 10 bit colour (RGBA1010102) for the output frames, which gives higher quality colour reproduction than 8 bit (RGBA888). It is supported by modern Android GPUs. On some older devices, or if you want to replay video that has alpha you may want to use RGBA888 colour format. You can do this by adding a DirectVideo section to DefaultEngine.ini with the colour format set, like so:
```
[DirectVideo]
OutputFormat=CharRGBA
```
It also supports a few other colour formats, although on current devices performance is quite poor for most of them.

Possible values for OutputFormat are:

name | description
-----|-----
CharBGR10A2 | 10 bit RGB + 2 bit alpha (recommended)
CharBGRA | 8 bit RGBA (works, may have better performance, but lower colour quality)
CharRGBA | 8 bit RGBA (same as CharBGRA)
RGBA16 | RGBA 16 bit integer (64 bits/pixel - SLOW on any devices I have)
FloatRGB | RGB float textures (may be too slow on current devices)
FloatRGBA | RGBA float textures with alpha (slow)



## Frequently Answered Questions

### Does this support streaming?
Streaming is not yet supported; the current version of the plugin supports playback from assets or local storage only. For remote files, you will need to download to local storage first. 

### What formats should I use?
As is the same with the default Unreal media player, this relies on decoding support on the Android device. Typically on pretty much any recent device you are safe using H264 or H265 mp4 files, with one caveat; if you are developing for Meta Quest 3, there is a bug on the device which means it crashes decoding some 4k/8K H264 files; if you are targeting 4K or 8K, I recommend encoding as H265, which has both improved compression performance, and doesn't crash the Quest 3.

### Why is this plugin neaded?

On Android platform video playback in Unreal is **broken** when using Vulkan. This becomes increasingly obvious when you are building for standalone VR platforms such as Meta Quest 3, and you try to play a 360 degree video, which are typically filmed at 6k or greater resolution. If you try to do this while building in recent Unreal using Vulkan for graphics, you will find frame rate drops massively, if you are on VR, the headset is unusable, and everything heats up. With my plugin, videos play smoothly in Vulkan and the device no longer drops frames. 

### Why is Android video so bad in Unreal?
In Unreal, video is played using Android Java. Each frame is then copied to Unreal C++. In Vulkan, each video frame is first copied to a CPU buffer, which is then loaded back into a vulkan GPU texture. Unsurprisingly when you start playing with typical 360 video resolution such as 6016x2560@25fps, this means transferring approx 1.5 gigabytes of data per second across the CPU / GPU boundary. This also removes the ability to make use of hardware support for colour space conversions, which again kills performance.


