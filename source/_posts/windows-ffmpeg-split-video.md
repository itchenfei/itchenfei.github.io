---
title: Windows下使用FFmpeg切分视频文件
date: 2022-04-01 11:29:30
tags: ffmpeg
categories:  ffmpeg
---

# 下载FFMPEG

从 http://ffmpeg.org/download.html 官网，选择合适的版本。  
  
下面以Windows系统为例。  

# 切分视频

以Windows为例，首先打开下载的`ffmpeg-master-latest-win64-gpl.zip`压缩包，将`ffmpeg.exe`文件复制到同一级目录  

运行下面命令对视频进行切分  
```text
./ffmpeg.exe  -i ./video2511504394.mp4 -vcodec copy -acodec copy -ss 01:30:02 -to 01:59:10 ./cut_1.mp4 -y
```

参数解释，从左到右：  

./ffmpeg.exe指定FFMPEG文件为当前目录；  
-i 指定一个需要切分的视频文件，这里指定的是当前目录下的`video2511504394.mp4`文件  
-vcodec copy 表示使用跟原视频一样的视频编解码器  
-acodec copy 表示使用跟原视频一样的音频编解码器  
-ss 指定开始剪切的时间点，格式 时/分/秒  
-to 指定视频结束的时间点，格式 时/分/秒  
./cut_1.mp4 指定剪切后的视频片段的名称  
-y 如果指定的名称的文件存在，则覆盖  