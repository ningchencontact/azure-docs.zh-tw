---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66689113"
---
| 資源 | 預設值/最大限制 | 附註 |
| --- | --- | --- |
| Azure 應用程式閘道 |訂用帳戶每 1,000 個 | |
| 前端 IP 組態 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1,200 | |
| HTTP 接聽程式 |100<sup>1</sup> | |
| HTTP 負載平衡規則 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |32 | |
| SSL 憑證 |100<sup>1</sup> |每個 HTTP 接聽程式 1 |
| SSL 憑證大小上限 |V1 SKU-10 KB<br>V2 SKU-25 KB| |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 最小的要求逾時 |1 秒 | |
| 要求逾時上限 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每個 HTTP 接聽程式 1 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 並行的 WebSocket 連線 |中型閘道 20k<br> 大型閘道 50 k| |
| URL 長度上限|8,000||
| 檔案上傳大小上限標準 |2 GB | |
| WAF 檔案上傳大小上限 |中型 WAF 閘道、 100 MB<br>大型的 WAF 閘道、 500 MB| |
| WAF 主體大小上限，如果沒有檔案|128 KB||
|最大的 WAF 自訂規則|100||

<sup>1</sup>時已啟用 WAF 的 Sku，我們建議您限制為 40，以獲得最佳效能的資源數目。
