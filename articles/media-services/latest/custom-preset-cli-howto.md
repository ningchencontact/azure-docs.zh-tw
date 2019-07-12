---
title: 編碼自訂的轉換使用媒體服務 v3 CLI-Azure |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3 來編碼使用 CLI 的自訂轉換。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762015"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>如何使用自訂的轉換-CLI 進行編碼

當使用 Azure 媒體服務編碼，您可以快速開始使用其中一個建議的內建預設值，根據業界最佳作法，如所示[串流檔案](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)快速入門。 您也可以建置自訂預設值為目標的特定案例或裝置需求。

## <a name="considerations"></a>考量

在建立自訂的預設設定時，適用下列考量：

* 高度和寬度 AVC 內容上的所有值必須都是 4 的倍數。
* Azure 媒體服務 v3 中所有的編碼位元速率是每秒位元。 這是我們使用千位元/秒為單位的 v2 Api 與預設值不同。 比方說，如果 （kb/秒） 指定在 v2 中的位元速率為 128，v3 中它會設 128000 （位元/秒）。

## <a name="prerequisites"></a>先決條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。 <br/>請務必記住資源群組名稱和「媒體服務」帳戶名稱。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定義自訂的預設值

下列範例會定義新的轉換的要求主體。 我們會定義一組我們想要使用這項轉換時產生的輸出。 

在此範例中，我們先新增 AacAudio 層的音訊的編碼方式和兩個 H264Video 層進行視訊編碼。 在視訊層中，我們會指派標籤，以便它們可以用於輸出檔案名稱。 接下來，我們想要輸出也包含 縮圖。 在下列範例中，我們會指定產生在 50%的輸入視訊的解析度和三個時間戳記-{25%、 50%、 75} 長度的輸入視訊的 PNG 格式中的映像。 最後，我們會指定輸出檔案-一個用於視訊 + 音訊格式，另一個用於縮圖。 由於我們有多個 H264Layers，我們必須使用巨集，產生唯一的名稱，每一層級。 我們可以使用`{Label}`或`{Bitrate}`巨集，此範例示範先前的功能。

我們會在檔案中儲存這項轉換。 在此範例中，我們將檔案命名為`customPreset.json`。 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}

```

## <a name="create-a-new-transform"></a>建立新的轉換  

在此範例中，我們會建立**轉換**我們稍早定義的自訂預設值為基礎。 時建立轉換，您應該先檢查是否其中一個已經存在。 如果轉換已存在，則重複使用它。 下列`show`命令會傳回`customTransformName`轉換若有的話：

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

下列 CLI 命令會建立自訂的預設值 （稍早定義） 為基礎的轉換。 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

若要將轉換套用至指定的視訊或音訊的媒體服務，您需要提交作業，以在該轉換。 如需示範如何提交下轉型作業的完整範例，請參閱[快速入門：Stream 視訊檔案-CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
