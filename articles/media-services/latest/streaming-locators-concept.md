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
ms.date: 05/08/2019
ms.author: juliako
ms.openlocfilehash: 24ee700e326ef61aa6a93aae725e85e7b4780edf
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465046"
---
# <a name="streaming-locators"></a>串流定位器

若要讓輸出資產中的影片可供用戶端播放，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，然後建置串流 URL。 如需 .NET 範例，請參閱[取得串流定位器](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

建立 [串流定位器] 的程序稱為發佈。 根據預設，[串流定位器] 會在進行 API 呼叫後立即生效，而且會持續運作到遭到刪除為止 (除非您有設定選擇性的開始和結束時間)。 

建立**串流定位器**時，您必須指定[資產](https://docs.microsoft.com/rest/api/media/assets)名稱和[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)名稱。 您可以使用其中一個預先定義的串流原則，或建立自訂原則。 目前可用的預先定義原則如下：'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' 和 'Predefined_MultiDrmStreaming'。 使用自訂的串流原則時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的選項和通訊協定時，對串流定位器重新使用這些原則。 

如果您要在串流上指定加密選項，請建立[內容金鑰原則](https://docs.microsoft.com/rest/api/media/contentkeypolicies)，該原則會設定如何透過媒體服務金鑰傳遞元件，將內容金鑰傳送給終端用戶端。 將您的串流定位器與**內容金鑰原則**和內容金鑰建立關聯。 您可以讓媒體服務自動產生金鑰。 下列 .NET 範例會示範如何在媒體服務 v3 中使用權杖限制來設定 AES 加密：[EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)。 **內容金鑰原則**是可更新的，您可能會在必須替換金鑰時，更新原則。 金鑰傳遞快取可能需要 15 分鐘的時間才能更新並挑選更新後的原則。 不建議您針對每個串流定位器建立新的內容金鑰原則。 您應嘗試在每次需要相同選項時，重複使用現有原則。

> [!IMPORTANT]
> * 屬於日期時間類型的**串流定位器**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 

## <a name="associate-filters-with-streaming-locators"></a>串流定位器相關聯的篩選器

您可以指定一份[資產或帳戶篩選器](filters-concept.md)，而這會套用至您[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)。 [動態封裝程式](dynamic-packaging-overview.md)適用於這份清單，以及那些用戶端在 URL 中所指定的篩選條件。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，根據在 URL 中的篩選器 + 串流定位器指定的篩選條件。 我們建議您使用這項功能，如果您想要套用篩選，但不是想要公開 （expose） 在 URL 中的篩選條件名稱。

## <a name="filter-order-page-streaming-locator-entities"></a>篩選、 訂單、 頁面串流定位器實體

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [教學課程：使用 .NET 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
* [使用 DRM 動態加密與授權傳遞服務](protect-with-drm.md)
