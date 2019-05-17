---
title: 標準編碼器格式和轉碼器-Azure
description: 本主題提供標準編碼器格式和轉碼器的的概觀。
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
ms.openlocfilehash: 730ff68e70999307417eea276761d56f4a44046a
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520019"
---
# <a name="standard-encoder-formats-and-codecs"></a>標準編碼器格式和轉碼器

本文件包含您可以藉由 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 使用的最常見匯入和匯出檔案格式列出的清單。 如需如何使用 **StandardEncoderPreset** 建立自訂預設設定的詳細資訊，請參閱[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)。

## <a name="input-containerfile-formats"></a>輸入容器/檔案格式

| 檔案格式 (副檔名) | 支援 |
| --- | --- |
| FLV (使用 H.264 和 AAC 轉碼器) (.flv) |有 |
| MXF    (.mxf) |有 |
| GXF    (.gxf) |有 |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |有 |
| Windows Media 視訊 (WMV)/ASF (.wmv、.asf) |有 |
| AVI (未壓縮 8 位元/10 位元) (.avi) |有 |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |有 |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |有 |
| Matroska/WebM (.mkv) |有 |
| WAVE/WAV (.wav) |有 |
| QuickTime (.mov) |有 |

> [!NOTE]
> 
> 

### <a name="audio-formats-in-input-containers"></a>輸入容器中的音訊格式

標準編碼器支援帶有下列音訊格式輸入容器中：

* MXF、GXF 和 QuickTime 檔案，其具有交錯立體聲或 5.1 範例的音訊音軌

或

* MXF、GXF 及 QuickTime 檔案，其中該音訊當做個別的 PCM 曲目攜帶，但可從檔案中繼資料推算通道對應 (立體聲或 5.1)

## <a name="input-video-codecs"></a>輸入視訊轉碼器
| 輸入視訊轉碼器 | 支援 |
| --- | --- |
| AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 |
| Avid DNxHD（MXF 格式） |有 |
| DVCPro/DVCProHD（MXF 格式） |有 |
| 數位視訊 (DV) (使用 AVI 檔案) |有 |
| JPEG 2000 |有 |
| MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種) |最高 422 設定檔 |
| MPEG-1 |有 |
| VC-1/WMV9 |有 |
| Canopus HQ/HQX |無 |
| Mpeg-4 第 2 部分 |有 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |有 |
| YUV420 未壓縮或夾層 |有 |
| Apple ProRes 422 |有 |
| Apple ProRes 422 LT |有 |
| Apple ProRes 422 HQ |有 |
| Apple ProRes Proxy |有 |
| Apple ProRes 4444 |有 |
| Apple ProRes 4444 XQ |有 |
| HEVC/H.265| 主要設定檔|

## <a name="input-audio-codecs"></a>輸入音訊轉碼器
| 輸入音訊轉碼器 | 支援 |
| --- | --- |
| AAC（AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1） |有 |
| MPEG Layer 2 |有 |
| MP3 (MPEG-1 Audio Layer 3) |有 |
| Windows Media 音訊 |有 |
| WAV/PCM |有 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |有 |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |有 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |有 |
| AMR (可變多速率) |有 |
| AES (SMPTE 331M 和 302M，AES3-2003) |無 |
| Dolby® E |無 |
| Dolby® Digital (AC3) |無 |
| Dolby® Digital Plus (E-AC3) |無 |

## <a name="output-formats-and-codecs"></a>輸出格式和轉碼器
下表會列出支援匯出的轉碼器和檔案格式清單。

| 檔案格式 | 視訊轉碼器 | 音频编解码器 |
| --- | --- | --- |
| MP4  <br/><br/>(包括多位元速率 MP4 容器) |H.264（High、Main 和 Baseline Profile） |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (高、主要和基準設定檔) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>後續步驟

[使用自訂預設建立轉換](customize-encoder-presets-how-to.md)
