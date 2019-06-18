---
title: Azure 媒體服務中的 LiveEvent 狀態和計費 | Microsoft Docs
description: 本主題提供 Azure 媒體服務 LiveEvent 狀態和計費的概觀。
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2907b5be7f8d5fda3d510484179e80b065ab64b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074885"
---
# <a name="live-event-states-and-billing"></a>實況活動狀態和計費

在「Azure 媒體服務」中，「實況活動」只要狀態轉換為 [正在執行]  ，就會立即開始計費。 若要停止「實況活動」計費，您必須停止「實況活動」。

當**LiveEventEncodingType**在您[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)設定為 [標準] 或 [Premium1080p，自動關閉仍在任何即時事件的媒體服務**執行**狀態 12時數之後輸入的摘要的遺失，且沒有任何**Live 輸出**執行。 不過，您仍將需要支付「實況活動」處於 [正在執行]  狀態時間的費用。

## <a name="states"></a>狀態

「實況活動」的狀態可以是下列其中之一。

|State|描述|
|---|---|
|**已停止**| 這是「實況活動」建立後的初始狀態 (除非已將自動啟動設定為 true)。此狀態中不會計費。 在此狀態下，可以更新「實況活動」屬性，但是不允許進行串流。|
|**啟動中**| 正在啟動「實況活動」並配置資源。 此狀態中不會計費。 在此狀態期間，不允許任何更新或串流。 如果發生錯誤，「實況活動」會回到 [已停止] 狀態。|
|**執行中**| 已配置、內嵌「實況活動」資源，並已產生預覽 URL，而能夠接收即時資料流。 此時已開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。|
|**停止中**| 正在停止「實況活動」並將資源取消佈建。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|
|**刪除中**| 正在刪除「實況活動」。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
