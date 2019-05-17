---
title: 了解 Azure 時間序列深入解析環境中的資料保留 | Microsoft Docs
description: 本文說明「Azure 時間序列深入解析」環境中，控制資料保留的兩個設定。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 443599e1b2876012bcbdf720bef7762a24e1ff90
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790419"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>了解 Azure Time Series Insights 中的資料保留

本文說明兩個會影響您的 Azure Time Series Insights 環境中的資料保留期的設定。

## <a name="video"></a>影片

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>下列影片摘要說明時間序列深入解析資料保留期，以及如何規劃它。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

每個 Azure 時間序列環境有設定，控制**資料保留時間**。 可設定的值為 1 到 400 天。 刪除環境儲存容量或保留持續時間為基礎的資料，視何者先。

此外，您的 Azure 時間序列環境會有**超過儲存體限制的行為**設定。 它會控制輸入，並清除行為，當達到環境的容量上限時。 有兩種設定時從中選擇的行為：

- **清除舊資料** (預設)  
- **暫停輸入**

> [!NOTE]
> 根據預設，建立新環境時，保留期會設定為**清除舊資料**。 可以切換此設定，在建立期間，使用 Azure 入口網站中，在後視**設定**的 Time Series Insights 環境的頁面。

如需切換保留行為的相關資訊，請檢閱[在時間序列深入解析中設定保留](time-series-insights-how-to-configure-retention.md)。

比較資料保留行為：

## <a name="purge-old-data"></a>清除舊資料

- 此行為是 Time Series Insights 環境的預設行為。  
- 此行為是慣用的當使用者想要一律會看到他們*最新的資料*Time Series Insights 環境中。
- 在達到環境限制 (保留時間、大小或計數，以先達到者為準) 時，此行為就會「清除」資料。 保留期預設值為 30 天。
- 最舊的內嵌資料會先清除 (FIFO 方法)。

### <a name="example-one"></a>其中一個範例

考慮具有保留行為 [繼續輸入並清除舊資料] 的範例環境：

**資料保留時間**設為 400 天。 [容量] 是設定為 S1 單位，其中包含 30 GB 的總容量。   假設傳入的資料平均每天會累積至 500 MB。 在給定的資料傳入速率下，此環境只能將資料保留 60 天，因為會在 60 天時達到容量上限。 傳入資料累積為：每天 500 MB x 60 天 = 30 GB。

第 61 天時，環境會顯示最新的資料，但會清除舊資料，超過 60 天。 清除動作可釋出空間供傳入的新資料使用，因此可以繼續瀏覽新資料。 如果使用者希望將資料保留更久一點，可以透過增加額外單位容量以提高環境的資料容納量，或減少傳入的資料量。  

### <a name="example-two"></a>範例二

考慮也設定 [繼續輸入並清除舊資料] 保留行為的環境。 在這個範例中，[資料保留時間] 是設定為較低的值：180 天。 [容量] 是設定為 S1 單位，其中包含 30 GB 的總容量。 為將資料儲存 180 天，每日輸入量不能超過 0.166 GB (166 MB)。  

只要此環境的每日輸入速率超過 0.166 GB，資料就無法儲存 180 天，因為某些資料會被清除。 請考慮此相同環境在忙碌的時間範圍內會發生什麼狀況。 假設環境的資料輸入速率可能會增加到每日平均 0.189 GB。 在該忙碌時間範圍內，約可保留 158 天的資料 (30 GB/0.189 = 158.73 保留天數)。 此時間短於所需的資料保留時間範圍。

## <a name="pause-ingress"></a>暫停輸入

- **暫停輸入**設定的設計可確保如果大小和計數限制達到保留期限之前，不會清除資料。  
- **暫停輸入**提供額外的時間，以增加其環境的容量，因為違反的保留期限清除資料之前的使用者
- 它可協助保護您免於資料遺失，但可以建立最新的資料遺失的機會，如果您的事件來源的保留期限超過之前暫停輸入。
- 不過，一旦達到環境的最大容量，環境會暫停資料輸入之前執行下列額外動作：

   - 您環境的最大容量增加到新增更多縮放單位中所述[如何調整您的 Time Series Insights 環境規模](time-series-insights-how-to-scale-your-environment.md)。
   - 在達到資料保留期和清除資料，讓環境容量上限以下。

### <a name="example-three"></a>範例三

考慮保留期行為設定為**暫停輸入**的環境。 在此範例中，[資料保留期間] 設定為 60 天。 **容量**設為三 （3） 個 S1 單位。 假設此環境每天輸入 2 GB 資料。 在此環境中，一旦達到最大容量就會暫停輸入。

屆時，環境會顯示相同的資料集直到恢復輸入或直到**繼續輸入**已啟用 （這會清除較舊的資料，以騰出空間給新的資料）。

恢復繼續輸入時：

- 資料會依事件來源收到資料的順序輸入
- 系統會依據事件的時間戳記為事件編製索引，除非您已超出事件來源的保留原則。 如需有關事件來源保留設定的詳細資訊，請參閱[事件中樞常見問題集](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> 您應該設定警示，以提供可協助避免暫停輸入資料的通知。 因為 Azure 事件來源的保留期預設為 1 天，所以是有可能會遺失資料的。 因此，輸入暫停之後，除非您採取其他動作，否則就可能會遺失最新資料。 您必須增加容量，或將行為切換為 [清除舊資料]，以避免發生資料遺失。

當事件中樞受到影響時，請考慮調整 [訊息保留期] 屬性，以在「時間序列深入解析」中發生暫停輸入時，將資料遺失機率降到最低。

[![事件中樞訊息保留。](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

如果不設定事件來源的任何屬性 (`timeStampPropertyName`)，預設為抵達事件中樞為 x 軸的時間戳記的時間序列深入解析。 如果`timeStampPropertyName`設定為其他項目，已設定的環境看起來`timeStampPropertyName`資料封包時所剖析出的事件中。

如果您需要相應增加環境以容納更多容量，或需要延長保留長度，請參閱[如何調整您的時間序列深入解析環境規模](time-series-insights-how-to-scale-your-environment.md)以了解詳細資訊。  

## <a name="next-steps"></a>後續步驟

- 如需設定或變更資料保留設定的詳細資訊，請檢閱[在時間序列深入解析中設定保留](time-series-insights-how-to-configure-retention.md)。
