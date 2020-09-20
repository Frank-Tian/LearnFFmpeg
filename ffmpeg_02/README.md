## FFmpeg 基本使用

### 安装 & 配置

上篇的文章已经对 FFmpeg 进行简单的介绍和安装实践。这里就可以开始使用 `ffmpeg` 命令进行玩耍了。

如果遇到下面的问题

```
$ ffmpeg 
-bash: ffmpeg: command not found
```
如果已经安装成功，很可能的原因就是，没有添加环境变量。
Mac OSX 添加环境变量比较简单,直接跟随下面的步骤即可。

```
$ cd ~
$ vi .profile
$ i (进入编辑模式)
$ export PATH=/usr/local/ffmpeg/bin:$PATH
$ wq （保存退出）
$ source .profile
```

再次输入 ffmpeg 命令，会输出以下信息：

```
ffmpeg

ffmpeg version git-2020-09-20-3d81d5e Copyright (c) 2000-2020 the FFmpeg developers
  built with Apple clang version 11.0.3 (clang-1103.0.32.62)
  configuration: --prefix=/usr/local/ffmpeg
  libavutil      56. 59.100 / 56. 59.100
  libavcodec     58.106.100 / 58.106.100
  libavformat    58. 58.100 / 58. 58.100
  libavdevice    58. 11.102 / 58. 11.102
  libavfilter     7. 87.100 /  7. 87.100
  libswscale      5.  8.100 /  5.  8.100
  libswresample   3.  8.100 /  3.  8.100
Hyper fast Audio and Video encoder
usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...
```

到这里，说明 FFmpeg 可以正常使用啦。

### FFmpeg 命令

FFmpeg 有很强的音视频处理功能，我们可以将其分为六大部分。

- ffmpeg 信息查询部分
- 公共操作参数部分
- 文件主要操作参数部分
- 视频操作参数部分
- 音频操作参数部分
- 字幕操作参数部分

那就来实际看看吧

```
ffmpeg --help

ffmpeg version git-2020-09-20-3d81d5e Copyright (c) 2000-2020 the FFmpeg developers
  built with Apple clang version 11.0.3 (clang-1103.0.32.62)
  configuration: --prefix=/usr/local/ffmpeg
  libavutil      56. 59.100 / 56. 59.100
  libavcodec     58.106.100 / 58.106.100
  libavformat    58. 58.100 / 58. 58.100
  libavdevice    58. 11.102 / 58. 11.102
  libavfilter     7. 87.100 /  7. 87.100
  libswscale      5.  8.100 /  5.  8.100
  libswresample   3.  8.100 /  3.  8.100
Hyper fast Audio and Video encoder
usage: ffmpeg [options] [[infile options] -i infile]... {[outfile options] outfile}...

Getting help:
    -h      -- print basic options
    -h long -- print more options
    -h full -- print all options (including all format and codec specific options, very long)
    -h type=name -- print all options for the named decoder/encoder/demuxer/muxer/filter/bsf/protocol
    See man ffmpeg for detailed description of the options.

Print help / information / capabilities:
-L                  show license
-h topic            show help
-? topic            show help
-help topic         show help
--help topic        show help
-version            show version
-buildconf          show build configuration
-formats            show available formats
-muxers             show available muxers
-demuxers           show available demuxers
-devices            show available devices
-codecs             show available codecs
-decoders           show available decoders
-encoders           show available encoders
-bsfs               show available bit stream filters
-protocols          show available protocols
-filters            show available filters
-pix_fmts           show available pixel formats
-layouts            show standard channel layouts
-sample_fmts        show available audio sample formats
-colors             show available color names
-sources device     list sources of the input device
-sinks device       list sinks of the output device
-hwaccels           show available HW acceleration methods

Global options (affect whole program instead of just one file):
-loglevel loglevel  set logging level
-v loglevel         set logging level
-report             generate a report
-max_alloc bytes    set maximum size of a single allocated block
-y                  overwrite output files
-n                  never overwrite output files
-ignore_unknown     Ignore unknown stream types
-filter_threads     number of non-complex filter threads
-filter_complex_threads  number of threads for -filter_complex
-stats              print progress report during encoding
-max_error_rate maximum error rate  ratio of errors (0.0: no errors, 1.0: 100% errors) above which ffmpeg returns an error instead of success.
-bits_per_raw_sample number  set the number of bits per raw sample
-vol volume         change audio volume (256=normal)

Per-file main options:
-f fmt              force format
-c codec            codec name
-codec codec        codec name
-pre preset         preset name
-map_metadata outfile[,metadata]:infile[,metadata]  set metadata information of outfile from infile
-t duration         record or transcode "duration" seconds of audio/video
-to time_stop       record or transcode stop time
-fs limit_size      set the limit file size in bytes
-ss time_off        set the start time offset
-sseof time_off     set the start time offset relative to EOF
-seek_timestamp     enable/disable seeking by timestamp with -ss
-timestamp time     set the recording timestamp ('now' to set the current time)
-metadata string=string  add metadata
-program title=string:st=number...  add program with specified streams
-target type        specify target file type ("vcd", "svcd", "dvd", "dv" or "dv50" with optional prefixes "pal-", "ntsc-" or "film-")
-apad               audio pad
-frames number      set the number of frames to output
-filter filter_graph  set stream filtergraph
-filter_script filename  read stream filtergraph description from a file
-reinit_filter      reinit filtergraph on input parameter changes
-discard            discard
-disposition        disposition

Video options:
-vframes number     set the number of video frames to output
-r rate             set frame rate (Hz value, fraction or abbreviation)
-s size             set frame size (WxH or abbreviation)
-aspect aspect      set aspect ratio (4:3, 16:9 or 1.3333, 1.7777)
-bits_per_raw_sample number  set the number of bits per raw sample
-vn                 disable video
-vcodec codec       force video codec ('copy' to copy stream)
-timecode hh:mm:ss[:;.]ff  set initial TimeCode value.
-pass n             select the pass number (1 to 3)
-vf filter_graph    set video filters
-ab bitrate         audio bitrate (please use -b:a)
-b bitrate          video bitrate (please use -b:v)
-dn                 disable data

Audio options:
-aframes number     set the number of audio frames to output
-aq quality         set audio quality (codec-specific)
-ar rate            set audio sampling rate (in Hz)
-ac channels        set number of audio channels
-an                 disable audio
-acodec codec       force audio codec ('copy' to copy stream)
-vol volume         change audio volume (256=normal)
-af filter_graph    set audio filters

Subtitle options:
-s size             set frame size (WxH or abbreviation)
-sn                 disable subtitle
-scodec codec       force subtitle codec ('copy' to copy stream)
-stag fourcc/tag    force subtitle tag/fourcc
-fix_sub_duration   fix subtitles duration
-canvas_size size   set canvas size (WxH or abbreviation)
-spre preset        set the subtitle options to the indicated preset
```

其他命令

```
ffmpeg --help full
ffmpeg --help long
ffmpeg -version     版本号
ffmpeg -formats     支持的格式
ffmpeg -codecs      支持的编码格式
ffmpeg -encoders    支持的编码器
ffmpeg -decoders    支持的解码器
ffmpeg -muxers      支持的封装格式
ffmpeg -demuxers    支持的解封装格式
ffmpeg -filters     支持的滤镜
```

可以使用命令查看详细的使用参数，格式为：

```
ffmpeg -h 操作参数=具体参数
ffmpeg -h muxer=flv
```

示例：
查看 flv 封装器的参数支持
```
ffmpeg -h muxer=flv

ffmpeg version git-2020-09-20-3d81d5e Copyright (c) 2000-2020 the FFmpeg developers
  built with Apple clang version 11.0.3 (clang-1103.0.32.62)
  configuration: --prefix=/usr/local/ffmpeg
  libavutil      56. 59.100 / 56. 59.100
  libavcodec     58.106.100 / 58.106.100
  libavformat    58. 58.100 / 58. 58.100
  libavdevice    58. 11.102 / 58. 11.102
  libavfilter     7. 87.100 /  7. 87.100
  libswscale      5.  8.100 /  5.  8.100
  libswresample   3.  8.100 /  3.  8.100
Muxer flv [FLV (Flash Video)]:
    Common extensions: flv.
    Mime type: video/x-flv.
    Default video codec: flv1.
    Default audio codec: adpcm_swf.
flv muxer AVOptions:
  -flvflags          <flags>      E......... FLV muxer flags (default 0)
     aac_seq_header_detect        E......... Put AAC sequence header based on stream data
     no_sequence_end              E......... disable sequence end for FLV
     no_metadata                  E......... disable metadata for FLV
     no_duration_filesize         E......... disable duration and filesize zero value metadata for FLV
     add_keyframe_index           E......... Add keyframe index metadata
```

查看H.264(AVC)的编码参数支持

```
ffmpeg -h encoder=h264

fmpeg version git-2020-09-20-3d81d5e Copyright (c) 2000-2020 the FFmpeg developers
  built with Apple clang version 11.0.3 (clang-1103.0.32.62)
  configuration: --prefix=/usr/local/ffmpeg
  libavutil      56. 59.100 / 56. 59.100
  libavcodec     58.106.100 / 58.106.100
  libavformat    58. 58.100 / 58. 58.100
  libavdevice    58. 11.102 / 58. 11.102
  libavfilter     7. 87.100 /  7. 87.100
  libswscale      5.  8.100 /  5.  8.100
  libswresample   3.  8.100 /  3.  8.100
Encoder h264_videotoolbox [VideoToolbox H.264 Encoder]:
    General capabilities: delay 
    Threading capabilities: none
    Supported pixel formats: videotoolbox_vld nv12 yuv420p
h264_videotoolbox AVOptions:
  -profile           <int>        E..V...... Profile (from 0 to 5) (default 0)
     baseline        1            E..V...... Baseline Profile
     main            2            E..V...... Main Profile
     high            3            E..V...... High Profile
     extended        4            E..V...... Extend Profile
  -level             <int>        E..V...... Level (from 0 to 52) (default 0)
     1.3             13           E..V...... Level 1.3, only available with Baseline Profile
     3.0             30           E..V...... Level 3.0
     3.1             31           E..V...... Level 3.1
     3.2             32           E..V...... Level 3.2
     4.0             40           E..V...... Level 4.0
     4.1             41           E..V...... Level 4.1
     4.2             42           E..V...... Level 4.2
     5.0             50           E..V...... Level 5.0
     5.1             51           E..V...... Level 5.1
     5.2             52           E..V...... Level 5.2
  -coder             <int>        E..V...... Entropy coding (from 0 to 2) (default 0)
     cavlc           1            E..V...... CAVLC entropy coding
     vlc             1            E..V...... CAVLC entropy coding
     cabac           2            E..V...... CABAC entropy coding
     ac              2            E..V...... CABAC entropy coding
  -a53cc             <boolean>    E..V...... Use A53 Closed Captions (if available) (default true)
  -allow_sw          <boolean>    E..V...... Allow software encoding (default false)
  -require_sw        <boolean>    E..V...... Require software encoding (default false)
  -realtime          <boolean>    E..V...... Hint that encoding should happen in real-time if not faster (e.g. capturing from camera). (default false)
  -frames_before     <boolean>    E..V...... Other frames will come before the frames in this session. This helps smooth concatenation issues. (default false)
  -frames_after      <boolean>    E..V...... Other frames will come after the frames in this session. This helps smooth concatenation issues. (default false)
```

### ffprobe 常用命令

ffprobe 是多媒体信息查看工具，用来查看多媒体文件的信息。

首先看下都支持哪些

```
ffprobe --help

######## 常用命令 ########
# 查看多媒体数据包信息
ffprobe -show_packets big_buck_bunny.mp4
输出片段：
[PACKET]
codec_type=data
stream_index=3
pts=1324032
pts_time=60.046803
dts=1324032
dts_time=60.046803
duration=1063
duration_time=0.048209
convergence_duration=N/A
convergence_duration_time=N/A
size=64
pos=5510808
flags=K_
[/PACKET]

# 查看具体数据信息
ffprobe -show_packets -show_data big_buck_bunny.mp4
输出片段：
[PACKET]
codec_type=audio
stream_index=0
pts=1324032
pts_time=60.046803
dts=1324032
dts_time=60.046803
duration=1024
duration_time=0.046440
convergence_duration=N/A
convergence_duration_time=N/A
size=281
pos=5510520
flags=K_
data=
00000000: 214f e74a c14a a892 2374 8714 0284 087e  !O.J.J..#t.....~
00000010: 573a 937a 4d43 8fd1 96e0 f28e 10e3 2db2  W:.zMC........-.
00000020: 3f14 14f1 f04a 9246 1ae4 3328 75c2 3765  ?....J.F..3(u.7e
00000030: 2bb3 d70e 6826 3be9 8a1a f380 11aa 1008  +...h&;.........
00000040: 521b 2e9c 3180 07a3 72e6 eb0f 1c5e 6fcc  R...1...r....^o.
00000050: 8f46 b5a1 8c29 5bf8 7079 6762 daeb 7477  .F...)[.pygb..tw
00000060: a4cd b305 470e 4e7c 2fd7 cf2f b0ed a808  ....G.N|/../....
00000070: b668 723b 6bde 5d9a 90e2 dbec ea9a 7687  .hr;k.].......v.
00000080: f3fc b5f8 01a6 e256 fae0 c21f 6f23 5988  .......V....o#Y.
00000090: 3e3d c50d 24d4 9596 4945 0114 04cd 62e8  >=..$...IE....b.
000000a0: 3f26 9d28 70eb d7af 25ed f44c 10e3 8486  ?&.(p...%..L....
000000b0: 201f c505 3c7c 1bb7 8cdf 25f1 59db 6229   ...<|....%.Y.b)
000000c0: ce66 223f 5bcc d42f 18c1 8c65 b346 b3aa  .f"?[../...e.F..
000000d0: cf27 3f27 89b8 49bb 58b6 e66a a13c 2e06  .'?'..I.X..j.<..
000000e0: b0f2 d3bf 4012 fd59 f300 0e5e 3f96 5ecc  ....@..Y...^?.^.
000000f0: e91c 4827 cc62 6bfb 91e8 0b37 bad6 462d  ..H'.bk....7..F-
00000100: 0140 1dc2 a15e bc27 e473 ee79 3165 68ff  .@...^.'.s.y1eh.
00000110: 19dc 7000 df3f 7c5c dc                   ..p..?|\.

[/PACKET]

```

查看封装格式

```
ffprobe -show_format big_buck_bunny.mp4

Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'big_buck_bunny.mp4':
  Metadata:
    major_brand     : mp42
    minor_version   : 1
    compatible_brands: mp42avc1
    creation_time   : 2010-02-09T01:55:39.000000Z
  Duration: 00:01:00.10, start: 0.000000, bitrate: 733 kb/s
    Stream #0:0(eng): Audio: aac (LC) (mp4a / 0x6134706D), 22050 Hz, stereo, fltp, 65 kb/s (default)
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : Apple Sound Media Handler
    Stream #0:1(eng): Video: h264 (Constrained Baseline) (avc1 / 0x31637661), yuv420p(tv, smpte170m/smpte170m/bt709), 640x360, 612 kb/s, 23.96 fps, 24 tbr, 600 tbn, 1200 tbc (default)
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : Apple Video Media Handler
    Stream #0:2(eng): Data: none (rtp  / 0x20707472), 45 kb/s
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : hint media handler
    Stream #0:3(eng): Data: none (rtp  / 0x20707472), 5 kb/s
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : hint media handler
Unsupported codec with id 0 for input stream 2
Unsupported codec with id 0 for input stream 3
[FORMAT]
filename=big_buck_bunny.mp4
nb_streams=4
nb_programs=0
format_name=mov,mp4,m4a,3gp,3g2,mj2
format_long_name=QuickTime / MOV
start_time=0.000000
duration=60.095000
size=5510872
bit_rate=733621
probe_score=100
TAG:major_brand=mp42
TAG:minor_version=1
TAG:compatible_brands=mp42avc1
TAG:creation_time=2010-02-09T01:55:39.000000Z
[/FORMAT]
```

查看帧信息

```
ffprobe -show_frames big_buck_bunny.mp4

[FRAME]
media_type=video
stream_index=1
key_frame=0
pkt_pts=35400
pkt_pts_time=59.000000
pkt_dts=35400
pkt_dts_time=59.000000
best_effort_timestamp=35400
best_effort_timestamp_time=59.000000
pkt_duration=25
pkt_duration_time=0.041667
pkt_pos=5421528
pkt_size=2599
width=640
height=360
pix_fmt=yuv420p
sample_aspect_ratio=N/A
pict_type=P
coded_picture_number=1416
display_picture_number=0
interlaced_frame=0
top_field_first=0
repeat_pict=0
color_range=tv
color_space=smpte170m
color_primaries=smpte170m
color_transfer=bt709
chroma_location=topleft
[SIDE_DATA]
side_data_type=H.26[45] User Data Unregistered SEI message
[/SIDE_DATA]
[/FRAME]
```

查看多媒体文件中的流信息

```
ffprobe -show_streams big_buck_bunny.mp4

Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'big_buck_bunny.mp4':
  Metadata:
    major_brand     : mp42
    minor_version   : 1
    compatible_brands: mp42avc1
    creation_time   : 2010-02-09T01:55:39.000000Z
  Duration: 00:01:00.10, start: 0.000000, bitrate: 733 kb/s
    Stream #0:0(eng): Audio: aac (LC) (mp4a / 0x6134706D), 22050 Hz, stereo, fltp, 65 kb/s (default)
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : Apple Sound Media Handler
    Stream #0:1(eng): Video: h264 (Constrained Baseline) (avc1 / 0x31637661), yuv420p(tv, smpte170m/smpte170m/bt709), 640x360, 612 kb/s, 23.96 fps, 24 tbr, 600 tbn, 1200 tbc (default)
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : Apple Video Media Handler
    Stream #0:2(eng): Data: none (rtp  / 0x20707472), 45 kb/s
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : hint media handler
    Stream #0:3(eng): Data: none (rtp  / 0x20707472), 5 kb/s
    Metadata:
      creation_time   : 2010-02-09T01:55:39.000000Z
      handler_name    : hint media handler
Unsupported codec with id 0 for input stream 2
Unsupported codec with id 0 for input stream 3
[STREAM]
index=0
codec_name=aac
codec_long_name=AAC (Advanced Audio Coding)
profile=LC
codec_type=audio
codec_time_base=1/22050
codec_tag_string=mp4a
codec_tag=0x6134706d
sample_fmt=fltp
sample_rate=22050
channels=2
channel_layout=stereo
bits_per_sample=0
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/22050
start_pts=0
start_time=0.000000
duration_ts=1325095
duration=60.095011
bit_rate=65075
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=1295
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
TAG:creation_time=2010-02-09T01:55:39.000000Z
TAG:language=eng
TAG:handler_name=Apple Sound Media Handler
[/STREAM]
[STREAM]
index=1
codec_name=h264
codec_long_name=H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10
profile=Constrained Baseline
codec_type=video
codec_time_base=12019/576000
codec_tag_string=avc1
codec_tag=0x31637661
width=640
height=360
coded_width=640
coded_height=368
closed_captions=0
has_b_frames=0
sample_aspect_ratio=N/A
display_aspect_ratio=N/A
pix_fmt=yuv420p
level=30
color_range=tv
color_space=smpte170m
color_transfer=bt709
color_primaries=smpte170m
chroma_location=topleft
field_order=unknown
timecode=N/A
refs=1
is_avc=true
nal_length_size=4
id=N/A
r_frame_rate=24/1
avg_frame_rate=288000/12019
time_base=1/600
start_pts=0
start_time=0.000000
duration_ts=36057
duration=60.095000
bit_rate=612177
max_bit_rate=N/A
bits_per_raw_sample=8
nb_frames=1440
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
TAG:creation_time=2010-02-09T01:55:39.000000Z
TAG:language=eng
TAG:handler_name=Apple Video Media Handler
[/STREAM]
[STREAM]
index=2
codec_name=unknown
codec_long_name=unknown
profile=unknown
codec_type=data
codec_tag_string=rtp
codec_tag=0x20707472
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/90000
start_pts=0
start_time=0.000000
duration_ts=5408550
duration=60.095000
bit_rate=45858
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=1440
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=0
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
TAG:creation_time=2010-02-09T01:55:39.000000Z
TAG:language=eng
TAG:handler_name=hint media handler
[/STREAM]
[STREAM]
index=3
codec_name=unknown
codec_long_name=unknown
profile=unknown
codec_type=data
codec_tag_string=rtp
codec_tag=0x20707472
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/22050
start_pts=0
start_time=0.000000
duration_ts=1325095
duration=60.095011
bit_rate=5514
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=648
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=0
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
DISPOSITION:timed_thumbnails=0
TAG:creation_time=2010-02-09T01:55:39.000000Z
TAG:language=eng
TAG:handler_name=hint media handler
[/STREAM]
```

如果想要以指定的格式输出，可以直接指定

```
ffprobe -show_streams -print_format json big_buck_bunny.mp4
ffprobe -show_streams -of xml big_buck_bunny.mp4
ffprobe -show_streams -print_format flat big_buck_bunny.mp4

支持的形式有：xml 、 ini、json、csv、flat 等。
```

### ffplay 常用命令

ffplay 不仅仅是播放器，也是测试 ffmpeg 各个引擎的工具。

```
ffplay --help
```