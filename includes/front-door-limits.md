---
title: 包含檔案
description: 包含檔案
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333368"
---
| 資源 | 預設值/最大限制 |
| --- | --- |
| 每個訂用帳戶的 azure 前端服務資源 | 100 |
| 前端主機，其中包含每個資源的自訂網域 | 100 |
| 每個資源的路由規則 | 100 |
| 每個資源的後端集區 | 50 |
| 每個後端集區的後端 | 100 |
| 要符合路由規則的路徑模式 | 25 |
| 每個原則的自訂 Web 應用程式防火牆規則 | 10 |
| 每個資源的 Web 應用程式防火牆原則 | 100 |
| Web 應用程式防火牆每個自訂規則的比對條件 | 10 |
| Web 應用程式防火牆的 IP 位址範圍每個比對條件 | 600 |
| Web 應用程式防火牆的字串比對每個值比對條件 | 10 |
| Web 應用程式防火牆的字串相符項目值的長度 | 256 |
| Web 應用程式防火牆 POST 主體參數名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 標頭名稱長度 | 256 |
| Web 應用程式防火牆的 cookie 名稱長度 | 256 |
| Web 應用程式防火牆 HTTP 要求主體大小檢查 | 128 KB |
| Web 應用程式防火牆的自訂回應內文長度 | 2 KB |

### <a name="timeout-values"></a>逾時值
#### <a name="client-to-front-door"></a>用戶端至 Front Door
- Front Door 的閒置 TCP 連線逾時為 61 秒。

#### <a name="front-door-to-application-back-end"></a>前端到後端的應用程式
- 如果回應是區塊的回應，還是在收到第一個區塊時，會傳回 200。
- 將 HTTP 要求轉送至後端之後，前端會等候 30 秒，讓來自後端的第一個封包。 然後它會傳回 「 503 錯誤給用戶端。
- 從後端收到第一個封包之後，前端會等候的閒置逾時為 30 秒。 然後它會傳回 「 503 錯誤給用戶端。
- 前端到後端 TCP 工作階段逾時為 30 分鐘。

### <a name="upload-and-download-data-limit"></a>上傳和下載資料限制

|  | 使用區塊傳輸編碼 (CTE) | 沒有區塊處理的 HTTP |
| ---- | ------- | ------- |
| **下載** | 下載大小沒有任何限制。 | 下載大小沒有任何限制。 |
| **上傳** |  只要每個 CTE 上傳小於 2 GB，沒有任何限制。 | 大小不得大於 2 GB。 |

### <a name="other-limits"></a>其他限制
- URL 大小上限為 8192 位元組-指定的原始 URL 的最大長度 (配置 + 主機名稱 + 連接埠 + 路徑 + 查詢 URL 字串)-最大的查詢字串大小為 4,096 個位元組-指定查詢字串，最大長度，以位元組為單位。