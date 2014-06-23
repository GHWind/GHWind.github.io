---
layout: post
title: "AVI格式详解"
date: 2014-04-17 16:48:03 +0800
comments: true
categories: [file format] 
---

AVI（Audio Video Interleaved的缩写）是一种RIFF（Resource Interchange File Format的缩写）文件格式，多用于音视频捕捉、编辑、回放等应用程序中。通常情况下，一个AVI文件可以包含多个不同类型的媒体流（典型的情况下有一个音频流和一个视频流），不过含有单一音频流或单一视频流的AVI文件也是合法的。AVI可以算是Windows操作系统上最基本的、也是最常用的一种媒体文件格式。

一、RIFF 文件格式

{%codeblock%}
RIFF    文件大小    文件类型    数据...
     |        |           |          | 
   4Bytes  4Bytes       4Bytes      4Bytes
{%endcodeblock%}

文件大小: 实际数据长度 + 文件类型(4Bytes) , 也就是说,文件大小的值不包括 ‘RIFF’ 和 ‘文件大小’ 域本身的大小。

RIFF文件的实际数据中,通常使用了列表(List) 和 块(Chunk)的形式来组织。列表可以嵌套子列表和块。

二、列表的结构

{%codeblock%}
'LIST'   列表大小  列表类型  列表数据 
     |        |        |          |
    4Bytes  4Bytes    4Bytes    4Bytes
{%endcodeblock%}

列表大小: 实际的列表数据长度 + (ListType 4Bytes), 也就是说, 列表大小不包括’List’领和listSize域本身的大小。

块结构:

{%codeblock%}
ckID   ckSize   ckData
    |       |        |
   4Bytes  4Bytes   4Bytes
{%endcodeblock%}

块大小: 实际的块数据长度,而不包括ckID域和ckSize域本身的大小。

整个AVI结构: RIFF + List1(描述媒体流格式) + List2(保留媒体流数据) + 可选的索引块。

{%codeblock%}
RIFF (‘AVI ’
      LIST (‘hdrl’
            ‘avih’(主AVI信息头数据)
            LIST (‘strl’
                  ‘strh’ (流的头信息数据)
                  ‘strf’ (流的格式信息数据)
                  [‘strd’ (可选的额外的头信息数据) ]
                  [‘strn’ (可选的流的名字) ]
                  ...
                 )
             ...
           )
      LIST (‘movi’
            { SubChunk | LIST (‘rec ’
                              SubChunk1
                              SubChunk2
                              ...
                             )
               ...
            }
            ...
           )
      [‘idx1’ (可选的AVI索引块数据) ]
     )
{%endcodeblock%}

‘hdrl’ 列表: 用于描述AVI文件中各个流的格式信息(AVI文件中的每一路媒体数据都成为一个流)。 嵌套一系列块和子列表。 首先是 ‘avih’ 块 (记录AVI文件的全局信息，比如流的数量、视频图像的宽和高等)

{%codeblock%}
typedef struct _avimainheader {
    char FORCC_ID[4];         // 必须为 'avih'
    DWORD  cb;                // 本数据结构的大小，不包括最初的8个字节（fcc和cb两个域）
    DWORD  dwMicroSecPerFrame;            // 视频帧间隔时间（以毫秒为单位）
    DWORD  dwMaxBytesPerSec;      // 这个AVI文件的最大数据率
    DWORD  dwPaddingGranularity;  // 数据填充的粒度
    DWORD  dwFlags;           // AVI文件的全局标记，比如是否含有索引块等
    DWORD  dwTotalFrames;     // 总帧数
    DWORD  dwInitialFrames;       // 为交互格式指定初始帧数（非交互格式应该指定为0）
    DWORD  dwStreams;         // 本文件包含的流的个数
    DWORD  dwSuggestedBufferSize; // 建议读取本文件的缓存大小（应能容纳最大的块）
    DWORD  dwWidth;           // 视频图像的宽（以像素为单位）
    DWORD  dwHeight;          // 视频图像的高（以像素为单位）
    DWORD  dwReserved[4];     //  保留
} AVIMAINHEADER;
{%endcodeblock%}
