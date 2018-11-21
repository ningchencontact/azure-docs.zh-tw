---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628104"
---
| 資源 | 預設限制 | 附註 |
| --- | --- | --- |
| 應用程式閘道 |每個訂用帳戶 1000 個 | |
| 前端 IP 組態 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |40 | |
| 後端位址集區 |40 | |
| 每個集區的後端伺服器 |1200 | |
| HTTP 接聽程式 |40 | |
| HTTP 負載平衡規則 |400 |HTTP 接聽程式數目 * n |
| 後端 HTTP 設定 |40 | |
| 每個閘道的執行個體 |75 | |
| SSL 憑證 |40 |每個 HTTP 接聽程式 1 個 |
| 驗證憑證 |40 | |
| 要求逾時下限 |1 秒 | |
| 要求逾時上限 |24 小時 | |
| 站台數目 |20 |每個 HTTP 接聽程式 1 個 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100|
| 重新導向組態 |40| |
| 並行的 WebSocket 連線 |5000| |
|URL 長度上限|8000|
| 標準檔案上傳大小上限 |2 GB | |
| WAF 檔案上傳大小上限 |中型 WAF 閘道 - 100 MB<br>大型 WAF 閘道 - 500 MB| |
|WAF 主體大小上限 (不含檔案)|128 KB|
