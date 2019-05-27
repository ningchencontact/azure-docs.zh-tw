---
title: 媒體服務中的內容金鑰原則 - Azure | Microsoft Docs
description: 本文解釋內容金鑰原則是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155731"
---
# <a name="content-key-policies"></a>內容金鑰原則

使用媒體服務，您就能傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密的即時與隨選內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。

若要指定您的資料流上的加密選項，您需要建立[串流原則](streaming-policy-concept.md)和其關聯您[串流定位器](streaming-locators-concept.md)。 您需要建立[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)來設定如何內容金鑰 (可提供安全存取您[資產](assets-concept.md)) 傳遞給終端用戶端。 **內容的索引鍵原則**也都有您**串流定位器**。 您需要設定的需求 （限制） 上的索引鍵的順序必須符合傳遞給用戶端指定的組態與內容金鑰原則。 

建議您讓媒體服務自動產生內容金鑰。 一般而言，您會使用長時間執行的金鑰，並檢查原則是否存在**取得**。 若要取得金鑰，您必須呼叫個別的動作方法，以取得秘密或認證，請參閱下列範例。

**內容金鑰原則**是可更新的。 例如，您可能會在必須替換金鑰時，更新原則。 您也可以更新現有原則中的主要驗證金鑰和替代驗證金鑰清單。 金鑰傳遞快取可能需要 15 分鐘的時間才能更新並挑選更新後的原則。 

> [!IMPORTANT]
> * 屬於日期時間類型的**內容金鑰原則**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="example"></a>範例

若要取得金鑰，請使用 **GetPolicyPropertiesWithSecretsAsync**，如下面範例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [使用 AES-128 動態加密和金鑰傳遞服務](protect-with-aes128.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
