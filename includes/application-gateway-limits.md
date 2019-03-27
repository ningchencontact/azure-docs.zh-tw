---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494745"
---
| 資源 | 預設限制 | 附註 |
| --- | --- | --- |
| Azure 應用程式閘道 |每个订阅 1,000 个 | |
| 前端 IP 配置 |2 |公用 1 個和私用 1 個 |
| 前端連接埠 |100<sup>1</sup> | |
| 後端位址集區 |100<sup>1</sup> | |
| 每個集區的後端伺服器 |1,200 | |
| HTTP 接聽程式 |100<sup>1</sup> | |
| HTTP 负载均衡规则 |100<sup>1</sup> | |
| 後端 HTTP 設定 |100<sup>1</sup> | |
| 每個閘道的執行個體 |32 | |
| SSL 憑證 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 驗證憑證 |100 | |
| 受信任的根憑證 |100 | |
| 请求超时最小值 |1 秒 | |
| 请求超时最大值 |24 小時 | |
| 站台數目 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 每個接聽程式的 URL 對應 |1 | |
| 每個 URL 對應的路徑型規則數上限|100||
| 重新導向組態 |100<sup>1</sup>| |
| 並行的 WebSocket 連線 |中型閘道 20k<br> 大型閘道 50 k| |
| URL 長度上限|8,000||
| 檔案上傳大小上限標準 |2 GB | |
| WAF 檔案上傳大小上限 |中型 WAF 閘道、 100 MB<br>大型的 WAF 閘道、 500 MB| |
| WAF 正文大小限制，不带文件|128 KB||

<sup>1</sup> 对于启用了 WAF 的 SKU，建议将资源数限制为 40 以实现最佳性能。
