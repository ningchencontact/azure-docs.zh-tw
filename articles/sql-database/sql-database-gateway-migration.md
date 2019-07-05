---
title: 適用於 Azure SQL Database 從 Gen2 Gen3 的閘道移轉注意事項 |Microsoft Docs
description: 篇文章提供有關 Azure SQL Database 閘道 IP 位址移轉的使用者通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538376"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database 流量移轉到較新的閘道

為改善 Azure 基礎結構，則 Microsoft 會定期重新整理硬體，以確保我們能夠提供最佳的客戶體驗。 接下來的幾個月，我們將計劃要新增閘道會根據較新世代的硬體，並解除委任某些區域中的舊硬體上建置閘道。  

透過電子郵件，並在 Azure 入口網站中部署可用的閘道，每個區域中的任何變更之前，客戶會收到通知。 最新的資訊都將保留在[Azure SQL Database 閘道 IP 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)資料表。

## <a name="impact-of-this-change"></a>此變更的影響

解除委任閘道第一回合已排定於 2019 年 9 月 1 日在下列區域：

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

解除委任的 IP 位址將會停止接受流量，和任何新的連線嘗試將會路由至其中一個區域中的閘道。

其中，您不會看到此變更的影響：

- 使用重新導向，當其連線原則不會看到任何影響的客戶。
- 連線到 SQL Database 從在 Azure 和使用服務標籤並不會影響。
- 使用適用於 SQL Server 的 JDBC 驅動程式支援的版本建立的連接將會看到任何影響。 如需支援 JDBC 版本，請參閱[下載 Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果您有影響，您該怎麼做

我們建議您為所有允許的 IP 位址的輸出流量[Azure SQL Database 閘道 IP 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)在區域上 TCP 通訊埠 1433，及連接埠範圍 11000-11999 防火牆裝置中。 如需有關連接埠範圍的詳細資訊，請參閱[連線原則](sql-database-connectivity-architecture.md#connection-policy)。

低於 4.0 的版本中使用 Microsoft JDBC Driver 從應用程式所建立的連接可能會讓憑證驗證失敗。 較低版本的 Microsoft JDBC 依賴在一般名稱 (CN) 憑證的 [主旨] 欄位中。 緩和措施可確保 hostNameInCertificate 屬性設為 *。 database.windows.net。 如需有關如何設定 hostNameInCertificate 屬性的詳細資訊，請參閱 <<c0> [ 使用 SSL 加密連接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述的緩和措施無法運作，請使用下列 URL 的 SQL Database 的申請支援要求： https://aka.ms/getazuresupport

## <a name="next-steps"></a>後續步驟

- 深入了解[Azure SQL 連線架構](sql-database-connectivity-architecture.md)