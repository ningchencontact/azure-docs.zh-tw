---
title: Azure 媒體服務 LiveEvent 和雲端 DVR | Microsoft Docs
description: 本文說明 LiveOutput 是什麼，以及如何使用雲端 DVR。
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52636983"
---
# <a name="using-a-cloud-dvr"></a>使用雲端 DVR

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 可讓您控制傳出即時資料流的屬性，例如錄製了多少資料流 (例如雲端 DVR 的容量)，以及檢視者是否可以開始觀看即時資料流。 **LiveEvent** 和其 **LiveOutput** 之間的關聯性就像傳統的電視廣播，其中頻道 (**LiveEvent**) 表示持續的視訊資料流，而錄製 (**LiveOutput**) 的範圍限制在特定時間區段 (例如下午 6:30 至下午 7:00 的晚間新聞)。 您可以使用數位視訊錄影機 (DVR) 錄製電視，而 LiveEvent 中對等的功能是透過 ArchiveWindowLength 屬性來管理。 它是 ISO-8601 時間範圍持續時間 (例如 PTHH:MM:SS)，可指定 DVR 的容量，並且可設定為最小 3 分鐘至最大 25 小時的值。

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** 值決定檢視者可以從目前的即時位置往過去的時間搜尋多少。  **ArchiveWindowLength** 也決定用戶端資訊清單可以達到多長。

假設您正在串流處理足球比賽，而它的 **ArchiveWindowLength** 只有 30 分鐘。 在比賽開始後 45 分鐘開始觀看事件的檢視者，只能往過去搜尋最多至 15 分鐘標記。 比賽的 **LiveOutput** 會持續直到 **LiveEvent** 停止，但是系統會將超出 **ArchiveWindowLength** 的內容從儲存體持續捨棄，且這些內容是不可復原的。 在此範例中，事件的起始和 15 分鐘標記之間的視訊，會從您的 DVR 和從 [Asset](https://docs.microsoft.com/rest/api/media/assets) 的 Blob 儲存體中的容器清除。 封存是不可復原的，並且會從 Azure Blob 儲存體中的容器移除。

每個 **LiveOutput** 都與 **Asset** 相關聯，它使用 Asset 將視訊錄製到相關聯的 Azure Blob 儲存體容器中。 若要發佈 LiveOutput，您必須為該 **Asset** 建立 **StreamingLocator**。 建立 [StreamingLocator ](https://docs.microsoft.com/rest/api/media/streaminglocators) 之後，您可以建立可提供給檢視者的串流 URL。

**LiveEvent** 支援最多三個同時執行的 **LiveOutput**，所以您可以從一個即時資料流建立最多 3 個錄製/封存。 這可讓您視需要發行和封存事件的不同部分。 假設您需要廣播 24 小時全年無休的即時線性饋送，並建立一天中不同節目的「錄製」，以提供給客戶作為可追趕觀看的隨選內容。 在此案例中，您首先建立主要 LiveOutput，其封存時間範圍為 1 小時或更短 (這是檢視者會觀看的主要即時資料流)。 您可以為此 **LiveOutput** 建立 **StreamingLocator**，並將它發佈到您的應用程式或網站作為「即時」饋送。 **LiveEvent** 在執行的同時，您可以在節目開始的時候 (或 5 分鐘前，以提供稍後修剪的控點)，透過程式設計方式建立第二個同時執行的 **LiveOutput**。 此第二個 **LiveOutput** 可以在節目結束 5 分鐘後刪除。 對於此地二個 **Asset**，您可以建立新的 **StreamingLocator** 來發佈此節目，作為您應用程式目錄中的隨選資產。 只要來自第一個 **LiveOutput** 的「即時」饋送繼續廣播線性饋送，您就可以針對想要共用為點播視訊的其他節目界限或精選重複此程序多次。 

> [!NOTE]
> **LiveOutput** 在建立時開始，並在刪除時結束。 當您刪除 **LiveOutput** 時，您不是刪除基礎的 **Asset** 和 Asset 中的內容。  

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

