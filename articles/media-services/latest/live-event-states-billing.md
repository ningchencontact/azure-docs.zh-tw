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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585400"
---
# <a name="liveevent-states-and-billing"></a>LiveEvent 狀態和計費

在 Azure 媒體服務中，LiveEvent 只要其狀態轉換為「執行中」，就會開始計費。 若要停止 LiveEvent 計費，您必須停止 LiveEvent。

當 [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 上的 **LiveEventEncodingType** 設定為 [標準]\(基本\) 時，媒體服務會在輸入摘要遺失且沒有 **LiveOutput** 仍在執行的 12 小時後，自動關閉任何仍處於「執行中」狀態的 LiveEvent。 不過，您仍將需要支付 LiveEvent 處於「執行中」狀態時間的費用。

## <a name="states"></a>狀態

LiveEvent 可以是下列其中一種狀態：

|State|說明|
|---|---|
|**已停止**| 這是 LiveEvent 建立後的初始狀態 (除非自動啟動已設為 true)。此狀態中不會計費。 在此狀態下，LiveEvent 屬性可以更新，但是不允許串流。|
|**啟動中**| 正在啟動 LiveEvent 以及配置資源。 此狀態中不會計費。 在此狀態期間，不允許任何更新或串流。 如果發生錯誤，LiveEvent 會回到「已停止」狀態。|
|**執行中**| LiveEvent 資源已配置、內嵌和預覽 Url 已建立，而且能夠接收即時串流。 此時已開始計費。 您必須在 LiveEvent 資源上明確呼叫停止，才能中止進一步計費。|
|**停止中**| 正在停止 LiveEvent 以及取消佈建資源。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|
|**刪除中**| 正在刪除 LiveEvent。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
