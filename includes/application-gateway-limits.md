---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211814"
---
| 資源 | 預設限制 | 附註 |
| --- | --- | --- |
| 應用程式閘道 |每個訂用帳戶 1000 個 | |
| 前端 IP 設定 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1200 | |
| HTTP 接聽程式 |100<sup>1</sup> | |
| HTTP 負載平衡規則 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |32 | |
| SSL 憑證 |100<sup>1</sup> |每個 HTTP 接聽程式 1 個 |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 要求逾時下限 |1 秒 | |
| 要求逾時上限 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每個 HTTP 接聽程式 1 個 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 並行的 WebSocket 連線 |5000| |
| URL 長度上限|8000||
| 標準檔案上傳大小上限 |2 GB | |
| WAF 檔案上傳大小上限 |中型 WAF 閘道 - 100 MB<br>大型 WAF 閘道 - 500 MB| |
| WAF 主體大小上限 (不含檔案)|128 KB||

<sup>1</sup> 若為啟用 WAF 的 SKU，建議您將資源數目限制為 40，以達到最佳效能。
