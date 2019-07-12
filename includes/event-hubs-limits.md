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
ms.openlocfilehash: b105fb14608d53c5c2ef469ab44e211ccdf4d3c8
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812546"
---
下表列出 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)的特定配額與限制。 如需事件中樞價格的相關資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | `Scope` | 注意 | 值 |
| --- | --- | --- | --- |
| 每一訂用帳戶的「事件中樞」命名空間數目 |Subscription |- |100 |
| 每個命名空間的事件中樞數目 |命名空間 |建立新的事件中樞的後續要求都會遭到拒絕。 |10 |
| 事件中樞內的資料分割數目 |實體 |- |32 |
| 每一個事件中樞取用者群組數目 |實體 |- |20 |
| 每個命名空間的 AMQP 連線數目 |命名空間 |其他連接的後續要求都會遭到拒絕，並呼叫程式碼會收到例外狀況。 |5,000 |
| 事件中樞事件的大小上限|實體 |- |1 MB |
| 事件中樞名稱的大小上限 |實體 |- |50 個字元 |
| 每個取用者群組的非 epoch 接收者數目 |實體 |- |5 |
| 事件資料的最大保留期間 |實體 |- |1-7 天 |
| 最大輸送量單位 |命名空間 |超過輸送量單位限制會導致您的資料會受到節流控制，並產生[伺服器忙碌例外狀況](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 若要要求大量的輸送量單位，標準層，檔案[支援要求](/azure/azure-supportability/how-to-create-azure-support-request)。 [更多輸送量單位](../articles/event-hubs/event-hubs-auto-inflate.md)依承諾購買方式，以 20 個為一組來取得。 |20 |
| 每個命名空間的授權規則數目 |命名空間|建立授權規則的後續要求都會遭到拒絕。|12 |
| GetRuntimeInformation 方法的呼叫數 | 實體 | - | 每秒 50 | 
| 虛擬網路 (VNet) 和 IP 設定的規則數目 | 實體 | - | 128 | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中樞專用-配額和限制
事件中樞專用供應項目是固定的每月價格，並至少 4 小時的使用量計費。 專用的層提供所有功能的 「 標準 」 方案，但企業規模容量和限制嚴苛的工作負載的客戶。 

| 功能 | 限制 |
| --- | ---|
| 頻寬 |  20 Cu |
| 命名空間 | 每個 CU 50 |
| 事件中樞 |  每個命名空間的 1000 |
| 輸入事件 | 已包括 |
| 訊息大小 | 1 百萬個位元組 |
| 分割數 | 每個 CU 2000 |
| 用戶群組 | 無限制每個 CU，每一個事件中樞的 1000 |
| 代理連線 | 包含 10 萬個 |
| 訊息保留期 | 設定為 7 天 （90 天的保留即將推出），10 TB 包含每個 CU |
| 擷取 | 已包括 |
