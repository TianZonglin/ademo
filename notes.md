视频无法播放，因为该文件已损坏




媒体资源 http://localhost:8080/AE-3D-variance-s0.03-R0.1-Concrete.mp4 未能解码，错误：Error Code: NS_ERROR_DOM_MEDIA_FATAL_ERR (0x806e0005)
Details: auto __cdecl mozilla::SupportChecker::AddMediaFormatChecker(const mozilla::TrackInfo &)::(anonymous class)::operator()(void) const: Decoder may not have the capability to handle the requested video format with YUV444 chroma subsampling.

Firefox不支持YUV444 chroma subsampling 视频解码，但有人指出使用ffmpeg编辑视频时，默认生成的编码格式就是这个，这里有无数针对此问题的bug记录。可以看到无数人重复的提交这个相同的bug，但看起来Firefox并不愿意支持这类格式。https://bugzilla.mozilla.org/show_bug.cgi?id=1368063

下一步

批量转换原有视频文件，借助格式工厂，然后覆盖原有视频Git库，重新提交。

命令行参数-crf

在优先保证画面质量（也不太在乎转码时间）的情况下，使用-crf参数来控制转码是比较适宜的。这个参数的取值范围为0~51，其中0为无损模式，数值越大，画质越差，生成的文件却越小。从主观上讲，18~28是一个合理的范围。18被认为是视觉无损的（从技术角度上看当然还是有损的），它的输出视频质量和输入视频相当。

我们的策略是，在保证可接受视频画质的前提下，选择一个最大的crf值——如果输出视频质量很好，那就尝试一个更大的值；如果看起来很糟，那就尝试一个小一点的值。

让我们先执行下面这条命令（关于FFmpeg运行环境的配置，请参阅这篇文章）：

ffmpeg -i D:\src.mov -c:v libx264 -preset veryslow -crf 18 -c:a copy D:\dest1.mp4
 



Well, the problem is the mismatching format of videos, I have know that videos with [YUV444 chroma subsampling format] can't be supported by Firefox ([bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1368063)), I guess Safari has the same problem. It caused by the screen recording tools we used when we doing the experiment. I noticed that videos made by Gijs are the right format [AVC(H.264) format] but mine and Rui's format [YUV444 chroma subsampling] are not. Anyway they are all supported by Chrome or Edge.

So, this could be a guide for us that if we want to show videos in multiple browser, we'd better to make sure that vidos format are H.264/AVC.

I have convert all videos formats to H.264 and now they can work well with Firefox and Safari.

I think IE now is not a recommand browser for most cases, and Microsoft is abandoning this project now, so I just added a warning message when users use IE to visit. 