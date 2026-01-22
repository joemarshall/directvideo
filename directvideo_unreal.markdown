---
title: DirectVideo Android
layout: page
headerimg: images/dv/894x488.png
engine: unreal
---

This is the Unreal version of the plugin. For Unity, see [DirectVideo Android for Unity](directvideo_unity.html).

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
<source src="images/dv/directvideo_comparison.mp4" type="video/mp4">
</video>

You can also download demo APKs to test on your own devices from [https://github.com/joemarshall/DirectVideoExample/releases/latest](https://github.com/joemarshall/DirectVideoExample/releases/latest).

## How to use

Install and enable the plugin. Enable it for your video sources, either by disabling the built in "Android Media Player" plugin, or manually by selecting "DirectVideo" as your player in your video source, as shown in the image below:

![Select DirectVideo in your video source under Player Overrides ](images/dv/select_player.png)

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

### Fallback Videos
If you want to support a range of devices, you can include lower resolution fallback videos for your main videos. By default these should be named like `[videoname]-fallback-1.mp4`, `[videoname]-fallback-2.mp4` etc. You can change the fallback video naming scheme in `Project settings -> Plugins -> DirectVideo`. When opening a video, DirectVideo Android will try and open the main video first, and if that fails, will try and open each fallback video in turn until one opens successfully.

### Demo APK log dump

If you find the demo apks don't work on your device, follow the instructions at (https://github.com/joemarshall/DirectVideoExample/) to send me a logfile dump and I'll try and fix it.

### Logging

If something doesn't work, and you want to look at logs (or send me some log output), there are a whole load of logs available - you can enable this in `project settings -> Plugins -> DirectVideo`.

Then it will print tons of stuff (if you set the level to **VeryVerbose**) or a bit of stuff if you set it to **Verbose** to the Android LogCat. You can read a logcat file from command prompt / terminal like so:

1. Kill your app on the device, and clear the logs, so that you don't log a million lines of data about whatever has happened before, using the following command: 

`adb logcat -c `

2. Now start logging to a file using this command:

`adb logcat > output.txt`

2. Now start your app. 

3. When your app has finished (or heaven forbid has crashed), output.txt should be full of lovely logs and you can press CTRL+C to stop writing logcat.

4. Look at the logcat file and search for lines with DirectVideo in them. You can send me the log file if you want me to look at it.

### Colour format

By default the player uses 10 bit colour (RGBA1010102) for the output frames, which gives higher quality colour reproduction than 8 bit (RGBA888). It is supported by modern Android GPUs. On some older devices, you may want to use RGBA888 colour format. You can do this in `project settings -> Plugins -> DirectVideo`, or by adding the following section to DefaultEngine.ini:
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
As is the same with the default Unreal media player, this relies on decoding support on the Android device. Typically on pretty much any recent device you are safe using H264 or H265 mp4 files, with one caveat; if you are developing for Meta Quest 3, there is a bug on the device which means it crashes decoding some 4k/8K H264 files; if you are targeting 4K or 8K, I recommend encoding as H265, which has both improved compression performance, and doesn't crash the Quest 3. Or you can use AV1 if you're happy only targeting recent devices like Quest 3 which support it. AV1 is really fast to encode with ffmpeg.

### How many videos can I play at once, and can I play loads of massive videos?

If you check out my [multiple players demo](https://github.com/joemarshall/DirectVideoMultiples) for Quest 3, I run 8 players at a 2K resolution (3840x2176x24fps), without frame drops or slowing down the Quest display frame rate. The main things limiting what you can play is limits on hardware decoders on your device. If you try and decode more, it will either fail, or fallback to very slow software video decoding, which both kills frame rate and on my phone at least has very poor colour rendering quality for some reason.

On Quest 3, I have quantified the main limit you hit, which is the total of the 'decoder blocks per frame' over all open players. The Snapdragon XR2 Gen2 chip on the Quest 3 can decode an absolute maximum of 278528 16x16 blocks over all the encoders (I don't know quite why it is that slightly odd number, but trust me, that is what the hardware reports and checks against). To work out how many blocks per frame your video takes:

1) For each video, divide the resolution by 16 on each axis and round up if not divisible by 16
2) Multiply the divided by 16 resolutions together for each video, this is the number of decoder blocks you are using for that video.
3) Sum all the decoder block counts up to get your total per frame block size. If this is greater than 278528, you won't be able to run this configuration on Quest.

Looking at the Android specs, I think there is in theory a limit on most devices of 16 videos decoding simulataneously in total, but I haven't tested that.

Bear in mind that the limits are on how many videos you have *open* in a player, no matter whether you are actively rendering it. Because of this, if you're using loads of different players, be careful to make sure you don't have any open when you don't need them to be prepared  or playing.

## How can I support different quality devices?

All devices have resolution limits - for Meta Quest, and many other Snapdragon 8 devices, the limits are really high so you are unlikely to hit them. However if you are targeting lower end devices, you may find that some devices can't decode full resolution video. To handle this, you can use fallback video files. DirectVideo Android will automatically fallback to a smaller resolution if the main file can't be opened.

### Why is this plugin needed?

On Android platform video playback in Unreal is **broken** when using Vulkan. This becomes increasingly obvious when you are building for standalone VR platforms such as Meta Quest 3, and you try to play a 360 degree video, which are typically filmed at 6k or greater resolution. If you try to do this while building in recent Unreal using Vulkan for graphics, you will find frame rate drops massively, if you are on VR, the headset is unusable, and everything heats up. With my plugin, videos play smoothly in Vulkan and the device no longer drops frames. 

### Why is Android video so bad in Unreal?
In Unreal, video is played using Android Java. Each frame is then copied to Unreal C++. In Vulkan, each video frame is first copied to a CPU buffer, which is then loaded back into a vulkan GPU texture. Unsurprisingly when you start playing with typical 360 video resolution such as 6016x2560@25fps, this means transferring approx 1.5 gigabytes of data per second across the CPU / GPU boundary. This also removes the ability to make use of hardware support for colour space conversions, which again kills performance.

### Is it possible to load alpha directly from videos?
Currently in Android alpha channels are not supported in video decoders [https://github.com/androidx/media/issues/1388](https://github.com/androidx/media/issues/1388). If you need video with alpha, you can workaround by either using a chroma key type mask in a shader, or by doing a dodgy hack where you render the video double size and then render the alpha on one side of the video, then recombine the two in your material shader [https://medium.com/go-electra/unlock-transparency-in-videos-on-android-5dc43776cc72](https://medium.com/go-electra/unlock-transparency-in-videos-on-android-5dc43776cc72).
