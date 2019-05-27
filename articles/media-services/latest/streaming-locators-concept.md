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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120341"
---
# <a name="streaming-locators"></a>串流定位器

若要讓輸出資產中的影片可供用戶端播放，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，然後建置串流 URL。 如需 .NET 範例，請參閱[取得串流定位器](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

建立 [串流定位器] 的程序稱為發佈。 根據預設，[串流定位器] 會在進行 API 呼叫後立即生效，而且會持續運作到遭到刪除為止 (除非您有設定選擇性的開始和結束時間)。 

建立時**串流定位器**，您必須指定**資產**名稱，**串流原則**名稱。 如需詳細資訊，請參閱下列主題：

* [資產](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

> [!IMPORTANT]
> * 屬於日期時間類型的**串流定位器**屬性一律為 UTC 格式。
> * 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="associate-filters-with-streaming-locators"></a>串流定位器相關聯的篩選器

請參閱[篩選器： 使用串流定位器關聯](filters-concept.md#associate-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>篩選、 訂單、 頁面串流定位器實體

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

[教學課程：使用 .NET 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
