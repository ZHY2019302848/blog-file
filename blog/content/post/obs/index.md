---
title: "Obs"
description: 
date: 2023-12-11T11:07:04+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories: 
    - 学习

---

第一步，把输出模式设为 高级 ，这样允许你进行高级操作，降低局限性。


# 录制设置
**录制路径**：这是你的视频文件保存位置，推荐选择大一点的磁盘，防止中断录制。

**生成没有空格的文件名**：生成的文件将不带有空格，而是用_代替。（用于不支持带有空格的文件名的场景）

**录像格式**：这个决定了你是视频将用什么格式封装，推荐的是.mkv，原因是它什么都能装。当然考虑到你的pr不支持mkv了，在主窗口点击文件，再点击录像转封装，拖入视频文件点转换封装即可转为pr支持的mp4；代价是文件略微变大~1mb。

**视频编码器**：重点！

总结：

**NVENC AV1 > NVENC HEVC > NVENC H.264 | H264 AMF | QuickSync H.264**

**SVT-AV1 > AOM-AV1**

x264 最低优先

+ x264 使用CPU进行h.264编码。在大部分情况，它的效果不如你的核显，关键是性能，如果你在录制过程中打开了高cpu占用的程序（例如大游戏），会编码过载导致录制的视频变卡顿，如果使用更低的预设则会导致编码质量不是很高，而且有时候还是会卡录制。如果你想有好的录制效果和希望cpu编码，考虑AV1编码器。

+ NVIDIA NVENC AV1 （至少需要英伟达40系显卡）使用GPU进行AV1编码。它会占用你显卡的Video Encode引擎，和游戏一般占用的3D和copy引擎是分开的，在大部分情况不会卡录制。（官方说明相较于NVENC H.264，效率提升1.45x）

+ NVIDIA NVENC HEVC （至少需要英伟达20系显卡）使用GPU进行hevc编码。它会占用你显卡的Video Encode引擎，和游戏一般占用的3D和copy引擎是分开的，在大部分情况不会卡录制，但是如果可以进行NVIDIA NVENC AV1 则不建议选择此编码器，因为AV1更有效率。（官方说明相较于NVENC H.264，效率提升1.15x）

+ NVIDIA NVENC H.264 （至少需要英伟达显卡）使用GPU进行h.264编码。它会占用你显卡的Video Encode引擎，和游戏一般占用的3D和copy引擎是分开的，在大部分情况不会卡录制，但是如果可以进行NVIDIA NVENC HEVC 则不建议选择此编码器，因为hevc更有效率。

+ AOM-AV1 使用CPU进行aom-AV1编码，需要强劲的cpu，否则编码质量不高，由此可得需要大量cpu，cpu使用量跌宕不平，由画面复杂度决定，并且和x264一样容易卡录制（如果你的cpu满载），所以推荐的是：后期使用ffmpeg压缩，代码如下，这个压缩效率高得多，不卡视频，缺点是速度慢。

```bash
ffmpeg -i %1 -c:v libsvtAV1 -crf 40 -bf 4 -preset 5 -g 240 ".\%~n1_SAV1.mp4"
```



+ SVT-AV1 使用CPU进行svt-AV1编码，需要强劲的cpu，否则编码质量不高，由此可得需要大量cpu，cpu使用量跌宕不平，由画面复杂度决定，并且和x264一样容易卡录制（如果你的cpu满载），所以推荐的是：后期使用ffmpeg压缩，代码如下，这个压缩效率高得多，不卡视频，缺点是速度慢。
对于aom-AV1，它的效率更高。
```bash
ffmpeg -i %1 -c:v libsvtAV1 -crf 40 -bf 4 -preset 5 -g 240 ".\%~n1_SAV1.mp4"
```

+ H264 AMF （至少需要AMD显卡）使用GPU进行h.264编码。

+ QuickSync H.264 （至少需要Intel显卡）使用GPU进行h.264编码。 如果你有核显，又有独显，用核显进行硬编码或许是一个不错的选择，因为这样就不会和游戏或其他大程序争夺资源。（前提是大程序不使用核显）

**音频编码器**：默认（FFmpeg AAC）

**音轨**：决定了将会保存哪些音轨，即使有些音轨是空的也会保存。搭配混音器设置。默认所有音源都映射到了所有音轨上，每个音轨都混合了所有音源。

**重新缩放输出**：将会缩放到指定分辨率。

**自定义混流器设置**：自定义混流器设置。

**自动分割文件**：会每隔自定义设置就截断视频并新创建视频文件，也可手动分割。

# 编码器设置
**速率控制**：决定用什么策略来分配码率（有一些是某些视频编码器没有的）

推荐CFR>CQP>VBR>ABR>CBR

+ CBR 固定每秒码率，每秒都会尽量分配一致的码率，不推荐在录制时使用这个，原因是在复杂的环境会导致画面模糊，简单的环境会导致浪费磁盘空间。推荐12000kbps（1080p）。
码率：8192kbps=8.192mbps=1mb/s

+ ABR 固定平均码率，会尽量把平均码率稳定到一定的值，它只是预测，不太能保证稳定到了，一般，它会在复杂的环境给多码率，简单的环境给少码率，而且会因为当前平均码率来做质量偏移。
码率：8192kbps=8.192mbps=1mb/s

+ VBR 可变码率，尽量让每秒的码率不超过目标码率而且在简单环境给少一些码率，仍然，在复杂度超过负荷的环境下，它还是会糊，建议12000kbps（1080p），调高一点目标码率就可以解决问题，但是会产生新问题，你还是会增加磁盘开销。
码率：目标码率，8192kbps=8.192mbps=1mb/s
最大码率：码率不会超过这个值，但实际上码率只会最高高于目标码率大约20%，所以此项设再高也没用。

+ CQP 固定每帧cq，cq难理解，说成画质就好理解了，一般，CQ级别增减6会导致视频大小增加1倍或减少一半，推荐在18-28，18的画质看起来就无损了，设为0代表无损编码。它不会管每秒到底花了多少磁盘空间，而是注意画质的好坏，28是普遍能接受的最低值。
如果你可以使用CFR，就不要使用CQP。
CQ 级别：CQ 级别。

+ 无损 无损编码，但是CQP 0 似乎兼容性更高。

+ CFR 更人性化的CQP，在高动态画面降低CQ级别来节省磁盘空间（因为高动态画面人眼难以捕捉细节，更高的画质可能作用不大，浪费磁盘空间），而低动态画面则会增加CQ级别来提高细节保留（因为低动态画面人眼将聚焦细节，更低的画质会令人厌烦，并且低动态画面即使保留细节也不会增加太多磁盘占用）
CFR：你可以理解为目标CQ级别。

关键帧间隔：每多少秒才存储一整张图片，高的值可以在低动态画面降低码率消耗，代价是播放时需要强劲的解码器（假如设为10s，你放pr里可能会卡的离谱，因为需要计算最多10秒的画面才能输出现在的画面），低的值可以在低配置设备上更流畅的播放和编辑，代价是磁盘遭老罪。最高设为10，推荐2-8

CPU使用预设（x264）：折磨cpu的指标（不是），更上面的占用更低的cpu和带来更低的编码质量，反之则反，不过受限于边际效应，更下面的预设不一定能带来更好的效果，比如你设为placebo，跟veryslow对比，质量提升大约1%，但是需要多100%的cpu性能。能设多低设多低吧（列表中的低）。

预设：和上述大差不差，有时候是用显卡罢了。

配置（Profile）：

配置：main10有更高压缩率。

调节：选高质量。

最大B帧：最多B帧

前向考虑：开启动态b帧，只需要足够多的b帧就可以完善画面，关闭则总为最大。

心理视觉调整：开

二次编码：能选多高选多高，但是预设更优先增加。

微调（Tune）：

* film  电影、真人类型。

* animation  动画。

* grain  需要保留大量的grain时用。

* stillimage  静态图像编码时使用。

* psnr  为提高psnr做了优化的参数。

* ssim 为提高ssim做了优化的参数。

* fastdecode  可以快速解码的参数。

* zerolatency 零延迟，用在需要非常低的延迟的情况下，比如电视电话会议的编码。

**x264选项（用空格分割）**：高级选项，可自定义x264参数。https://ffmpeg.org/

**FFmpeg 选项：**高级选项，可自定义FFmpeg参数。https://ffmpeg.org/



作者：寒琴庭霜wawdili https://www.bilibili.com/read/cv28371449/ 出处：bilibili