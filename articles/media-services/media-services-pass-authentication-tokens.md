---
title: "將驗證語彙基元傳遞給 Azure Media Services |Microsoft 文件"
description: "了解如何從用戶端的驗證權杖傳送到 Azure Media Services 金鑰傳遞服務"
services: media-services
keywords: "content protection, DRM, token authentication, 內容保護, 權杖驗證"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 7d143242231444b8557a303d1b504d5311693f1a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>了解如何用戶端將權杖傳遞至 Azure Media Services 金鑰傳遞服務
客戶的常常詢問如何播放程式傳遞語彙基元給 Azure Media Services 金鑰傳遞服務進行驗證以便播放程式可以取得金鑰。 Media Services 支援簡單 web 權杖 (SWT) 和 JSON Web Token (JWT) 格式。 權杖驗證會套用至任何類型的索引鍵，不論您是使用一般加密或進階加密標準 (AES) 信封加密系統中。

 根據播放程式和您的目標平台，您可以以下列方式與您的播放程式傳遞語彙基元：

- 透過 HTTP 授權標頭。
    > [!NOTE]
    > 每個 OAuth 2.0 規格必須是"Bearer"前置詞。 使用語彙基元的組態範例播放程式裝載於 Azure Media Player[示範頁面](http://ampdemo.azureedge.net/)。 若要設定視訊來源，請選擇**AES （JWT 權杖）**或**AES （SWT 權杖）**。 權杖會傳遞透過授權標頭。

- 透過 URL 加入查詢參數 「 語彙基元 = tokenvalue。 」  
    > [!NOTE]
    > 不應該有"Bearer"前置詞。 因為權杖會傳送到 URL，您需要防護語彙基元字串。 以下是 C# 程式碼範例示範如何執行：

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- 透過 CustomData 欄位中。
透過 PlayReady 授權取得挑戰的 CustomData 欄位的 PlayReady 授權取得，使用這個選項。 在此情況下，語彙基元必須是 XML 文件內，如下所示：

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    將驗證權杖放在語彙基元的項目。

- 透過替代的 HTTP Live Streaming (HLS) 播放清單。 如果您需要設定權杖驗證 AES + HLS 播放 iOS/Safari 上, 沒有您可以直接傳送語彙基元的方法。 如需有關如何替代播放清單，以實現此案例的詳細資訊，請參閱此[部落格文章](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]