---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919180"
---
下表列出 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)的特定配額與限制。 如需事件中樞價格的相關資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | 影響範圍 | 注意 | 值 |
| --- | --- | --- | --- |
| 每一訂用帳戶的「事件中樞」命名空間數目 |訂用帳戶 |- |100 |
| 每個命名空間的事件中樞數目 |命名空間 |建立新的事件中樞的後續要求都會遭到拒絕。 |10 |
| 事件中樞內的資料分割數目 |實體 |- |32 |
| 每一個事件中樞取用者群組數目 |實體 |- |20 |
| 每個命名空間的 AMQP 連線數目 |命名空間 |其他連接的後續要求都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |5,000 |
| 事件中樞事件的大小上限|實體 |- |1 MB |
| 事件中樞名稱的大小上限 |實體 |- |50 個字元 |
| 每个使用者组的非 epoch 接收者数 |實體 |- |5 |
| 事件資料的最大保留期間 |實體 |- |1-7 天 |
| 最大輸送量單位 |命名空間 |超過輸送量單位限制會導致您的資料會受到節流控制，並產生[伺服器忙碌例外狀況](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 若要要求大量的輸送量單位，標準層，檔案[支援要求](/azure/azure-supportability/how-to-create-azure-support-request)。 [更多輸送量單位](../articles/event-hubs/event-hubs-auto-inflate.md)依承諾購買方式，以 20 個為一組來取得。 |20 |
| 每個命名空間的授權規則數目 |命名空間|建立授權規則的後續要求都會遭到拒絕。|12 |
