# 常用 youtube-dl 命令


> 记录一些常用的 youtube-dl 命令，拯救脑容量。

<!--more-->

## 下载播放列表视频+音频

Windows

```cmd
youtube-dl.exe -o "D:\xxxxxx\%(title)s.%(ext)s" -f bestvideo+bestaudio https://www.youtube.com/playlist?list=x
```

## 仅下载播放列表音频
```cmd
youtububedl.exe -o D:\%(title)s.%(ext)s -f bestaudio --extract-audio --audio-format mp3 --audio-quality 0 https://www.youtube.com/playlist?list=xxxxxxxxxxxx

```

```
-o D:\%(title)s.%(ext)s     #保留原始名称和后缀
-f bestaudio                #最佳音频
--extract-audio             #提取音频
--audio-format mp3          #设置音频格式，下载文件若不同，则调用ffmpeg转换
--playlist-items 1,2,5      #下载播放列表中的哪些项
--playstart-start 2         #从第二项开始下载播放列表
--playstart-end 2
-f 'bestvideo[height<=1080]+bestaudio/best[height<=1080]'             #设置最大下载分辨率
```

