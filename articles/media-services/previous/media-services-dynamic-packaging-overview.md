---
title: Azure 媒體服務動態封裝概觀 | Microsoft Docs
description: 本主题概述动态打包。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: e27b52c96f524f25aab18f45cf72c43884b7640d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227156"
---
# <a name="dynamic-packaging"></a>動態封裝

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [第 3 版](../latest/dynamic-packaging-overview.md)
> * [第 2 版](media-services-dynamic-packaging-overview.md)

Microsoft Azure Media Services 可用來針對數種用戶端技術 (例如 iOS、XBOX、Silverlight、Windows 8) 提供許多媒體來源檔案格式、媒體串流格式和內容保護格式。 這些用戶端各自使用不同的通訊協定，例如 iOS 需要 HTTP 即時串流 (HLS) V4 格式，而 Silverlight 與 Xbox 需要 Smooth Streaming。 如果您有一組自動調整位元速率 (多位元速率) MP4 (ISO Base Media 14496-12) 檔案或一組自動調整位元速率 Smooth Streaming 檔案，想要傳遞給了解 MPEG DASH、HLS 或 Smooth Streaming 的用戶端，應該利用媒體服務動態封裝。

使用动态打包，只需要创建一个包含一组自适应比特率 MP4 文件或自适应比特率平滑流文件的资产。 然後隨選資料流處理伺服器會根據資訊清單或片段要求中的指定格式，確保您以自己選擇的通訊協定接收串流。 因此，用户只需以单一存储格式存储文件并为其付费，媒体服务服务就会基于客户端的请求构建并提供相应响应。

下圖顯示傳統編碼和靜態封裝工作流程。

![靜態編碼](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

下圖顯示動態封裝工作流程。

![動態編碼](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>常见方案

1. 上傳輸入檔案 (稱為夾層檔)。 例如，H.264、MP4 或 WMV (如需支援格式清單，請參閱 [媒體編碼器標準所支援的格式](media-services-media-encoder-standard-formats.md))。
2. 將夾層檔編碼為 H.264 MP4 自動調整位元速率集。
3. 藉由建立隨選定位器，發行包含自動調整位元速率 MP4 集的資產。
4. 建置串流 URL 來存取和串流您的內容。

## <a name="preparing-assets-for-dynamic-streaming"></a>準備動態串流的資產

若要准备用于动态流式传输的资产，可以使用以下选项：

- [上傳主檔案](media-services-dotnet-upload-files.md)。
- [使用媒體編碼器標準編碼器產生 H.264 MP4 自動調整位元速率集](media-services-dotnet-encode-with-media-encoder-standard.md)。
- [串流處理內容](media-services-deliver-content-overview.md)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>動態封裝支援的音訊轉碼器

动态打包支持 MP4 文件，其中包含使用 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)（AAC-LC、HE-AAC v1、HE-AAC v2）、[Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增强版 AC-3 或 E-AC3）、Dolby Atmos 或 [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)（DTS Express、DTS LBR、DTS HD、DTS HD 无损）编码的音频。 流式传输 Dolby Atmos 内容适用于特定的标准（例如 MPEG-DASH 协议），采用通用流式传输格式 (CSF) 或通用媒体应用程序格式 (CMAF) 分段 MP4，在使用 CMAF 的情况通过 HTTP 实时传送视频流 (HLS) 来进行。

> [!NOTE]
> 動態封裝不支援包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音訊的檔案 (它是舊版的轉碼器)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

