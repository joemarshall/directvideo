---
title: DirectVideo Android
layout: page
headerimg: images/dv/894x488.png
engine: Unity
---

This is the Unity version of the plugin. For Unreal, see [DirectVideo Android for Unreal](directvideo_unreal.html).


## What is it?

DirectVideo Android enables high performance video playback within Unity on Android devices, including phones and VR Headsets such as Meta Quest 2 & 3. It works through direct use of the Vulkan graphics API, keeping all video handling in GPU, with frames being decompressed directly into Unity textures. This means you can play 4K and 8K videos, including 360 degree content without frame drops on mid-level phones and VR headsets.

Coming soon on Unity Asset Store. If you need it sooner, contact me [by email](mailto:unity@joemarshall.org.uk).
<!-- Get it from Unity asset store: [Direct Video Android](https://assetstore.unity.com/packages/tools/video/direct-video-android-2384) -->


# About Direct Video Android

Use the Direct Video Android package to play video files on Android devices such as smartphones and standalone VR headsets. 

Key features:
* Hardware-accelerated video decoding which reduces CPU usage to approx 60% of Unity's built-in VideoPlayer. 
* Seamless looping - videos loop without audio gaps or pauses.
* Automatic fallback to lower resolution or lower frame-rate videos on lower-end devices based on maximum sizes supported by device hardware decoders. This means you can play e.g. 8K 360 video at 60fps on high end devices, and fall-back to 4k 30fps on lower-end devices without any scripting work.
* On non-Android platforms (and in the Unity editor), the plugin can fall back to using Unity's built-in VideoPlayer component. This means you can test in-editor without having to have different components on your GameObjects.


# Installing Direct Video Android

To install this package, follow the instructions in the [Package Manager documentation](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@latest/index.html). 


<a name="UsingPackageName"></a>
# Using Direct Video Android
To use the plugin, create a `RenderTexture`, and create a `Direct Video Player` component on a gameobject which references that RenderTexture. You can then assign this texture to a material as usual. The initial size of the RenderTexture is ignored, as it is resized to the video dimensions when a frame is received.

## Options
The Direct Video Player component has the following options:

**Target Texture** - the texture to render video to.

**Video Source** - the video file to play. This can be a file in the `StreamingAssets` folder, or a path to a file on the device. If you put files into `StreamingAssets`, they will be available on the file chooser here. If you are scripting this property, use paths relative to the `StreamingAssets` folder starting with `./` to references things built into the app (e.g. `./myvideo.mp4`)

**Play on Start** - if enabled, playback will start as soon as the component is created.

**Loop** - if enabled, playback will loop when the end of the video is reached.

In the `advanced` section there are various things that you are unlikely to need to mess with, these are:

**Audio source** - an optional AudioSource component to play audio from the video through. If not specified, any audio source on the same GameObject as the Direct Video Player component will be used, or a new one will be added if there is no existing audio source.

**Mode on non-Android platforms** - what to do if the component is used on a non-Android platform. Options are to do nothing, or to use Unity's built in VideoPlayer component instead.

**Internal logging** - enable this to get more detailed logging from the plugin, useful for debugging issues / support.

**Fallback Filename Pattern** - This pattern is used to find lower-resolution or lower-framerate versions of the video to use on less powerful devices. The pattern defaults to `{NAME}-fallback-{N}.{EXT}` and should include three placeholders:

`{NAME}` - Replaced by the base part of the original filename (without extension)

`{EXT}` - Replaced by the original file extension (e.g. mp4)

 `{N}`, this is replaced by 1, 2, 3 etc to find lower quality versions of the video. You can set priority ordering by naming your fallback videos accordingly.

# Scripting API

To use DirectVideo from C# scripts, look at the [API documentation](doxygen/dv_unity/index.html)


# Technical details
## Requirements

The plugin should work with any recent (Android api level>27 or greater) Android device using the Vulkan API. 

This version of Direct Video Android is compatible with the following versions of the Unity Editor:

* 6.0 and later (recommended)


## Package contents
>>>
This section includes the location of important files you want the user to know about. For example, if this is a sample package containing textures, models, and materials separated by sample group, you may want to provide the folder location of each group.
>>>

The following table indicates the &lt;describe the breakdown you used here&gt;:

|Location|Description|
|---|---|
|`Runtime`|Contains the runtime script which defines the `DirectVideoPlayer` class|
|`Editor`|Contains the editor inspector code for `DirectVideoPlayer` components.|
|`Plugins/Android`|Contains the Android .so files which implement the video playback functionality.|


## Document revision history
 
|Date|Reason|
|---|---|
|Jan 21, 2026|Unedited. Published to package.|
