---
title: 媒體編碼器標準格式和轉碼器 - Azure
description: 本主題提供媒體編碼器標準格式和轉碼器的概觀。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 3bc42bf5b83f9ff983c3d8d88a9a496cc395096c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992580"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard 格式和轉碼器

本文件包含您可以藉由 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 使用的最常見匯入和匯出檔案格式列出的清單。 如需如何使用 **StandardEncoderPreset** 建立自訂預設設定的詳細資訊，請參閱[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)。

## <a name="input-containerfile-formats"></a>輸入容器/檔案格式

| 檔案格式 (副檔名) | 支援 |
| --- | --- | --- | --- |
| FLV (使用 H.264 和 AAC 轉碼器) (.flv) |yes |
| MXF    (.mxf) |yes |
| GXF    (.gxf) |yes |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |yes |
| Windows Media 視訊 (WMV)/ASF (.wmv、.asf) |yes |
| AVI (未壓縮 8 位元/10 位元) (.avi) |yes |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |yes |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |yes |
| Matroska/WebM (.mkv) |yes |
| WAVE/WAV (.wav) |yes |
| QuickTime (.mov) |yes |

> [!NOTE]
> 以上是較常見的副檔名清單。 Media Encoder Standard 支援許多其他副檔名 (例如，.m2ts、.mpeg2video 和 .qt)。 如果您嘗試將檔案編碼，但收到格式不支援的相關錯誤訊息，請在[這裡](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)提供意見反應。
> 
> 

### <a name="audio-formats-in-input-containers"></a>輸入容器中的音訊格式
Media Encoder Standard 支援在輸入容器中帶有下列音訊格式：

* MXF、GXF 和 QuickTime 檔案，其具有交錯立體聲或 5.1 範例的音訊音軌

或

* MXF、GXF 及 QuickTime 檔案，其中該音訊當做個別的 PCM 曲目攜帶，但可從檔案中繼資料推算通道對應 (立體聲或 5.1)

## <a name="input-video-codecs"></a>輸入視訊轉碼器
| 輸入視訊轉碼器 | 支援 |
| --- | --- | --- | --- |
| AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 |
| Avid DNxHD (使用 MXF) |yes |
| DVCPro/DVCProHD (使用 MXF) |yes |
| 數位視訊 (DV) (使用 AVI 檔案) |yes |
| JPEG 2000 |yes |
| MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種) |最高 422 設定檔 |
| MPEG-1 |yes |
| VC-1/WMV9 |yes |
| Canopus HQ/HQX |否 |
| Mpeg-4 第 2 部分 |yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |yes |
| YUV420 未壓縮或夾層 |yes |
| Apple ProRes 422 |yes |
| Apple ProRes 422 LT |yes |
| Apple ProRes 422 HQ |yes |
| Apple ProRes Proxy |yes |
| Apple ProRes 4444 |yes |
| Apple ProRes 4444 XQ |yes |
| HEVC/H.265| 主要設定檔|

## <a name="input-audio-codecs"></a>輸入音訊轉碼器
| 輸入音訊轉碼器 | 支援 |
| --- | --- | --- | --- |
| AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1) |yes |
| MPEG Layer 2 |yes |
| MP3 (MPEG-1 音訊層 3) |yes |
| Windows Media 音訊 |yes |
| WAV/PCM |yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |yes |
| AMR (可變多速率) |yes |
| AES (SMPTE 331M 和 302M，AES3-2003) |否 |
| Dolby® E |否 |
| Dolby® Digital (AC3) |否 |
| Dolby® Digital Plus (E-AC3) |否 |

## <a name="output-formats-and-codecs"></a>輸出格式和轉碼器
下表會列出支援匯出的轉碼器和檔案格式清單。

| 檔案格式 | 視訊轉碼器 | 音訊轉碼器 |
| --- | --- | --- |
| MP4  <br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>後續步驟

[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)
