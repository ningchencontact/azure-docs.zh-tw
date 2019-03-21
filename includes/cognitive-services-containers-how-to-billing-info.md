---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964089"
---
用於 Azure 資源的定價層會計費至容器的查詢`<ApiKey>`。

認知服務容器並未授權予執行而不必連接至計費計量的端點。 客戶必須啟用要通訊與計費的端點隨時都的帳單資訊的容器。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。 

### <a name="connecting-to-azure"></a>連接到 Azure

因此容器需要用於計費的引數值，來執行。 這些值可以讓容器連接到計費的端點。 容器會每隔 10 到 15 分鐘回報使用量。 如果容器未連線至 Azure 的允許的時間範圍內，容器會繼續執行，但將不提供查詢，直到還原計費的端點。 在相同的時間間隔為 10 到 15 分鐘 10 次嘗試連線。 如果它無法連線到 10 次嘗試中計費的端點，容器就會停止執行。 

### <a name="billing-arguments"></a>計費引數

所有這三個下列選項必須指定有效的值，為了讓`docker run`命令來啟動容器：

| 選項 | 描述 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之認知服務資源的 API 金鑰。<br/>此選項的值必須設定為已佈建 `Billing` 指定資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之認知服務資源的端點。<br/>此選項的值必須設定為已佈建 LUIS Azure 資源的端點 URI。|
| `Eula` | 表示您已接受容器的授權。<br/>此選項的值必須設定為 `accept`。 |


