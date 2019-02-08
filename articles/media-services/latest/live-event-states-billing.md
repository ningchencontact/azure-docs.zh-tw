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
ms.openlocfilehash: 17fead25840e45f98478a6eb6c146bcc261dfe75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158004"
---
# <a name="live-event-states-and-billing"></a>實況活動狀態和計費

在「Azure 媒體服務」中，「實況活動」只要狀態轉換為 [正在執行]，就會立即開始計費。 若要停止「實況活動」計費，您必須停止「實況活動」。

當[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)上的 **LiveEventEncodingType** 設定為 Standard 時，「媒體服務」會在遺失輸入摘要且沒有任何執行中**實況輸出**的 12 小時後，自動關閉任何仍處於 [正在執行] 狀態的「實況活動」。 不過，您仍將需要支付「實況活動」處於 [正在執行] 狀態時間的費用。

## <a name="states"></a>狀態

「實況活動」的狀態可以是下列其中之一。

|狀態|說明|
|---|---|
|**已停止**| 這是「實況活動」建立後的初始狀態 (除非已將自動啟動設定為 true)。此狀態中不會計費。 在此狀態下，可以更新「實況活動」屬性，但是不允許進行串流。|
|**啟動中**| 正在啟動「實況活動」並配置資源。 此狀態中不會計費。 在此狀態期間，不允許任何更新或串流。 如果發生錯誤，「實況活動」會回到 [已停止] 狀態。|
|**執行中**| 已配置、內嵌「實況活動」資源，並已產生預覽 URL，而能夠接收即時資料流。 此時已開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。|
|**停止中**| 正在停止「實況活動」並將資源取消佈建。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|
|**刪除中**| 正在刪除「實況活動」。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
