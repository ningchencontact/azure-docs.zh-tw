---
title: Azure SQL Database 從 Gen2 到 Gen3 的閘道遷移通知 |Microsoft Docs
description: 文章提供有關遷移 Azure SQL Database 閘道 IP 位址的使用者通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568110"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database 流量遷移至較新的閘道

隨著 Azure 基礎結構的改善, Microsoft 會定期重新整理硬體, 以確保我們能提供最佳的客戶體驗。 在接下來的幾個月, 我們打算新增以較新硬體世代為基礎的閘道, 並解除委任在某些區域中的舊硬體上建立的閘道。  

客戶將會在每個區域中可用的閘道變更之前, 透過電子郵件和 Azure 入口網站中的通知。 最新的資訊將會保留在 [ [Azure SQL Database 閘道 IP 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)] 資料表中。

## <a name="impact-of-this-change"></a>這項變更的影響

第一輪的閘道解除委任已排程于下欄區域中2019年9月1日:

- 美國西部
- 西歐
- East US
- 美國中部
- 東南亞
- 美國中南部
- 北歐
- 美國中北部
- 日本西部
- 日本東部
- 美國東部 2
- 東亞

已解除委任的 IP 位址將會停止接受流量, 而且任何新的連線嘗試都會路由傳送到該區域中的其中一個閘道。

您不會看到這項變更的影響:

- 使用重新導向作為其連線原則的客戶不會看到任何影響。
- 從 Azure 內部和使用服務標籤的 SQL Database 連線不會受到影響。
- 使用支援的 JDBC Driver for SQL Server 版本所建立的連接將不會有任何影響。 如需支援的 JDBC 版本, 請參閱[下載適用于 SQL Server 的 MICROSOFT JDBC Driver](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果受到影響, 該怎麼辦？

針對 TCP 通訊埠1433上的區域中的所有[Azure SQL Database 閘道 ip 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses), 以及防火牆裝置中的埠範圍 11000-11999, 建議您允許連入 ip 位址的連出流量。 如需埠範圍的詳細資訊, 請參閱連線[原則](sql-database-connectivity-architecture.md#connection-policy)。

使用 Microsoft JDBC Driver 4.0 版的應用程式所進行的連接可能會使憑證驗證失敗。 較低版本的 Microsoft JDBC 會依賴憑證的 [主旨] 欄位中的一般名稱 (CN)。 緩和措施是確保 hostNameInCertificate 屬性設定為 *. database.windows.net。 如需有關如何設定 hostNameInCertificate 屬性的詳細資訊, 請參閱[使用 SSL 加密進行連接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述緩和措施無法解決問題, 請使用下列 URL 提出 SQL Database 的支援要求: https://aka.ms/getazuresupport

## <a name="next-steps"></a>後續步驟

- 深入瞭解[AZURE SQL 連線架構](sql-database-connectivity-architecture.md)