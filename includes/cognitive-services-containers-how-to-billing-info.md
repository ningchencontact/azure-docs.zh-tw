---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: ce7d8628c28a4a202a05aeea60e71655b4b7f1a2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984886"
---
認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (語句) 傳送至 Microsoft。 容器會每隔 10 到 15 分鐘回報使用量。

`docker run` 會使用下列引數進行計費：

| 選項 | 說明 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之認知服務資源的 API 金鑰。<br/>此選項的值必須設定為已佈建 `Billing` 指定資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之認知服務資源的端點。<br/>此選項的值必須設定為已佈建 LUIS Azure 資源的端點 URI。|
| `Eula` | 表示您已接受容器的授權。<br/>此選項的值必須設定為 `accept`。 |

> [!IMPORTANT]
> 這三個選項都必須指定為有效的值，否則將無法啟動容器。
