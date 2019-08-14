---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912317"
---
下表列出 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)的特定配額與限制。 如需事件中樞價格的相關資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | `Scope` | 注意 | 值 |
| --- | --- | --- | --- |
| 每一訂用帳戶的「事件中樞」命名空間數目 |訂閱 |- |100 |
| 每個命名空間的事件中樞數目 |命名空間 |建立新事件中樞的後續要求會遭到拒絕。 |10 |
| 事件中樞內的資料分割數目 |實體 |- |32 |
| 事件中樞事件的大小上限|實體 |- |1 MB |
| 事件中樞名稱的大小上限 |實體 |- |50 個字元 |
| 每個取用者群組的非 epoch 接收者數目 |實體 |- |5 |
| 最大輸送量單位 |命名空間 |超過輸送量單位限制會導致您的資料受到節流, 並產生[伺服器忙碌的例外](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)狀況。 若要為標準層要求大量的輸送量單位, 請提出[支援要求](/azure/azure-supportability/how-to-create-azure-support-request)。 [更多輸送量單位](../articles/event-hubs/event-hubs-auto-inflate.md)依承諾購買方式，以 20 個為一組來取得。 |20 |
| 每個命名空間的授權規則數目 |命名空間|授權規則建立的後續要求會遭到拒絕。|12 |
| GetRuntimeInformation 方法的呼叫次數 | 實體 | - | 每秒50 | 
| 虛擬網路 (VNet) 和 IP 設定規則的數目 | 實體 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>事件中樞基本和標準-配額和限制
| 限制 | `Scope` | 注意 | 基本 | 標準 |
| --- | --- | --- | -- | --- |
| 每一個事件中樞取用者群組數目 |實體 | - |1 |20 |
| 每個命名空間的 AMQP 連線數目 |命名空間 |後續的其他連接要求會遭到拒絕, 而且呼叫程式碼會收到例外狀況。 |100 |5,000|
| 事件資料的最大保留期間 |實體 | - |1 天 |1-7 天 |
|已啟用 Apache Kafka 的命名空間|命名空間 |使用 Kafka 通訊協定事件中樞命名空間串流應用程式 |否 | 是 |
|擷取 |實體 | 啟用時, 相同資料流程上的微批次 |否 |是 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中樞專用-配額和限制
事件中樞專用供應專案以固定的每月價格計費, 最低使用量為4小時。 專用層提供標準方案的所有功能, 但具備企業級的容量和限制, 可滿足具有工作負載需求的客戶。 

| 功能 | 限制 |
| --- | ---|
| 頻寬 |  20 Cu |
| 命名空間 | 50/CU |
| 事件中樞 |  1000每個命名空間 |
| 輸入事件 | 內含 |
| 訊息大小 | 1000000個位元組 |
| 分割區 | 2000/CU |
| 取用者群組 | 每個事件中樞不會有每個 CU 的限制 (1000) |
| 代理連線 | 包含 10 萬個 |
| 訊息保留期 | 最多7天 (即將推出90天保留期), 每個 CU 包含 10 TB |
| 擷取 | 內含 |
