---
title: 編碼自訂的轉換使用媒體服務 v3.NET-Azure |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3 來編碼使用.NET 的自訂轉換。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: ed2ae50aa9d7a26ed6e0569264ee981f7be35525
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754551"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何使用自訂的轉換為.NET 進行編碼

當使用 Azure 媒體服務編碼，您可以快速開始使用其中一個建議的內建預設值，以根據業界最佳作法，如所示[串流檔案](stream-files-tutorial-with-api.md)教學課程。 您也可以建置自訂預設值為目標的特定案例或裝置需求。

## <a name="considerations"></a>考量

在建立自訂的預設設定時，適用下列考量：

* 高度和寬度 AVC 內容上的所有值必須都是 4 的倍數。
* Azure 媒體服務 v3 中所有的編碼位元速率是每秒位元。 這是我們使用千位元/秒為單位的 v2 Api 與預設值不同。 比方說，如果 （kb/秒） 指定在 v2 中的位元速率為 128，v3 中它會設 128000 （位元/秒）。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。 <br/>請務必記住資源群組名稱和「媒體服務」帳戶名稱。 

## <a name="download-the-sample"></a>下載範例

使用以下命令將包含完整 .NET Core 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
自訂預設的範例位於 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 資料夾。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自訂預設建立轉換 

建立新的[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您需要指定想要其產生的輸出是什麼。 必要的參數是 [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) 物件，如下列程式碼所示。 每個 **TransformOutput** 都會包含 **Preset (預設)**。 **Preset** 會描述影片和/或音訊處理作業的逐步指示，以產生所需的 **TransformOutput**。 下列 **TransformOutput** 會建立自訂轉碼器和圖層輸出設定。

建立[轉換](https://docs.microsoft.com/rest/api/media/transforms)時，您應該先使用 **Get** 方法檢查是否已有轉換存在，如下列程式碼所示。 在媒體服務 v3 中，如果實體不存在，對實體執行的 **Get** 方法會傳回 **null** (檢查名稱時不區分大小寫)。

### <a name="example"></a>範例

下列範例會定義一組我們想要使用這項轉換時產生的輸出。 我們先新增 AacAudio 層的音訊的編碼方式和兩個 H264Video 層進行視訊編碼。 在視訊層中，我們會指派標籤，以便它們可以用於輸出檔案名稱。 接下來，我們想要輸出也包含 縮圖。 在下列範例中，我們會指定產生在 50%的輸入視訊的解析度和三個時間戳記-{25%、 50%、 75} 長度的輸入視訊的 PNG 格式中的映像。 最後，我們會指定輸出檔案-一個用於視訊 + 音訊格式，另一個用於縮圖。 由於我們有多個 H264Layers，我們必須使用巨集，產生唯一的名稱，每一層級。 我們可以使用`{Label}`或`{Bitrate}`巨集，此範例示範先前的功能。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md) 
