---
title: 適用於 PostgreSQL 的 Azure 資料庫中的連線架構
description: 描述適用於 PostgreSQL 的 Azure 資料庫伺服器的連接架構。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 92844f0fe3a851802836015a1340983eb4633ed2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900558"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的連線架構
本文說明適用於 PostgreSQL 的 Azure 資料庫連線架構, 以及如何從 Azure 內部和外部的用戶端, 將流量導向至您的適用於 PostgreSQL 的 Azure 資料庫資料庫實例。

## <a name="connectivity-architecture"></a>連線架構
您的適用於 PostgreSQL 的 Azure 資料庫的連線是透過負責將連入連線路由至叢集中伺服器實體位置的閘道所建立。 下圖說明流量的流程。

![連線性架構的總覽](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

當用戶端連接到資料庫時, 它們會取得連接到閘道的連接字串。 此閘道的公用 IP 位址會接聽埠5432。 在資料庫叢集中, 會將流量轉送到適當的適用於 PostgreSQL 的 Azure 資料庫。 因此, 為了連接到您的伺服器 (例如從公司網路), 必須開啟用戶端防火牆, 以允許輸出流量能夠連線到我們的閘道。 您可以在下方找到每個區域的閘道所使用的 IP 位址的完整清單。

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>適用於 PostgreSQL 的 Azure 資料庫閘道 IP 位址
下表列出所有資料區域之適用於 PostgreSQL 的 Azure 資料庫閘道的主要和次要 Ip。 主要 IP 位址是閘道目前的 IP 位址, 而第二個 IP 位址是容錯移轉的 IP 位址 (萬一主要複本失敗時)。 如前所述, 客戶應允許輸出到這兩個 IP 位址。 第二個 IP 位址不會在任何服務上接聽, 直到適用於 PostgreSQL 的 Azure 資料庫啟用以接受連線為止。

| **區功能變數名稱稱** | **主要 IP 位址** | **次要 IP 位址** |
|:----------------|:-------------|:------------------------|
| 澳大利亞東部 | 13.75.149.87 | 40.79.161.1 |
| 澳大利亞東南部 | 191.239.192.109 | 13.73.109.251 |
| 巴西南部 | 104.41.11.5 | |
| 加拿大中部 | 40.85.224.249 | |
| 加拿大東部 | 40.86.226.166 | |
| 美國中部 | 23.99.160.139 | 13.67.215.62 |
| 中國東部 1 | 139.219.130.35 | |
| 中國東部 2 | 40.73.82.1 | |
| 中國北部 1 | 139.219.15.17 | |
| 中國北部 2 | 40.73.50.0 | |
| 東亞 | 191.234.2.139 | 52.175.33.150 |
| 美國東部 1 | 191.238.6.43 | 40.121.158.30 |
| 美國東部 2 | 191.239.224.107 | 40.79.84.180 * |
| 法國中部 | 40.79.137.0 | 40.79.129.1 |
| 德國中部 | 51.4.144.100 | |
| 印度中部 | 104.211.96.159 | |
| 印度南部 | 104.211.224.146 | |
| 印度西部 | 104.211.160.80 | |
| 日本東部 | 191.237.240.43 | 13.78.61.196 |
| 日本西部 | 191.238.68.11 | 104.214.148.156 |
| 南韓中部 | 52.231.32.42 | |
| 南韓南部 | 52.231.200.86 |  |
| 美國中北部 | 23.98.55.75 | 23.96.178.199 |
| 北歐 | 191.235.193.75 | 40.113.93.91 |
| 美國中南部 | 23.98.162.75 | 13.66.62.124 |
| 東南亞 | 23.100.117.95 | 104.43.15.0 |
| 英國南部 | 51.140.184.11 | |
| 英國西部 | 51.141.8.11| |
| 西歐 | 191.237.232.75 | 40.68.37.158 |
| 美國西部 1 | 23.99.34.75 | 104.42.238.205 |
| 美國西部 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *美國東部 2* 也有第三 IP 位址 `52.167.104.0`。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](./howto-manage-firewall-using-cli.md)
