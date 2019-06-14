---
title: Azure 媒體服務實況活動和雲端 DVR | Microsoft Docs
description: 本文說明「實況輸出」是什麼，以及如何使用雲端 DVR。
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322330"
---
# <a name="using-a-cloud-dvr"></a>使用雲端 DVR

[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)可讓您控制傳出即時資料流的屬性，像是錄製多少資料流 (例如雲端 DVR 的容量)，以及檢視者是否可以開始觀看即時資料流。 **實況活動**與其**實況輸出**之間的關聯性類似於傳統的電視廣播，其中頻道 (**實況活動**) 代表持續的視訊資料流，而錄製 (**實況輸出**) 的範圍則限制在特定的時間區段 (例如下午 6:30 至下午 7:00 的晚間新聞)。 您可以使用「數位影像錄影機」(DVR) 來錄製電視，而「實況活動」中對等的功能則會透過 ArchiveWindowLength 屬性來管理。 它是 ISO-8601 時間範圍持續時間 (例如 PTHH:MM:SS)，可指定 DVR 的容量，並且可設定為最小 3 分鐘至最大 25 小時的值。

## <a name="live-output"></a>實況輸出

**ArchiveWindowLength** 值決定檢視者可以從目前的即時位置往過去的時間搜尋多少。  **ArchiveWindowLength** 也決定用戶端資訊清單可以達到多長。

假設您正在串流處理足球比賽，而它的 **ArchiveWindowLength** 只有 30 分鐘。 在比賽開始後 45 分鐘開始觀看事件的檢視者，只能往過去搜尋最多至 15 分鐘標記。 比賽的**實況輸出**會持續直到**實況活動**停止為止，但超出 **ArchiveWindowLength** 範圍的內容會持續被捨棄而不儲存，且無法復原。 在此範例中，事件的起始和 15 分鐘標記之間的視訊，會從您的 DVR 和從 [Asset](https://docs.microsoft.com/rest/api/media/assets) 的 Blob 儲存體中的容器清除。 封存是不可復原的，並且會從 Azure Blob 儲存體中的容器移除。

每個**實況輸出**都與一個**資產**相關，它會使用該「資產」將視訊錄製到相關的 Azure Blob 儲存體容器中。 若要發佈「實況輸出」，您必須為該**資產**建立**串流定位器**。 建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)之後，您可以建立可提供給檢視者的串流 URL。

**實況活動**最多可支援三個同時執行的**實況輸出**，因此您可以從一個即時資料流最多建立 3 個錄製/封存。 這可讓您視需要發行和封存事件的不同部分。 假設您需要廣播 24 小時全年無休的即時線性饋送，並建立一天中不同節目的「錄製」，以提供給客戶作為可追趕觀看的隨選內容。 針對此案例，您首先會建立一個主要「實況輸出」，其封存時間範圍為 1 小時或更短 (這是檢視者會觀看的主要即時資料流)。 您會為這個**實況輸出**建立一個**串流定位器**，然後將它發佈到您的應用程式或網站作為「即時」摘要。 在**實況活動**執行的同時，您可以透過程式設計方式，在節目開始時 (或提早 5 分鐘以提供一些可供稍後修剪的控點)，建立第二個並行的**實況輸出**。 這第二個**實況輸出**可在節目結束 5 分鐘後刪除。 有了這第二個**資產**，您便可以建立新的**串流定位器**，以將此節目發佈為您應用程式目錄中的隨選資產。 只要來自第一個**實況輸出**的「即時」摘要繼續廣播線性摘要，您就可以針對您想要當作隨選視訊來共用的其他節目界限或精選，多次重複此程序。 

> [!NOTE]
> **實況輸出**會在建立時開始，並在刪除時結束。 當您刪除**實況輸出**時，所刪除的並不是基礎**資產**及資產中的內容。 
>
> 如果您已使用**串流定位器**來發佈**實況輸出**資產，**實況活動** (最長可達 DVR 時段長度) 將繼續可供檢視，直到**串流定位器**到期或被刪除 (以先到者為準) 為止。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

