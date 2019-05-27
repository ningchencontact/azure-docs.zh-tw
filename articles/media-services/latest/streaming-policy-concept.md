---
title: Azure 媒體服務中的串流原則 | Microsoft Docs
description: 本文解釋串流原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120205"
---
# <a name="streaming-policies"></a>串流原則

Azure 媒體服務 v3 中，[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)可讓您設定[串流定位器](streaming-locators-concept.md)的串流通訊協定和加密選項。 媒體服務 v3 提供一些預先定義資料流的原則，以便您可以將它們直接用於試用或生產環境。 

目前可用的預先定義資料流的原則：<br/>'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' 和 'Predefined_MultiDrmStreaming'。

如果您有特殊需求 （例如，如果您想要指定不同的通訊協定，必須使用自訂金鑰傳遞服務，或需要使用清除音訊資料軌），您可以建立自訂的資料流處理原則。 

 
> [!IMPORTANT]
> * 屬於日期時間類型的**串流原則**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="decision-tree"></a>決策樹

下列決策樹可協助您選擇預先定義資料流處理原則，您的案例。

按一下影像可以完整大小檢視。  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>範例

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

如果您要使用信封和 CENC 加密來加密您的內容，請將您的原則設定為 'Predefined_MultiDrmCencStreaming'。 此原則會指出您要在定位器上產生並設定兩個內容金鑰 (信封和 CENC)。 因此，會套用信封、PlayReady 及 Widevine 加密 (會根據設定的 DRM 授權將金鑰傳遞至播放用戶端)。

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

如果您也想要使用 CBCS (FairPlay) 為串流加密，請使用 'Predefined_MultiDrmStreaming'。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
