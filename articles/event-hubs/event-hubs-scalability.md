---
title: 延展性-Azure 事件中樞 |Microsoft Docs
description: 提供如何調整 Azure 事件中樞的相關資訊。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276778"
---
# <a name="scaling-with-event-hubs"></a>使用事件中樞自動調整規模

有兩個因素影響事件中樞使用自動調整規模。
*   輸送量單位
*   分割數

## <a name="throughput-units"></a>輸送量單位

事件中樞的輸送量容量受「輸送量單位」  所控制。 輸送量單位是預先購買的容量單位。 單一輸送量，可讓您：

* 輸入：每秒最多 1 MB 或 1000 個事件 (以先達到者為準)。
* 輸出：最高每秒 2 MB 或每秒 4096 個事件。

超出所購買輸送量單位的容量時，輸入就開始節流，並傳回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 輸出不會產生節流例外狀況，但仍受限於所購買輸送量單位的容量。 如果您收到發佈速率例外狀況，或輸出速率低於預期，請務必檢查您為命名空間所購買的輸送量單位數目。 在 [Azure 入口網站](https://portal.azure.com)中，您可以在命名空間的 [調整規模]  刀鋒視窗管理輸送量單位。 您也可以使用[事件中樞 API](event-hubs-api-overview.md) 以程式設計方式管理輸送量單位。

輸送量單位是預先購買制且以每小時計費。 一經購買，您至少必須支付一個小時的輸送量單位費用。 最多可以為一個事件中樞命名空間購買 20 個輸送量單位，讓該命名空間中的所有事件中樞共用。

「事件中樞」的「自動擴充」  功能會自動增加輸送量單位數以進行相應增加，進而符合使用量需求。 增加輸送量單位可避免發生節流情況，其中：

- 資料輸入速率會超出所設定的輸送量單位。
- 資料輸出要求速率會超出所設定的輸送量單位。

「事件中樞」服務可在負載超過最低閾值時增加輸送量，不會有任何要求因為發生 ServerBusy 錯誤而失敗。 

如需有關自動擴充功能，請參閱 <<c0> [ 自動調整輸送量單位](event-hubs-auto-inflate.md)。

## <a name="partitions"></a>分割數

資料分割可讓您向下游處理。 由於事件中樞資料分割提供資料分割取用者模型，您可以向外延展時同時處理您的事件。 事件中樞可以有最多 32 個分割區。

我們建議您平衡 1:1 輸送量單位和分割區，以達到最佳規模。 單一分割區具有保證的輸入和輸出的最多一個輸送量單位。 雖然您可以達到更高輸送量的磁碟分割上，不保證效能。 這就是為什麼我們強烈建議在事件中樞的資料分割數目會大於或等於輸送量單位數目。

假設您計劃需要的總輸送量，您知道您所需要的輸送量單位數目和最小數目的資料分割，但您多少個分割區應該有嗎？ 選擇您想要達成的下游平行處理原則為基礎的資料分割數目，以及您未來的輸送量需求。 沒有會收取您擁有事件中樞內的資料分割數目。

如需詳細的事件中樞價格資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

- [自動調整輸送量單位](event-hubs-auto-inflate.md)
- [事件中樞服務概觀](event-hubs-what-is-event-hubs.md)
