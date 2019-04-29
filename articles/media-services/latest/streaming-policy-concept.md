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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230891"
---
# <a name="streaming-policies"></a>串流原則

Azure 媒體服務 v3 中，[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)可讓您設定[串流定位器](streaming-locators-concept.md)的串流通訊協定和加密選項。 您可以使用其中一個預先定義的串流原則，或建立自訂原則。 可用的預先定義串流原則如下：'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' 和 'Predefined_MultiDrmStreaming'。

> [!IMPORTANT]
> * 屬於日期時間類型的**串流原則**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 

## <a name="examples"></a>範例

### <a name="not-encrypted"></a>未加密

如果您想要串流乾淨的檔案 (未加密)，請將預先定義的乾淨串流原則設定為 'Predefined_ClearStreamingOnly' (在 .NET 中，您可以使用 PredefinedStreamingPolicy.ClearStreamingOnly)。

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
