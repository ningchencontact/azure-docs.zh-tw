---
title: Azure 媒體服務中的串流定位器 | Microsoft Docs
description: 本文解釋串流定位器是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299158"
---
# <a name="streaming-locators"></a>串流定位器

若要讓輸出資產中的影片可供用戶端播放，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，然後建置串流 URL。 若要建置 URL，您要串連的串流端點主機名稱 」 和 「 串流定位器路徑。 如需 .NET 範例，請參閱[取得串流定位器](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

建立 [串流定位器]  的程序稱為發佈。 根據預設，[串流定位器]  會在進行 API 呼叫後立即生效，而且會持續運作到遭到刪除為止 (除非您有設定選擇性的開始和結束時間)。 

建立時**串流定位器**，您必須指定**資產**名稱，**串流原則**名稱。 如需詳細資訊，請參閱下列主題：

* [資產](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

您也可以指定您串流定位器 」，這只會讓您的使用者的開始和結束時間播放這些 （比方說，在 5/1/2019 到 5/5/2019年) 之間的時間之間的內容。  

## <a name="considerations"></a>考量

* **串流定位器**為不可更新。 
* 屬於日期時間類型的**串流定位器**屬性一律為 UTC 格式。
* 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="create-streaming-locators"></a>建立串流定位器  

### <a name="not-encrypted"></a>未加密

如果您想要串流處理您的檔案中--清除 （非加密的），設定預先定義的清除資料流原則： 至 'Predefined_ClearStreamingOnly' （在.NET 中，您可以使用 PredefinedStreamingPolicy.ClearStreamingOnly 列舉）。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>已加密 

如果您要加密內容的 CENC 加密，請設定您的原則以 'Predefined_MultiDrmCencStreaming'。 Widevine 加密支付 DASH 資料流和 PlayReady Smooth。 索引鍵會播放用戶端根據設定的 DRM 授權傳遞。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

如果您也想要加密您的 HLS 串流與 CBCS (FairPlay)，請使用 'Predefined_MultiDrmStreaming'。

## <a name="associate-filters-with-streaming-locators"></a>串流定位器相關聯的篩選器

請參閱[篩選器： 使用串流定位器關聯](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>篩選、 訂單、 頁面串流定位器實體

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="list-streaming-locators-by-asset-name"></a>列出串流定位器的資產名稱

若要取得相關聯的資產名稱為基礎的串流定位器，請使用下列作業：

|語言|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>另請參閱

* [資產](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

## <a name="next-steps"></a>後續步驟

[教學課程：使用 .NET 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
