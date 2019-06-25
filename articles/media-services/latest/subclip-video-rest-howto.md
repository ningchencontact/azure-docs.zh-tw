---
title: 當編碼使用 Azure 媒體服務 REST API 時，子剪輯影片
description: 本主題描述如何使用 REST 的 Azure 媒體服務編碼時，子剪輯影片
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305169"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>當編碼使用媒體服務-REST 時，子剪輯影片

您可以修剪或編碼方式時，子剪輯影片[作業](https://docs.microsoft.com/rest/api/media/jobs)。 這項功能適用於任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)所建置使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)預設值，或有[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)預設值。 

本主題中的其餘範例會建立工作以修剪視訊，因為它會將提交編碼工作。 

## <a name="prerequisites"></a>必要條件

若要完成此主題中所述的步驟，您必須：

- [建立 Azure 媒體服務帳戶](create-account-cli-how-to.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。
    
    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 
- 建立轉換和輸出資產。 您可以了解如何建立轉換和輸出中的資產[遠端檔案，以根據 URL 編碼和串流影片-REST](stream-files-tutorial-with-rest.md)教學課程。
- 檢閱[編碼概念](encoding-concept.md)主題。

## <a name="create-a-subclipping-job"></a>建立子剪輯作業

1. 在您下載的 Postman 集合，選取**轉換，並在作業** -> **建立的工作與子剪輯**。
    
    **PUT**要求看起來像這樣：
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 以您的轉換名稱更新 「 transformName"環境變數的值。 
1. 選取 **主體**索引標籤，然後使用您的輸出來更新 「 myOutputAsset"資產名稱。

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. 按 [傳送]  。

    您會看到**回應**以建立和提交作業的相關資訊和作業的狀態。 

## <a name="next-steps"></a>後續步驟

[如何使用自訂的轉換進行編碼](custom-preset-rest-howto.md) 
