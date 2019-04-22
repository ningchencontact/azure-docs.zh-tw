---
title: 進階資料安全性 - Azure SQL Database | Microsoft Docs
description: 了解探索及分類敏感性資料、管理資料庫弱點，以及偵測可能表示對 Azure SQL 資料庫有威脅之異常活動的功能。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: monhaber
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: a078ac38cef5b395a19481188c474c7f908160d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784478"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database 的進階資料安全性

進階資料安全性是進階 SQL 安全性功能的整合套件。 其中包含的功能可用於探索和分類敏感性資料、找出潛在資料庫弱點並減低其風險，及偵測可能指出資料庫遇到威脅的異常活動。 此套件可讓您從單一的進入點位置啟用及管理前述功能。

## <a name="overview"></a>概觀

高级数据安全性 (ADS) 提供一组高级 SQL 安全功能，包括数据发现和分类、漏洞评估和高级威胁防护。

- [資料探索與分類](sql-database-data-discovery-and-classification.md) (目前處於預覽階段) 提供內建於 Azure SQL Database 的功能，可用來探索、分類、標記和保護資料庫中的敏感性資料。 它可用來讓您檢視資料庫分類狀態、追蹤對資料庫內敏感性資料的存取，並具有其他多方面的用途。
- [弱點評估](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它可讓您檢視安全性狀態，且包含解決安全性問題和增強資料庫防護性的可行步驟。
- [高级威胁防护](sql-database-threat-detection-overview.md)检测异常活动，指出尝试访问或利用数据库的行为异常且可能有害。 它會持續監視您的資料庫是否有可疑的活動，並在發現潛在弱點、SQL 插入式攻擊和異常資料庫存取模式時提供即時安全性警示。 高级威胁防护警报提供可疑活动的详细信息，并建议如何调查和缓解威胁。

只要啟用 SQL ADS，其中包含的所有功能即會一併啟用。 只要按一下按鍵，即可對 SQL Database 伺服器或受控執行個體上的所有資料庫啟用 ADS。 啟用或管理 ADS 設定需要屬於 [SQL 安全性管理員角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)、SQL 資料庫管理員角色或 SQL Server 管理員角色。 

ADS 的定價與 Azure 資訊安全中心標準層相同，每個受保護的 SQL Database 伺服器或受控執行個體算作一個節點。 新的受保護資源適用資訊安全中心標準層的免費試用。 如需詳細資訊，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-ads"></a>開始使用 ADS

下列步驟可讓您開始使用 ADS。

## <a name="1-enable-ads"></a>1.啟用 ADS

針對您的 SQL Database 伺服器或受控執行個體，瀏覽至 [安全性] 標題下的 [進階資料安全性]，以啟用 ADS。 若要對資料庫伺服器或受控執行個體上的所有資料庫啟用 ADS，請按一下 [在伺服器上啟用進階資料安全性]。

> [!NOTE]
> 系统会自动创建一个存储帐户用于存储**漏洞评估**的扫描结果。 如果为同一个资源组和区域中的另一台服务器启用了 ADS，则使用现有的存储帐户。

![啟用 ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS 的費用與每個節點的 Azure 資訊安全中心標準層費用相同，其中一個節點代表整個 SQL Database 伺服器或受控執行個體。 因此，您只需付費一次，即可使用 ADS 保護資料庫伺服器或受控執行個體上的所有資料庫。 您可以先以免費試用版試用 ADS。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2.開始分類資料、追蹤弱點，並調查威脅警示

按一下 [資料探索與分類] 卡可查看建議要分類的敏感性資料行，以及使用持續敏感性標籤為資料分類。 按一下 [弱點評估] 卡可檢視和管理弱點掃描和報告，以及追蹤您的安全性水準。 如果收到安全性警示，按一下**進階威脅防護**卡，以檢視詳細資料的警示，並查看您透過 [Azure 資訊安全中心的安全性警示] 頁面的 Azure 訂用帳戶中的所有警示的彙總的報表.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3.管理 SQL Database 伺服器或受控執行個體的 ADS 設定

若要檢視及管理 ADS 設定，請針對您的 SQL Database 伺服器或受控執行個體，瀏覽至 [安全性] 標題下的 [進階資料安全性]。 在此頁面上，您可以啟用或停用廣告，並修改的弱點可能會評估和您的整個 SQL Database 伺服器或受管理的執行個體的進階威脅防護設定。

![伺服器設定](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4.管理 SQL 資料庫的 ADS 設定

若要覆寫特定資料庫的 ADS 設定，請勾選 [啟用資料庫層級的進階資料安全性] 核取方塊。 使用此選項，只有當您有特定的需求，要接收不同的進階威脅防護通知或針對個別的資料庫，取代或是再加上收到的所有資料庫的結果與警示的弱點可能會評估結果資料庫伺服器或受管理的執行個體。

选中该复选框后，可以配置此数据库的相关设置。
 
![数据库和高级威胁防护设置](./media/sql-advanced-protection/database_threat_detection_settings.png) 

從 ADS 資料庫窗格，也可以觸達資料庫伺服器或受控執行個體的進階資料安全性設定。 按一下主要 ADS 窗格中的 [設定]，然後按一下 [檢視進階資料安全性伺服器設定]。 

![資料庫設定](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>後續步驟 

- 深入了解[資料探索與分類](sql-database-data-discovery-and-classification.md) 
- 深入了解[弱點評估](sql-vulnerability-assessment.md) 
- 详细了解[高级威胁防护](sql-database-threat-detection.md)
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
