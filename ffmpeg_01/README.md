
## FFmpeg 简介

FFmpeg（Fast Forward mpeg）是一款开源的音视频编解码工具及开发套件，它内部实现有很多有用的 API 封装，能够满足常见的音视频处理需求。

FFmpeg 库地址：

[https://github.com/FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg)

### FFmpeg 组成模块：

- AVFormat 封装模块: 

    实现了多媒体领域绝大多数媒体封装格式，如：MP4、FLV、KV、TS 等，以及 RTMP、RTSP、MMS、HLS 等网络协议封装格式。

- AVCodec 编解码模块：

    实现了绝大多数常见编解码格式，如：MPEG4、AAC、MJPEG等，还支持第三方编解码器，如 H.264(AVC)、H.264(HEVC)、MP3(mp3lame)等。

- AVFilter 滤镜模块：
    
    提供通用的音频、视频、字幕等滤镜处理框架。
    
- swscale 视频图像转换计算模块：

    提供了高级别图像转换 API ,如YUV 转 RGB 等图像格式转换。

- swresample 音频转换计算模块：

    提供高级别的音频重采样 API
    
- AVDevice 
- AVUtil
- ...

### FFmpeg 工作流程

ffmpeg 工作流程如下：

1. 解封装 Demuxing
2. 解码 Decoding
3. 编码 Encoding
4. 封装 Muxing

执行步骤如下：

1. 读取输入源
2. 进行音视频的解封装 （Demuxer）
3. 解码每一帧音视频数据 (Decoder)
4. 编码每一帧音视频数据 (Encoder)
5. 进行音视频的重新封装 (Muxer)
6. 输出到目标

转码工作流程：

读取文件 -->  解封装 --> 解码 --> 转换参数 --> 新编码 --> 封装 --> 写入文件

其他工具 ：

- ffplay : 播放器
- ffprobe : 多媒体分析器

### FFmpeg 编译

一般情况下，直接可以使用已经编译好的可执行文件，如果遇到自定义或减包等需求的时候，可以自行手动编译即可。

因为 FFmpeg 是跨平台的，支持多平台的编译，这里只记录下 Mac OSX 平台的编译。

编译环境：LLVM ，如果已经安装 Xcode，则默认已经存在编译环境了。

编译步骤：

1. 下载 FFmpeg 源码：[https://github.com/FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg)

    如果 git clone 慢的话，直接下载 zip 文件即可。

2. 安装 sdl

	brew install sdl

3. 安装 yasm

    brew install yasm
    
4. 执行配置 config

     ./configure --prefix=/usr/local/ffmpeg
     
5. make 编译

6. make install 安装

### FFmpeg 定制编译

上面已经提到了，FFmpeg 是支持自定义编译的，我们可以通过输入配置参数来做到动态控制，可以通过 config --help 来查看支持的配置命令。

我们可以配置 FFmpeg 仅支持 H.264 视频和 AAC 音频编码，示例配置项如下：

```
./config --enable-libx264 --enable-libfdk-aac --enable-gpl --enable-nonfree
```

当然，直接执行上面的命令，可能会遇到错误，因为我们指定了 H.264、AAC 但是我们可能没有安装相应的依赖库，因此需要先安装对应的支持库 libx264 和 fdkaac
```
brew install x264
brew install fdk-aac
brew install x265
```

同理，如果要想支持 H.265 则需要添加
```
--enable-libx265
```

事实上，FFmpeg 是支持裁剪的，根据自身的需求来定制化编译，简单示例如下，全部命令，见最下方 `[Individual component options]` 部分。

示例：
```
./configure --prefix=/usr/local/ffmpeg --enable-gpl --enable-nonfree --enable-libfdk-aac --enable-libx264 --enable-libx265 --enable-filter=delogo --enable-debug --disable-optimizations --enable-libspeex --enable-videotoolbox --enable-shared --enable-pthreads --enable-version3 --enable-hardcoded-tables --cc=clang --host-cflags= --host-ldflags=
```

完成之后，执行make
```
make 
make intall
```

如果报错 `/usr/local/ffmpeg/share/man/man1: Permission denied`  很有可能是没有这个文件夹，直接创建 ffmpeg 目录，重新 install 即可
完成之后，就看到 
`/usr/local/ffmpeg` 路径下已经有很多文件了，大概如下：

```
├── bin
│   ├── ffmpeg
│   └── ffprobe
├── include
│   ├── libavcodec
│   ├── libavdevice
│   ├── libavfilter
│   ├── libavformat
│   ├── libavutil
│   ├── libswresample
│   └── libswscale
├── lib
│   ├── libavcodec.a
│   ├── libavdevice.a
│   ├── libavfilter.a
│   ├── libavformat.a
│   ├── libavutil.a
│   ├── libswresample.a
│   ├── libswscale.a
│   └── pkgconfig
└── share
    ├── ffmpeg
    └── man
```

到这里，编译安装就完成了。

### 编码器支持

FFmpeg 源码中支持了很多的编码，我们可以输出看看。

```
./configure --list-encoders

a64multi                jpegls                  pcm_s32be
a64multi5               libaom_av1              pcm_s32le
aac                     libcodec2               pcm_s32le_planar
aac_at                  libfdk_aac              pcm_s64be
aac_mf                  libgsm                  pcm_s64le
ac3                     libgsm_ms               pcm_s8
ac3_fixed               libilbc                 pcm_s8_planar
ac3_mf                  libkvazaar              pcm_u16be
adpcm_adx               libmp3lame              pcm_u16le
adpcm_argo              libopencore_amrnb       pcm_u24be
adpcm_g722              libopenh264             pcm_u24le
adpcm_g726              libopenjpeg             pcm_u32be
adpcm_g726le            libopus                 pcm_u32le
adpcm_ima_apm           librav1e                pcm_u8
adpcm_ima_qt            libshine                pcm_vidc
adpcm_ima_ssi           libspeex                pcx
adpcm_ima_wav           libsvtav1               pgm
adpcm_ms                libtheora               pgmyuv
adpcm_swf               libtwolame              png
adpcm_yamaha            libvo_amrwbenc          ppm
alac                    libvorbis               prores
alac_at                 libvpx_vp8              prores_aw
alias_pix               libvpx_vp9              prores_ks
amv                     libwavpack              qtrle
apng                    libwebp                 r10k
aptx                    libwebp_anim            r210
aptx_hd                 libx262                 ra_144
ass                     libx264                 rawvideo
asv1                    libx264rgb              roq
asv2                    libx265                 roq_dpcm
avrp                    libxavs                 rpza
avui                    libxavs2                rv10
ayuv                    libxvid                 rv20
bmp                     ljpeg                   s302m
cfhd                    magicyuv                sbc
cinepak                 mjpeg                   sgi
cljr                    mjpeg_qsv               snow
comfortnoise            mjpeg_vaapi             sonic
dca                     mlp                     sonic_ls
dnxhd                   movtext                 srt
dpx                     mp2                     ssa
dvbsub                  mp2fixed                subrip
dvdsub                  mp3_mf                  sunrast
dvvideo                 mpeg1video              svq1
eac3                    mpeg2_qsv               targa
ffv1                    mpeg2_vaapi             text
ffvhuff                 mpeg2video              tiff
fits                    mpeg4                   truehd
flac                    mpeg4_omx               tta
flashsv                 mpeg4_v4l2m2m           utvideo
flashsv2                msmpeg4v2               v210
flv                     msmpeg4v3               v308
g723_1                  msvideo1                v408
gif                     nellymoser              v410
h261                    nvenc                   vc2
h263                    nvenc_h264              vorbis
h263_v4l2m2m            nvenc_hevc              vp8_v4l2m2m
h263p                   opus                    vp8_vaapi
h264_amf                pam                     vp9_qsv
h264_mf                 pbm                     vp9_vaapi
h264_nvenc              pcm_alaw                wavpack
h264_omx                pcm_alaw_at             webvtt
h264_qsv                pcm_dvd                 wmav1
h264_v4l2m2m            pcm_f32be               wmav2
h264_vaapi              pcm_f32le               wmv1
h264_videotoolbox       pcm_f64be               wmv2
hap                     pcm_f64le               wrapped_avframe
hevc_amf                pcm_mulaw               xbm
hevc_mf                 pcm_mulaw_at            xface
hevc_nvenc              pcm_s16be               xsub
hevc_qsv                pcm_s16be_planar        xwd
hevc_v4l2m2m            pcm_s16le               y41p
hevc_vaapi              pcm_s16le_planar        yuv4
hevc_videotoolbox       pcm_s24be               zlib
huffyuv                 pcm_s24daud             zmbv
ilbc_at                 pcm_s24le

```

### 解码器支持

FFmpeg 也包含了很多的解码支持，就是对压缩过的编码进行解压缩。

```
./configure --list-decoders

aac                     gsm_ms_at               pcm_u16le
aac_at                  h261                    pcm_u24be
aac_fixed               h263                    pcm_u24le
aac_latm                h263_v4l2m2m            pcm_u32be
aasc                    h263i                   pcm_u32le
ac3                     h263p                   pcm_u8
ac3_at                  h264                    pcm_vidc
ac3_fixed               h264_crystalhd          pcx
acelp_kelvin            h264_cuvid              pfm
adpcm_4xm               h264_mediacodec         pgm
adpcm_adx               h264_mmal               pgmyuv
adpcm_afc               h264_qsv                pgssub
adpcm_agm               h264_rkmpp              pgx
adpcm_aica              h264_v4l2m2m            photocd
adpcm_argo              hap                     pictor
adpcm_ct                hca                     pixlet
adpcm_dtk               hcom                    pjs
adpcm_ea                hevc                    png
adpcm_ea_maxis_xa       hevc_cuvid              ppm
adpcm_ea_r1             hevc_mediacodec         prores
adpcm_ea_r2             hevc_qsv                prosumer
adpcm_ea_r3             hevc_rkmpp              psd
adpcm_ea_xas            hevc_v4l2m2m            ptx
adpcm_g722              hnm4_video              qcelp
adpcm_g726              hq_hqa                  qdm2
adpcm_g726le            hqx                     qdm2_at
adpcm_ima_alp           huffyuv                 qdmc
adpcm_ima_amv           hymt                    qdmc_at
adpcm_ima_apc           iac                     qdraw
adpcm_ima_apm           idcin                   qpeg
adpcm_ima_cunning       idf                     qtrle
adpcm_ima_dat4          iff_ilbm                r10k
adpcm_ima_dk3           ilbc                    r210
adpcm_ima_dk4           ilbc_at                 ra_144
adpcm_ima_ea_eacs       imc                     ra_288
adpcm_ima_ea_sead       imm4                    ralf
adpcm_ima_iss           imm5                    rasc
adpcm_ima_moflex        indeo2                  rawvideo
adpcm_ima_mtf           indeo3                  realtext
adpcm_ima_oki           indeo4                  rl2
adpcm_ima_qt            indeo5                  roq
adpcm_ima_qt_at         interplay_acm           roq_dpcm
adpcm_ima_rad           interplay_dpcm          rpza
adpcm_ima_smjpeg        interplay_video         rscc
adpcm_ima_ssi           jacosub                 rv10
adpcm_ima_wav           jpeg2000                rv20
adpcm_ima_ws            jpegls                  rv30
adpcm_ms                jv                      rv40
adpcm_mtaf              kgv1                    s302m
adpcm_psx               kmvc                    sami
adpcm_sbpro_2           lagarith                sanm
adpcm_sbpro_3           libaom_av1              sbc
adpcm_sbpro_4           libaribb24              scpr
adpcm_swf               libcelt                 screenpresso
adpcm_thp               libcodec2               sdx2_dpcm
adpcm_thp_le            libdav1d                sgi
adpcm_vima              libdavs2                sgirle
adpcm_xa                libfdk_aac              sheervideo
adpcm_yamaha            libgsm                  shorten
adpcm_zork              libgsm_ms               sipr
agm                     libilbc                 siren
aic                     libopencore_amrnb       smackaud
alac                    libopencore_amrwb       smacker
alac_at                 libopenh264             smc
alias_pix               libopenjpeg             smvjpeg
als                     libopus                 snow
amr_nb_at               librsvg                 sol_dpcm
amrnb                   libspeex                sonic
amrwb                   libvorbis               sp5x
amv                     libvpx_vp8              speedhq
anm                     libvpx_vp9              srgc
ansi                    libzvbi_teletext        srt
ape                     loco                    ssa
apng                    lscr                    stl
aptx                    m101                    subrip
aptx_hd                 mace3                   subviewer
arbc                    mace6                   subviewer1
ass                     magicyuv                sunrast
asv1                    mdec                    svq1
asv2                    metasound               svq3
atrac1                  microdvd                tak
atrac3                  mimic                   targa
atrac3al                mjpeg                   targa_y216
atrac3p                 mjpeg_cuvid             tdsc
atrac3pal               mjpeg_qsv               text
atrac9                  mjpegb                  theora
aura                    mlp                     thp
aura2                   mmvideo                 tiertexseqvideo
av1                     mobiclip                tiff
avrn                    motionpixels            tmv
avrp                    movtext                 truehd
avs                     mp1                     truemotion1
avui                    mp1_at                  truemotion2
ayuv                    mp1float                truemotion2rt
bethsoftvid             mp2                     truespeech
bfi                     mp2_at                  tscc
bink                    mp2float                tscc2
binkaudio_dct           mp3                     tta
binkaudio_rdft          mp3_at                  twinvq
bintext                 mp3adu                  txd
bitpacked               mp3adufloat             ulti
bmp                     mp3float                utvideo
bmv_audio               mp3on4                  v210
bmv_video               mp3on4float             v210x
brender_pix             mpc7                    v308
c93                     mpc8                    v408
cavs                    mpeg1_cuvid             v410
ccaption                mpeg1_v4l2m2m           vb
cdgraphics              mpeg1video              vble
cdtoons                 mpeg2_crystalhd         vc1
cdxl                    mpeg2_cuvid             vc1_crystalhd
cfhd                    mpeg2_mediacodec        vc1_cuvid
cinepak                 mpeg2_mmal              vc1_mmal
clearvideo              mpeg2_qsv               vc1_qsv
cljr                    mpeg2_v4l2m2m           vc1_v4l2m2m
cllc                    mpeg2video              vc1image
comfortnoise            mpeg4                   vcr1
cook                    mpeg4_crystalhd         vmdaudio
cpia                    mpeg4_cuvid             vmdvideo
cscd                    mpeg4_mediacodec        vmnc
cyuv                    mpeg4_mmal              vorbis
dca                     mpeg4_v4l2m2m           vp3
dds                     mpegvideo               vp4
derf_dpcm               mpl2                    vp5
dfa                     msa1                    vp6
dirac                   mscc                    vp6a
dnxhd                   msmpeg4_crystalhd       vp6f
dolby_e                 msmpeg4v1               vp7
dpx                     msmpeg4v2               vp8
dsd_lsbf                msmpeg4v3               vp8_cuvid
dsd_lsbf_planar         msrle                   vp8_mediacodec
dsd_msbf                mss1                    vp8_qsv
dsd_msbf_planar         mss2                    vp8_rkmpp
dsicinaudio             msvideo1                vp8_v4l2m2m
dsicinvideo             mszh                    vp9
dss_sp                  mts2                    vp9_cuvid
dst                     mv30                    vp9_mediacodec
dvaudio                 mvc1                    vp9_qsv
dvbsub                  mvc2                    vp9_rkmpp
dvdsub                  mvdv                    vp9_v4l2m2m
dvvideo                 mvha                    vplayer
dxa                     mwsc                    vqa
dxtory                  mxpeg                   wavpack
dxv                     nellymoser              wcmv
eac3                    notchlc                 webp
eac3_at                 nuv                     webvtt
eacmv                   on2avc                  wmalossless
eamad                   opus                    wmapro
eatgq                   paf_audio               wmav1
eatgv                   paf_video               wmav2
eatqi                   pam                     wmavoice
eightbps                pbm                     wmv1
eightsvx_exp            pcm_alaw                wmv2
eightsvx_fib            pcm_alaw_at             wmv3
escape124               pcm_bluray              wmv3_crystalhd
escape130               pcm_dvd                 wmv3image
evrc                    pcm_f16le               wnv1
exr                     pcm_f24le               wrapped_avframe
fastaudio               pcm_f32be               ws_snd1
ffv1                    pcm_f32le               xan_dpcm
ffvhuff                 pcm_f64be               xan_wc3
ffwavesynth             pcm_f64le               xan_wc4
fic                     pcm_lxf                 xbin
fits                    pcm_mulaw               xbm
flac                    pcm_mulaw_at            xface
flashsv                 pcm_s16be               xl
flashsv2                pcm_s16be_planar        xma1
flic                    pcm_s16le               xma2
flv                     pcm_s16le_planar        xpm
fmvc                    pcm_s24be               xsub
fourxm                  pcm_s24daud             xwd
fraps                   pcm_s24le               y41p
frwu                    pcm_s24le_planar        ylc
g2m                     pcm_s32be               yop
g723_1                  pcm_s32le               yuv4
g729                    pcm_s32le_planar        zero12v
gdv                     pcm_s64be               zerocodec
gif                     pcm_s64le               zlib
gremlin_dpcm            pcm_s8                  zmbv
gsm                     pcm_s8_planar
gsm_ms                  pcm_u16be
```

### FFmpeg 封装支持

FFmpeg 的封装（Muxing）是指将压缩后的编码封装到一个容器格式中。支持的封装格式有很多

```
./configure --list-muxers

a64                     hds                     pcm_s24le
ac3                     hevc                    pcm_s32be
adts                    hls                     pcm_s32le
adx                     ico                     pcm_s8
aiff                    ilbc                    pcm_u16be
amr                     image2                  pcm_u16le
apm                     image2pipe              pcm_u24be
apng                    ipod                    pcm_u24le
aptx                    ircam                   pcm_u32be
aptx_hd                 ismv                    pcm_u32le
argo_asf                ivf                     pcm_u8
asf                     jacosub                 pcm_vidc
asf_stream              kvag                    psp
ass                     latm                    rawvideo
ast                     lrc                     rm
au                      m4v                     roq
avi                     matroska                rso
avm2                    matroska_audio          rtp
avs2                    md5                     rtp_mpegts
bit                     microdvd                rtsp
caf                     mjpeg                   sap
cavsvideo               mkvtimestamp_v2         sbc
chromaprint             mlp                     scc
codec2                  mmf                     segafilm
codec2raw               mov                     segment
crc                     mp2                     singlejpeg
dash                    mp3                     smjpeg
data                    mp4                     smoothstreaming
daud                    mpeg1system             sox
dirac                   mpeg1vcd                spdif
dnxhd                   mpeg1video              spx
dts                     mpeg2dvd                srt
dv                      mpeg2svcd               stream_segment
eac3                    mpeg2video              streamhash
f4v                     mpeg2vob                sup
ffmetadata              mpegts                  swf
fifo                    mpjpeg                  tee
fifo_test               mxf                     tg2
filmstrip               mxf_d10                 tgp
fits                    mxf_opatom              truehd
flac                    null                    tta
flv                     nut                     uncodedframecrc
framecrc                oga                     vc1
framehash               ogg                     vc1t
framemd5                ogv                     voc
g722                    oma                     w64
g723_1                  opus                    wav
g726                    pcm_alaw                webm
g726le                  pcm_f32be               webm_chunk
gif                     pcm_f32le               webm_dash_manifest
gsm                     pcm_f64be               webp
gxf                     pcm_f64le               webvtt
h261                    pcm_mulaw               wtv
h263                    pcm_s16be               wv
h264                    pcm_s16le               yuv4mpegpipe
hash                    pcm_s24be
```

### FFmpeg 解封装支持

FFmpeg 解封装是指将读入的容器格式拆解开，将里面压缩的音频流、视频流、字幕流、数据流等提取出来。

```
./configure --list-demuxers

aa                      ico                     pcm_mulaw
aac                     idcin                   pcm_s16be
aax                     idf                     pcm_s16le
ac3                     iff                     pcm_s24be
acm                     ifv                     pcm_s24le
act                     ilbc                    pcm_s32be
adf                     image2                  pcm_s32le
adp                     image2_alias_pix        pcm_s8
ads                     image2_brender_pix      pcm_u16be
adx                     image2pipe              pcm_u16le
aea                     image_bmp_pipe          pcm_u24be
afc                     image_dds_pipe          pcm_u24le
aiff                    image_dpx_pipe          pcm_u32be
aix                     image_exr_pipe          pcm_u32le
alp                     image_gif_pipe          pcm_u8
amr                     image_j2k_pipe          pcm_vidc
amrnb                   image_jpeg_pipe         pjs
amrwb                   image_jpegls_pipe       pmp
anm                     image_pam_pipe          pp_bnk
apc                     image_pbm_pipe          pva
ape                     image_pcx_pipe          pvf
apm                     image_pgm_pipe          qcp
apng                    image_pgmyuv_pipe       r3d
aptx                    image_pgx_pipe          rawvideo
aptx_hd                 image_photocd_pipe      realtext
aqtitle                 image_pictor_pipe       redspark
argo_asf                image_png_pipe          rl2
argo_brp                image_ppm_pipe          rm
asf                     image_psd_pipe          roq
asf_o                   image_qdraw_pipe        rpl
ass                     image_sgi_pipe          rsd
ast                     image_sunrast_pipe      rso
au                      image_svg_pipe          rtp
av1                     image_tiff_pipe         rtsp
avi                     image_webp_pipe         s337m
avisynth                image_xpm_pipe          sami
avr                     image_xwd_pipe          sap
avs                     ingenient               sbc
avs2                    ipmovie                 sbg
bethsoftvid             ircam                   scc
bfi                     iss                     sdp
bfstm                   iv8                     sdr2
bink                    ivf                     sds
bintext                 ivr                     sdx
bit                     jacosub                 segafilm
bmv                     jv                      ser
boa                     kux                     shorten
brstm                   kvag                    siff
c93                     libgme                  sln
caf                     libmodplug              smacker
cavsvideo               libopenmpt              smjpeg
cdg                     live_flv                smush
cdxl                    lmlm4                   sol
cine                    loas                    sox
codec2                  lrc                     spdif
codec2raw               luodat                  srt
concat                  lvf                     stl
dash                    lxf                     str
data                    m4v                     subviewer
daud                    matroska                subviewer1
dcstr                   mca                     sup
derf                    mcc                     svag
dfa                     mgsts                   svs
dhav                    microdvd                swf
dirac                   mjpeg                   tak
dnxhd                   mjpeg_2000              tedcaptions
dsf                     mlp                     thp
dsicin                  mlv                     threedostr
dss                     mm                      tiertexseq
dts                     mmf                     tmv
dtshd                   mods                    truehd
dv                      moflex                  tta
dvbsub                  mov                     tty
dvbtxt                  mp3                     txd
dxa                     mpc                     ty
ea                      mpc8                    v210
ea_cdata                mpegps                  v210x
eac3                    mpegts                  vag
epaf                    mpegtsraw               vapoursynth
ffmetadata              mpegvideo               vc1
filmstrip               mpjpeg                  vc1t
fits                    mpl2                    vividas
flac                    mpsub                   vivo
flic                    msf                     vmd
flv                     msnwc_tcp               vobsub
fourxm                  mtaf                    voc
frm                     mtv                     vpk
fsb                     musx                    vplayer
fwse                    mv                      vqf
g722                    mvi                     w64
g723_1                  mxf                     wav
g726                    mxg                     wc3
g726le                  nc                      webm_dash_manifest
g729                    nistsphere              webvtt
gdv                     nsp                     wsaud
genh                    nsv                     wsd
gif                     nut                     wsvqa
gsm                     nuv                     wtv
gxf                     obu                     wv
h261                    ogg                     wve
h263                    oma                     xa
h264                    paf                     xbin
hca                     pcm_alaw                xmv
hcom                    pcm_f32be               xvag
hevc                    pcm_f32le               xwma
hls                     pcm_f64be               yop
hnm                     pcm_f64le               yuv4mpegpipe
```

### FFmpeg 的通信协议支持

FFmpeg 除了支持常见的本地多媒体，还指出网络流媒体处理。

HTTP、HTTPS、HLS（m3u8）、RTMP、RTP 等。

```
./configure --list-protocols

async                   libamqp                 rtmpe
bluray                  librtmp                 rtmps
cache                   librtmpe                rtmpt
concat                  librtmps                rtmpte
crypto                  librtmpt                rtmpts
data                    librtmpte               rtp
ffrtmpcrypt             libsmbclient            sctp
ffrtmphttp              libsrt                  srtp
file                    libssh                  subfile
ftp                     libzmq                  tcp
gopher                  md5                     tee
hls                     mmsh                    tls
http                    mmst                    udp
httpproxy               pipe                    udplite
https                   prompeg                 unix
icecast                 rtmp
```


## Help Command

```
Help options:
  --help                   print this message
  --quiet                  Suppress showing informative output
  --list-decoders          show all available decoders
  --list-encoders          show all available encoders
  --list-hwaccels          show all available hardware accelerators
  --list-demuxers          show all available demuxers
  --list-muxers            show all available muxers
  --list-parsers           show all available parsers
  --list-protocols         show all available protocols
  --list-bsfs              show all available bitstream filters
  --list-indevs            show all available input devices
  --list-outdevs           show all available output devices
  --list-filters           show all available filters

Standard options:
  --logfile=FILE           log tests and output to FILE [ffbuild/config.log]
  --disable-logging        do not log configure debug information
  --fatal-warnings         fail if any configure warning is generated
  --prefix=PREFIX          install in PREFIX [/usr/local]
  --bindir=DIR             install binaries in DIR [PREFIX/bin]
  --datadir=DIR            install data files in DIR [PREFIX/share/ffmpeg]
  --docdir=DIR             install documentation in DIR [PREFIX/share/doc/ffmpeg]
  --libdir=DIR             install libs in DIR [PREFIX/lib]
  --shlibdir=DIR           install shared libs in DIR [LIBDIR]
  --incdir=DIR             install includes in DIR [PREFIX/include]
  --mandir=DIR             install man page in DIR [PREFIX/share/man]
  --pkgconfigdir=DIR       install pkg-config files in DIR [LIBDIR/pkgconfig]
  --enable-rpath           use rpath to allow installing libraries in paths
                           not part of the dynamic linker search path
                           use rpath when linking programs (USE WITH CARE)
  --install-name-dir=DIR   Darwin directory name for installed targets

Licensing options:
  --enable-gpl             allow use of GPL code, the resulting libs
                           and binaries will be under GPL [no]
  --enable-version3        upgrade (L)GPL to version 3 [no]
  --enable-nonfree         allow use of nonfree code, the resulting libs
                           and binaries will be unredistributable [no]

Configuration options:
  --disable-static         do not build static libraries [no]
  --enable-shared          build shared libraries [no]
  --enable-small           optimize for size instead of speed
  --disable-runtime-cpudetect disable detecting CPU capabilities at runtime (smaller binary)
  --enable-gray            enable full grayscale support (slower color)
  --disable-swscale-alpha  disable alpha channel support in swscale
  --disable-all            disable building components, libraries and programs
  --disable-autodetect     disable automatically detected external libraries [no]

Program options:
  --disable-programs       do not build command line programs
  --disable-ffmpeg         disable ffmpeg build
  --disable-ffplay         disable ffplay build
  --disable-ffprobe        disable ffprobe build

Documentation options:
  --disable-doc            do not build documentation
  --disable-htmlpages      do not build HTML documentation pages
  --disable-manpages       do not build man documentation pages
  --disable-podpages       do not build POD documentation pages
  --disable-txtpages       do not build text documentation pages

Component options:
  --disable-avdevice       disable libavdevice build
  --disable-avcodec        disable libavcodec build
  --disable-avformat       disable libavformat build
  --disable-swresample     disable libswresample build
  --disable-swscale        disable libswscale build
  --disable-postproc       disable libpostproc build
  --disable-avfilter       disable libavfilter build
  --enable-avresample      enable libavresample build (deprecated) [no]
  --disable-pthreads       disable pthreads [autodetect]
  --disable-w32threads     disable Win32 threads [autodetect]
  --disable-os2threads     disable OS/2 threads [autodetect]
  --disable-network        disable network support [no]
  --disable-dct            disable DCT code
  --disable-dwt            disable DWT code
  --disable-error-resilience disable error resilience code
  --disable-lsp            disable LSP code
  --disable-lzo            disable LZO decoder code
  --disable-mdct           disable MDCT code
  --disable-rdft           disable RDFT code
  --disable-fft            disable FFT code
  --disable-faan           disable floating point AAN (I)DCT code
  --disable-pixelutils     disable pixel utils in libavutil

Individual component options:
  --disable-everything     disable all components listed below
  --disable-encoder=NAME   disable encoder NAME
  --enable-encoder=NAME    enable encoder NAME
  --disable-encoders       disable all encoders
  --disable-decoder=NAME   disable decoder NAME
  --enable-decoder=NAME    enable decoder NAME
  --disable-decoders       disable all decoders
  --disable-hwaccel=NAME   disable hwaccel NAME
  --enable-hwaccel=NAME    enable hwaccel NAME
  --disable-hwaccels       disable all hwaccels
  --disable-muxer=NAME     disable muxer NAME
  --enable-muxer=NAME      enable muxer NAME
  --disable-muxers         disable all muxers
  --disable-demuxer=NAME   disable demuxer NAME
  --enable-demuxer=NAME    enable demuxer NAME
  --disable-demuxers       disable all demuxers
  --enable-parser=NAME     enable parser NAME
  --disable-parser=NAME    disable parser NAME
  --disable-parsers        disable all parsers
  --enable-bsf=NAME        enable bitstream filter NAME
  --disable-bsf=NAME       disable bitstream filter NAME
  --disable-bsfs           disable all bitstream filters
  --enable-protocol=NAME   enable protocol NAME
  --disable-protocol=NAME  disable protocol NAME
  --disable-protocols      disable all protocols
  --enable-indev=NAME      enable input device NAME
  --disable-indev=NAME     disable input device NAME
  --disable-indevs         disable input devices
  --enable-outdev=NAME     enable output device NAME
  --disable-outdev=NAME    disable output device NAME
  --disable-outdevs        disable output devices
  --disable-devices        disable all devices
  --enable-filter=NAME     enable filter NAME
  --disable-filter=NAME    disable filter NAME
  --disable-filters        disable all filters

External library support:

  Using any of the following switches will allow FFmpeg to link to the
  corresponding external library. All the components depending on that library
  will become enabled, if all their other dependencies are met and they are not
  explicitly disabled. E.g. --enable-libwavpack will enable linking to
  libwavpack and allow the libwavpack encoder to be built, unless it is
  specifically disabled with --disable-encoder=libwavpack.

  Note that only the system libraries are auto-detected. All the other external
  libraries must be explicitly enabled.

  Also note that the following help text describes the purpose of the libraries
  themselves, not all their features will necessarily be usable by FFmpeg.

  --disable-alsa           disable ALSA support [autodetect]
  --disable-appkit         disable Apple AppKit framework [autodetect]
  --disable-avfoundation   disable Apple AVFoundation framework [autodetect]
  --enable-avisynth        enable reading of AviSynth script files [no]
  --disable-bzlib          disable bzlib [autodetect]
  --disable-coreimage      disable Apple CoreImage framework [autodetect]
  --enable-chromaprint     enable audio fingerprinting with chromaprint [no]
  --enable-frei0r          enable frei0r video filtering [no]
  --enable-gcrypt          enable gcrypt, needed for rtmp(t)e support
                           if openssl, librtmp or gmp is not used [no]
  --enable-gmp             enable gmp, needed for rtmp(t)e support
                           if openssl or librtmp is not used [no]
  --enable-gnutls          enable gnutls, needed for https support
                           if openssl, libtls or mbedtls is not used [no]
  --disable-iconv          disable iconv [autodetect]
  --enable-jni             enable JNI support [no]
  --enable-ladspa          enable LADSPA audio filtering [no]
  --enable-libaom          enable AV1 video encoding/decoding via libaom [no]
  --enable-libaribb24      enable ARIB text and caption decoding via libaribb24 [no]
  --enable-libass          enable libass subtitles rendering,
                           needed for subtitles and ass filter [no]
  --enable-libbluray       enable BluRay reading using libbluray [no]
  --enable-libbs2b         enable bs2b DSP library [no]
  --enable-libcaca         enable textual display using libcaca [no]
  --enable-libcelt         enable CELT decoding via libcelt [no]
  --enable-libcdio         enable audio CD grabbing with libcdio [no]
  --enable-libcodec2       enable codec2 en/decoding using libcodec2 [no]
  --enable-libdav1d        enable AV1 decoding via libdav1d [no]
  --enable-libdavs2        enable AVS2 decoding via libdavs2 [no]
  --enable-libdc1394       enable IIDC-1394 grabbing using libdc1394
                           and libraw1394 [no]
  --enable-libfdk-aac      enable AAC de/encoding via libfdk-aac [no]
  --enable-libflite        enable flite (voice synthesis) support via libflite [no]
  --enable-libfontconfig   enable libfontconfig, useful for drawtext filter [no]
  --enable-libfreetype     enable libfreetype, needed for drawtext filter [no]
  --enable-libfribidi      enable libfribidi, improves drawtext filter [no]
  --enable-libglslang      enable GLSL->SPIRV compilation via libglslang [no]
  --enable-libgme          enable Game Music Emu via libgme [no]
  --enable-libgsm          enable GSM de/encoding via libgsm [no]
  --enable-libiec61883     enable iec61883 via libiec61883 [no]
  --enable-libilbc         enable iLBC de/encoding via libilbc [no]
  --enable-libjack         enable JACK audio sound server [no]
  --enable-libklvanc       enable Kernel Labs VANC processing [no]
  --enable-libkvazaar      enable HEVC encoding via libkvazaar [no]
  --enable-liblensfun      enable lensfun lens correction [no]
  --enable-libmodplug      enable ModPlug via libmodplug [no]
  --enable-libmp3lame      enable MP3 encoding via libmp3lame [no]
  --enable-libopencore-amrnb enable AMR-NB de/encoding via libopencore-amrnb [no]
  --enable-libopencore-amrwb enable AMR-WB decoding via libopencore-amrwb [no]
  --enable-libopencv       enable video filtering via libopencv [no]
  --enable-libopenh264     enable H.264 encoding via OpenH264 [no]
  --enable-libopenjpeg     enable JPEG 2000 de/encoding via OpenJPEG [no]
  --enable-libopenmpt      enable decoding tracked files via libopenmpt [no]
  --enable-libopenvino     enable OpenVINO as a DNN module backend
                           for DNN based filters like dnn_processing [no]
  --enable-libopus         enable Opus de/encoding via libopus [no]
  --enable-libpulse        enable Pulseaudio input via libpulse [no]
  --enable-librabbitmq     enable RabbitMQ library [no]
  --enable-librav1e        enable AV1 encoding via rav1e [no]
  --enable-librsvg         enable SVG rasterization via librsvg [no]
  --enable-librubberband   enable rubberband needed for rubberband filter [no]
  --enable-librtmp         enable RTMP[E] support via librtmp [no]
  --enable-libshine        enable fixed-point MP3 encoding via libshine [no]
  --enable-libsmbclient    enable Samba protocol via libsmbclient [no]
  --enable-libsnappy       enable Snappy compression, needed for hap encoding [no]
  --enable-libsoxr         enable Include libsoxr resampling [no]
  --enable-libspeex        enable Speex de/encoding via libspeex [no]
  --enable-libsrt          enable Haivision SRT protocol via libsrt [no]
  --enable-libssh          enable SFTP protocol via libssh [no]
  --enable-libsvtav1       enable AV1 encoding via SVT [no]
  --enable-libtensorflow   enable TensorFlow as a DNN module backend
                           for DNN based filters like sr [no]
  --enable-libtesseract    enable Tesseract, needed for ocr filter [no]
  --enable-libtheora       enable Theora encoding via libtheora [no]
  --enable-libtls          enable LibreSSL (via libtls), needed for https support
                           if openssl, gnutls or mbedtls is not used [no]
  --enable-libtwolame      enable MP2 encoding via libtwolame [no]
  --enable-libv4l2         enable libv4l2/v4l-utils [no]
  --enable-libvidstab      enable video stabilization using vid.stab [no]
  --enable-libvmaf         enable vmaf filter via libvmaf [no]
  --enable-libvo-amrwbenc  enable AMR-WB encoding via libvo-amrwbenc [no]
  --enable-libvorbis       enable Vorbis en/decoding via libvorbis,
                           native implementation exists [no]
  --enable-libvpx          enable VP8 and VP9 de/encoding via libvpx [no]
  --enable-libwavpack      enable wavpack encoding via libwavpack [no]
  --enable-libwebp         enable WebP encoding via libwebp [no]
  --enable-libx264         enable H.264 encoding via x264 [no]
  --enable-libx265         enable HEVC encoding via x265 [no]
  --enable-libxavs         enable AVS encoding via xavs [no]
  --enable-libxavs2        enable AVS2 encoding via xavs2 [no]
  --enable-libxcb          enable X11 grabbing using XCB [autodetect]
  --enable-libxcb-shm      enable X11 grabbing shm communication [autodetect]
  --enable-libxcb-xfixes   enable X11 grabbing mouse rendering [autodetect]
  --enable-libxcb-shape    enable X11 grabbing shape rendering [autodetect]
  --enable-libxvid         enable Xvid encoding via xvidcore,
                           native MPEG-4/Xvid encoder exists [no]
  --enable-libxml2         enable XML parsing using the C library libxml2, needed
                           for dash demuxing support [no]
  --enable-libzimg         enable z.lib, needed for zscale filter [no]
  --enable-libzmq          enable message passing via libzmq [no]
  --enable-libzvbi         enable teletext support via libzvbi [no]
  --enable-lv2             enable LV2 audio filtering [no]
  --disable-lzma           disable lzma [autodetect]
  --enable-decklink        enable Blackmagic DeckLink I/O support [no]
  --enable-mbedtls         enable mbedTLS, needed for https support
                           if openssl, gnutls or libtls is not used [no]
  --enable-mediacodec      enable Android MediaCodec support [no]
  --enable-mediafoundation enable encoding via MediaFoundation [auto]
  --enable-libmysofa       enable libmysofa, needed for sofalizer filter [no]
  --enable-openal          enable OpenAL 1.1 capture support [no]
  --enable-opencl          enable OpenCL processing [no]
  --enable-opengl          enable OpenGL rendering [no]
  --enable-openssl         enable openssl, needed for https support
                           if gnutls, libtls or mbedtls is not used [no]
  --enable-pocketsphinx    enable PocketSphinx, needed for asr filter [no]
  --disable-sndio          disable sndio support [autodetect]
  --disable-schannel       disable SChannel SSP, needed for TLS support on
                           Windows if openssl and gnutls are not used [autodetect]
  --disable-sdl2           disable sdl2 [autodetect]
  --disable-securetransport disable Secure Transport, needed for TLS support
                           on OSX if openssl and gnutls are not used [autodetect]
  --enable-vapoursynth     enable VapourSynth demuxer [no]
  --enable-vulkan          enable Vulkan code [no]
  --disable-xlib           disable xlib [autodetect]
  --disable-zlib           disable zlib [autodetect]

  The following libraries provide various hardware acceleration features:
  --disable-amf            disable AMF video encoding code [autodetect]
  --disable-audiotoolbox   disable Apple AudioToolbox code [autodetect]
  --enable-cuda-nvcc       enable Nvidia CUDA compiler [no]
  --disable-cuda-llvm      disable CUDA compilation using clang [autodetect]
  --disable-cuvid          disable Nvidia CUVID support [autodetect]
  --disable-d3d11va        disable Microsoft Direct3D 11 video acceleration code [autodetect]
  --disable-dxva2          disable Microsoft DirectX 9 video acceleration code [autodetect]
  --disable-ffnvcodec      disable dynamically linked Nvidia code [autodetect]
  --enable-libdrm          enable DRM code (Linux) [no]
  --enable-libmfx          enable Intel MediaSDK (AKA Quick Sync Video) code via libmfx [no]
  --enable-libnpp          enable Nvidia Performance Primitives-based code [no]
  --enable-mmal            enable Broadcom Multi-Media Abstraction Layer (Raspberry Pi) via MMAL [no]
  --disable-nvdec          disable Nvidia video decoding acceleration (via hwaccel) [autodetect]
  --disable-nvenc          disable Nvidia video encoding code [autodetect]
  --enable-omx             enable OpenMAX IL code [no]
  --enable-omx-rpi         enable OpenMAX IL code for Raspberry Pi [no]
  --enable-rkmpp           enable Rockchip Media Process Platform code [no]
  --disable-v4l2-m2m       disable V4L2 mem2mem code [autodetect]
  --disable-vaapi          disable Video Acceleration API (mainly Unix/Intel) code [autodetect]
  --disable-vdpau          disable Nvidia Video Decode and Presentation API for Unix code [autodetect]
  --disable-videotoolbox   disable VideoToolbox code [autodetect]

Toolchain options:
  --arch=ARCH              select architecture []
  --cpu=CPU                select the minimum required CPU (affects
                           instruction selection, may crash on older CPUs)
  --cross-prefix=PREFIX    use PREFIX for compilation tools []
  --progs-suffix=SUFFIX    program name suffix []
  --enable-cross-compile   assume a cross-compiler is used
  --sysroot=PATH           root of cross-build tree
  --sysinclude=PATH        location of cross-build system headers
  --target-os=OS           compiler targets OS []
  --target-exec=CMD        command to run executables on target
  --target-path=DIR        path to view of build directory on target
  --target-samples=DIR     path to samples directory on target
  --tempprefix=PATH        force fixed dir/prefix instead of mktemp for checks
  --toolchain=NAME         set tool defaults according to NAME
                           (gcc-asan, clang-asan, gcc-msan, clang-msan,
                           gcc-tsan, clang-tsan, gcc-usan, clang-usan,
                           valgrind-massif, valgrind-memcheck,
                           msvc, icl, gcov, llvm-cov, hardened)
  --nm=NM                  use nm tool NM [nm -g]
  --ar=AR                  use archive tool AR [ar]
  --as=AS                  use assembler AS []
  --ln_s=LN_S              use symbolic link tool LN_S [ln -s -f]
  --strip=STRIP            use strip tool STRIP [strip]
  --windres=WINDRES        use windows resource compiler WINDRES [windres]
  --x86asmexe=EXE          use nasm-compatible assembler EXE [nasm]
  --cc=CC                  use C compiler CC [gcc]
  --cxx=CXX                use C compiler CXX [g++]
  --objcc=OCC              use ObjC compiler OCC [gcc]
  --dep-cc=DEPCC           use dependency generator DEPCC [gcc]
  --nvcc=NVCC              use Nvidia CUDA compiler NVCC or clang []
  --ld=LD                  use linker LD []
  --pkg-config=PKGCONFIG   use pkg-config tool PKGCONFIG [pkg-config]
  --pkg-config-flags=FLAGS pass additional flags to pkgconf []
  --ranlib=RANLIB          use ranlib RANLIB [ranlib]
  --doxygen=DOXYGEN        use DOXYGEN to generate API doc [doxygen]
  --host-cc=HOSTCC         use host C compiler HOSTCC
  --host-cflags=HCFLAGS    use HCFLAGS when compiling for host
  --host-cppflags=HCPPFLAGS use HCPPFLAGS when compiling for host
  --host-ld=HOSTLD         use host linker HOSTLD
  --host-ldflags=HLDFLAGS  use HLDFLAGS when linking for host
  --host-extralibs=HLIBS   use libs HLIBS when linking for host
  --host-os=OS             compiler host OS []
  --extra-cflags=ECFLAGS   add ECFLAGS to CFLAGS []
  --extra-cxxflags=ECFLAGS add ECFLAGS to CXXFLAGS []
  --extra-objcflags=FLAGS  add FLAGS to OBJCFLAGS []
  --extra-ldflags=ELDFLAGS add ELDFLAGS to LDFLAGS []
  --extra-ldexeflags=ELDFLAGS add ELDFLAGS to LDEXEFLAGS []
  --extra-ldsoflags=ELDFLAGS add ELDFLAGS to LDSOFLAGS []
  --extra-libs=ELIBS       add ELIBS []
  --extra-version=STRING   version string suffix []
  --optflags=OPTFLAGS      override optimization-related compiler flags
  --nvccflags=NVCCFLAGS    override nvcc flags []
  --build-suffix=SUFFIX    library name suffix []
  --enable-pic             build position-independent code
  --enable-thumb           compile for Thumb instruction set
  --enable-lto             use link-time optimization
  --env="ENV=override"     override the environment variables

Advanced options (experts only):
  --malloc-prefix=PREFIX   prefix malloc and related names with PREFIX
  --custom-allocator=NAME  use a supported custom allocator
  --disable-symver         disable symbol versioning
  --enable-hardcoded-tables use hardcoded tables instead of runtime generation
  --disable-safe-bitstream-reader
                           disable buffer boundary checking in bitreaders
                           (faster, but may crash)
  --sws-max-filter-size=N  the max filter size swscale uses [256]

Optimization options (experts only):
  --disable-asm            disable all assembly optimizations
  --disable-altivec        disable AltiVec optimizations
  --disable-vsx            disable VSX optimizations
  --disable-power8         disable POWER8 optimizations
  --disable-amd3dnow       disable 3DNow! optimizations
  --disable-amd3dnowext    disable 3DNow! extended optimizations
  --disable-mmx            disable MMX optimizations
  --disable-mmxext         disable MMXEXT optimizations
  --disable-sse            disable SSE optimizations
  --disable-sse2           disable SSE2 optimizations
  --disable-sse3           disable SSE3 optimizations
  --disable-ssse3          disable SSSE3 optimizations
  --disable-sse4           disable SSE4 optimizations
  --disable-sse42          disable SSE4.2 optimizations
  --disable-avx            disable AVX optimizations
  --disable-xop            disable XOP optimizations
  --disable-fma3           disable FMA3 optimizations
  --disable-fma4           disable FMA4 optimizations
  --disable-avx2           disable AVX2 optimizations
  --disable-avx512         disable AVX-512 optimizations
  --disable-aesni          disable AESNI optimizations
  --disable-armv5te        disable armv5te optimizations
  --disable-armv6          disable armv6 optimizations
  --disable-armv6t2        disable armv6t2 optimizations
  --disable-vfp            disable VFP optimizations
  --disable-neon           disable NEON optimizations
  --disable-inline-asm     disable use of inline assembly
  --disable-x86asm         disable use of standalone x86 assembly
  --disable-mipsdsp        disable MIPS DSP ASE R1 optimizations
  --disable-mipsdspr2      disable MIPS DSP ASE R2 optimizations
  --disable-msa            disable MSA optimizations
  --disable-msa2           disable MSA2 optimizations
  --disable-mipsfpu        disable floating point MIPS optimizations
  --disable-mmi            disable Loongson SIMD optimizations
  --disable-fast-unaligned consider unaligned accesses slow

Developer options (useful when working on FFmpeg itself):
  --disable-debug          disable debugging symbols
  --enable-debug=LEVEL     set the debug level []
  --disable-optimizations  disable compiler optimizations
  --enable-extra-warnings  enable more compiler warnings
  --disable-stripping      disable stripping of executables and shared libraries
  --assert-level=level     0(default), 1 or 2, amount of assertion testing,
                           2 causes a slowdown at runtime.
  --enable-memory-poisoning fill heap uninitialized allocated space with arbitrary data
  --valgrind=VALGRIND      run "make fate" tests through valgrind to detect memory
                           leaks and errors, using the specified valgrind binary.
                           Cannot be combined with --target-exec
  --enable-ftrapv          Trap arithmetic overflows
  --samples=PATH           location of test samples for FATE, if not set use
                           $FATE_SAMPLES at make invocation time.
  --enable-neon-clobber-test check NEON registers for clobbering (should be
                           used only for debugging purposes)
  --enable-xmm-clobber-test check XMM registers for clobbering (Win64-only;
                           should be used only for debugging purposes)
  --enable-random          randomly enable/disable components
  --disable-random
  --enable-random=LIST     randomly enable/disable specific components or
  --disable-random=LIST    component groups. LIST is a comma-separated list
                           of NAME[:PROB] entries where NAME is a component
                           (group) and PROB the probability associated with
                           NAME (default 0.5).
  --random-seed=VALUE      seed value for --enable/disable-random
  --disable-valgrind-backtrace do not print a backtrace under Valgrind
                           (only applies to --disable-optimizations builds)
  --enable-ossfuzz         Enable building fuzzer tool
  --libfuzzer=PATH         path to libfuzzer
  --ignore-tests=TESTS     comma-separated list (without "fate-" prefix
                           in the name) of tests whose result is ignored
  --enable-linux-perf      enable Linux Performance Monitor API
  --disable-large-tests    disable tests that use a large amount of memory

NOTE: Object files are built at the place where configure is launched.
localhost:FFmpeg-master-920 july$ 

```