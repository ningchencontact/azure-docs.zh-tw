---
title: 使用媒體服務在雲端編碼 - Azure | Microsoft Docs
description: 本主題說明使用 Azure 媒體服務時的編碼程序
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d236f00e70e08c7bce2a94c5bd4fb64f1fa99bbc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54826593"
---
# <a name="encoding-with-media-services"></a>使用媒體服務編碼

Azure 媒體服務可讓您將高品質數位媒體檔案編碼成可在各種不同的瀏覽器和裝置上播放的格式。 例如，您可能會想要串流處理 Apple 的 HLS 或 MPEG DASH 格式的內容。 本主題會指導您如何使用媒體服務 v3 將內容編碼。

若要使用媒體服務 v3 來編碼，您需要建立轉換和作業。 轉換可定義編碼的設定和輸出配方，作業則是配方的執行個體。 如需詳細資訊，請參閱[轉換和作業](transform-concept.md)

使用媒體服務編碼時，需使用預設來告訴編碼器應該如何處理輸入媒體檔案。 例如，您可以指定所編碼內容中需要的視訊解析度及/或音訊聲道數目。 

您可以使用依據業界最佳做法所建議的其中一個內建預設來快速開始，也可以選擇建置以特定案例或裝置需求為標的的自訂預設。 如需詳細資訊，請參閱[使用自訂轉換進行編碼](customize-encoder-presets-how-to.md)。 

從 2019 年 1 月開始，使用「媒體編碼器標準」來編碼以產生 MP4 檔案時，會產生新的 .mpi 檔案並將該檔案新增到輸出資產。 此 MPI 檔案的目的是用來改進動態封裝與串流處理案例的效能。

> [!NOTE]
> 您不應該修改或移除該 MPI 檔案，也不應該在您的服務中相依於此類檔案的存在與否。

## <a name="built-in-presets"></a>內建預設

媒體服務目前支援下列內建編碼預設：  

|**預設名稱**|**案例**|**詳細資料**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|串流|用來設定內建預設，以供使用標準編碼器為輸入視訊編碼時使用。 <br/>目前支援的預設如下：<br/>**EncoderNamedPreset.AdaptiveStreaming** (建議)。 如需詳細資訊，請參閱[自動產生位元速率階梯](autogen-bitrate-ladder.md)。<br/>**EncoderNamedPreset.AACGoodQualityAudio** - 會產生只包含立體聲音訊 (以 192 kbps 編碼) 的單一 MP4 檔案。<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - 會產生一組 8 個對齊 GOP 的 MP4 檔案 (範圍從 6000 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 1080p，下至 360p。<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - 會產生一組 6 個對齊 GOP 的 MP4 檔案 (範圍從 3400 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 720p，下至 360p。<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - 會產生一組 5 個對齊 GOP 的 MP4 檔案 (範圍從 1600 kbps 到 400 kbps) 和立體聲 AAC 音訊。 解析度起自 480p，下至 360p。<br/><br/>如需詳細資訊，請參閱[上傳、編碼和串流檔案](stream-files-tutorial-with-api.md)。|
|**StandardEncoderPreset**|串流|說明在使用標準編碼器為輸入視訊編碼時，所要使用的設定。 <br/>在自訂轉換預設時，請使用此預設。 如需詳細資訊，請參閱[如何自訂轉換預設](customize-encoder-presets-how-to.md)。|

## <a name="custom-presets"></a>自訂預設

「媒體服務」可完整支援自訂預設中的所有值，以滿足您的特定編碼需要和需求。 在自訂轉換預設時，請使用 **StandardEncoderPreset** 預設。 如需詳細說明和範例，請參閱[如何自訂編碼器預設](customize-encoder-presets-how-to.md)。

## <a name="scaling-encoding-in-v3"></a>在 v3 中調整編碼

目前，客戶必須使用 Azure 入口網站或媒體服務 v2 API 來設定 RU (如[調整媒體處理](../previous/media-services-scale-media-processing-overview.md)所述)。 

## <a name="next-steps"></a>後續步驟

### <a name="tutorials"></a>教學課程

下列教學課程說明如何使用媒體服務將內容編碼：

* [使用媒體服務上傳、編碼和串流](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>程式碼範例

下列程式碼範例包含的程式碼會說明如何使用媒體服務來編碼：

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Azure CLI](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

您可以使用下列受支援的媒體服務 v3 SDK 來將內容編碼。

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

