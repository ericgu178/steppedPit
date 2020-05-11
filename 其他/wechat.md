# 微信制作语音文件

> 环境需要
> 1. FFmpeg 
> 2. silk_v3_decode

## silk_v3_decode 的安装

压缩文件 [https://github.com/kn007/silk-v3-decoder/archive/master.zip](https://github.com/kn007/silk-v3-decoder/archive/master.zip) 下载解压缩 然后配置环境变量

检测配置是否成功 打开cmd 输入 `silk_v3_decoder` 有输出如下即配置成功

```cmd
Version:20160922    Build By kn007 (kn007.net)
Github: https://github.com/kn007/silk-v3-decoder

usage: silk_v3_decoder in.bit out.pcm [settings]

in.bit       : Bitstream input to decoder
out.pcm      : Speech output from decoder
   settings:
-Fs_API <Hz> : Sampling rate of output signal in Hz; default: 24000
-loss <perc> : Simulated packet loss percentage (0-100); default: 0
-quiet       : Print out just some basic values
```

## FFmpeg 的 安装

去[官网](https://ffmpeg.org/)下载 或者 [https://ericgu178.com/onedrive/个人软件清单/windows/语音制作.zip](https://ericgu178.com/onedrive/个人软件清单/windows/语音制作.zip) 这里应该有文件

同上 配置环境变量 检测成功 输入 `ffmpeg` 输出如下 即成功

```cmd
C:\Users\Administrator>ffmpeg
ffmpeg version git-2020-03-12-675bb1f Copyright (c) 2000-2020 the FFmpeg developers
  built with gcc 9.2.1 (GCC) 20200122
  configuration: --enable-gpl --enable-version3 --enable-sdl2 --enable-fontconfig --enable-gnutls --enable-iconv --enable-libass --enable-libdav1d --enable-libbluray --enable-libfreetype --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenjpeg --enable-libopus --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libtheora --enable-libtwolame --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxml2 --enable-libzimg --enable-lzma --enable-zlib --enable-gmp --enable-libvidstab --enable-libvorbis --enable-libvo-amrwbenc --enable-libmysofa --enable-libspeex --enable-libxvid --enable-libaom --enable-libmfx --enable-ffnvcodec --enable-cuda-llvm --enable-cuvid --enable-d3d11va --enable-nvenc --enable-nvdec --enable-dxva2 --enable-avisynth --enable-libopenmpt --enable-amf
  libavutil      56. 42.100 / 56. 42.100
  libavcodec     58. 75.100 / 58. 75.100
  libavformat    58. 41.100 / 58. 41.100
  libavdevice    58.  9.103 / 58.  9.103
  libavfilter     7. 77.100 /  7. 77.100
  libswscale      5.  6.101 /  5.  6.101
  libswresample   3.  6.100 /  3.  6.100
  libpostproc    55.  6.100 / 55.  6.100
Hyper fast Audio and Video encoder
usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...

Use -h to get full help or, even better, run 'man ffmpeg'

C:\Users\Administrator>
```

## 制作脚本文件

```c
E:\
cd 我的语音\压缩
rem 定义必要目录
for /f "delims=" %%i in ('dir /b /s /a-d *.mp3') do (
	rem 开始处理 %%~ni
	echo 开始处理 %%~ni >> "结果.log"
	set filename=%%~ni
	ffmpeg -y -i E:/我的语音/压缩/%%~ni.mp3 -f s16le -ar 24000 -ac 1 E:/我的语音/pcm/%%~ni.pcm
	silk_v3_encoder E:/我的语音/pcm/%%~ni.pcm E:/我的语音/silk语音/%%~ni.silk -tencent	
	echo 处理结束 %%~ni >> "结果.log"
	rem 处理结束 %%~ni	
)
```

解释 我先切换到 `e 盘` 进入到具体文件夹 然后循环目录里的 mp3 文件 <font size=5 color='skyblue'>先通过ffmpeg转换成为pcm数据文件</font> <font size=5 color='red'>再 将pcm文件 转换为 silk 文件</font> 处理过程 输出到 日志文件



这样就将文件制作好了 通过手动替换文件为slk文件 通过微信语音发出就ok了