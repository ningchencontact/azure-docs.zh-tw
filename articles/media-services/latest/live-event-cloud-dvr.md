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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: a10c76dd7fb4ef1e9a45666ff3a3ca0d937d2c94
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231220"
---
# <a name="using-a-cloud-digital-video-recorder-dvr"></a>使用雲端數位錄影機 (DVR)

在 Azure 媒體服務中,[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)物件就像是數位錄影機, 它會攔截您的即時串流, 並將其記錄到您媒體服務帳戶中的資產。 記錄的內容會保存到[資產](https://docs.microsoft.com/rest/api/media/assets)資源所定義的容器中 (容器位於附加至您帳戶的 Azure 儲存體帳戶中)。 即時輸出也可讓您控制傳出即時串流的某些屬性, 例如保存在封存記錄中的資料流程數量 (例如, 雲端 DVR 的容量), 以及檢視器是否可以開始監看即時串流。 磁片上的封存是「迴圈保存」的「視窗」, 只會保存即時輸出的**archiveWindowLength**屬性中所指定的內容量。 超出此時間的內容會自動從儲存容器中捨棄，而且無法復原。 ArchiveWindowLength 值代表 ISO-8601 時間範圍持續期間 (例如, PTHH: MM: SS), 可指定 DVR 的容量, 且最少可以設定為最多25小時的3分鐘。

實況活動與其即時輸出之間的關聯性與傳統電視廣播類似, 其中頻道 (即時事件) 代表影片的持續性串流, 而錄製 (即時輸出) 的範圍是特定的時間區段 (例如, 晚上6: 1:30 至7的新聞:00)。 當資料流程流入即時事件之後, 您就可以藉由建立資產、即時輸出和串流定位器來開始串流事件。 即時輸出會封存串流, 並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)讓檢視器可以使用它。 您可以在具有不同保存長度和設定的實況活動上建立多個即時輸出 (最多三個)。 如需有關即時串流工作流程的詳細資訊, 請參閱[一般步驟](live-streaming-overview.md#general-steps)一節。

## <a name="using-a-dvr-during-an-event"></a>在事件期間使用 DVR 

本節討論如何在事件期間使用 DVR, 以控制可供「倒轉」使用的資料流程部分。

ArchiveWindowLength 值會決定檢視器可以從目前的即時位置往多久進行搜尋的時間。 ArchiveWindowLength 值也會決定用戶端資訊清單可以成長多長的時間。

假設您正在串流處理足球遊戲, 而且它只有30分鐘的 ArchiveWindowLength。 在比賽開始後 45 分鐘開始觀看事件的檢視者，只能往過去搜尋最多至 15 分鐘標記。 遊戲的即時輸出會繼續進行, 直到即時事件停止為止, 但超出 archiveWindowLength 的內容會持續從儲存體中捨棄, 而且無法復原。 在此範例中, 事件開始與15分鐘標記之間的影片會從您的 DVR 和資產的 blob 儲存體中的容器中清除。 封存是不可復原的，並且會從 Azure Blob 儲存體中的容器移除。

實況活動支援最多三個同時執行的即時輸出 (您可以同時從一個即時資料流建立最多3個錄製/封存)。 這可讓您視需要發行和封存事件的不同部分。 假設您需要廣播 24 小時全年無休的即時線性饋送，並建立一天中不同節目的「錄製」，以提供給客戶作為可追趕觀看的隨選內容。 針對此案例，您首先會建立一個主要「實況輸出」，其封存時間範圍為 1 小時或更短 (這是檢視者會觀看的主要即時資料流)。 您會建立此即時輸出的串流定位器, 並將其發佈至您的應用程式或網站作為「即時」摘要。 當即時事件正在執行時, 您可以透過程式設計方式, 在程式開始時建立第二個並行即時輸出 (或提早5分鐘, 以提供稍後要修剪的一些控制碼)。 這個第二個即時輸出可以在程式結束後5分鐘後刪除。 使用第二個資產時, 您可以建立新的串流定位器, 將此程式發佈為應用程式目錄中的隨選資產。 您可以針對想要與隨選影片共用的其他程式界限或重點, 重複這個進程多次, 而第一個即時輸出的「即時」摘要則會繼續廣播線性摘要。 

## <a name="creating-an-archive-for-on-demand-playback"></a>建立隨選播放的封存

即時輸出所封存的資產, 會在即時輸出刪除時自動成為隨選資產。 您必須先刪除所有即時輸出, 才能停止即時事件。 您可以使用選擇性的旗標[removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) , 在停止時自動移除即時輸出。 

即使在您停止並刪除事件之後, 只要您未刪除資產, 使用者就可以視需要將封存的內容串流為影片。 如果事件使用資產, 則不應將它刪除;必須先刪除事件。

如果您已使用串流定位器來發佈實況輸出的資產, 實況活動 (最長可達 DVR 視窗長度) 將繼續可見, 直到串流定位器的到期或刪除為止 (以先發生者為准)。

如需詳細資訊，請參閱：

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 當您刪除即時輸出時, 不會刪除資產中的基礎資產和內容。 

## <a name="next-steps"></a>後續步驟

* [子剪輯您的](subclip-video-rest-howto.md)影片。
* [定義資產的篩選準則](filters-dynamic-manifest-rest-howto.md)。
