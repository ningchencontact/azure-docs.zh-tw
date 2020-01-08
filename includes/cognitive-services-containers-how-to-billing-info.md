---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704328"
---
對容器的查詢會以用於 `<ApiKey>`之 Azure 資源的定價層計費。

Azure 認知服務容器在未連線至計費端點以進行計量的情況下，將無法被授權以執行。 您必須讓容器隨時都能與計量端點進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。 

### <a name="connect-to-azure"></a>連線到 Azure

容器需要計費引數值才能執行。 這些值讓容器能夠連線到計費端點。 容器會每隔 10 到 15 分鐘回報使用量。 如果容器未在允許的時間範圍內連線到 Azure，容器會繼續執行，但在還原計費端點之前不會提供查詢。 以 10 到 15 分鐘的相同時間間隔嘗試連線 10 次。 如果無法在 10 次嘗試內連線到計費端點，則容器會停止執行。 

### <a name="billing-arguments"></a>計費引數

若要讓 `docker run` 命令啟動容器，必須以有效值指定下列三個選項：

| 選項 | 說明 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之認知服務資源的 API 金鑰。<br/>此選項的值必須設定為 `Billing`中指定之已布建資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之認知服務資源的端點。<br/>此選項的值必須設定為已佈建 Azure 資源的端點 URI。|
| `Eula` | 表示您接受容器的授權。<br/>此選項的值必須設定為 [**接受**]。 |


