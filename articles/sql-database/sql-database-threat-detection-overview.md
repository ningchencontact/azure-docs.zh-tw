---
title: 進階威脅防護 - Azure SQL 資料庫 | Microsoft Docs
description: 高级威胁防护会检测异常的数据库活动，指出 Azure SQL 数据库中的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797059"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 資料庫的進階威脅防護

适用于 [Azure SQL 数据库](sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的高级威胁防护可检测异常活动，这些活动指示对数据库的异常和可能有害的访问或利用企图。

高级威胁防护包含在[高级数据安全性](sql-database-advanced-data-security.md) (ADS) 产品/服务中，是一个针对高级 SQL 安全功能的统一软件包。 可通过中心 SQL ADS 门户访问和管理高级威胁防护。

> [!NOTE]
> 本主題適用於 Azure SQL 伺服器，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护

 高级威胁防护提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 進階的威脅防護整合了警示與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)，這包含可疑活動的詳細資料，以及如何調查與降低威脅的建議。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用高级威胁防护轻松解决数据库的潜在威胁。

如需完整的調查體驗，建議您啟用 [SQL Database 稽核](sql-database-auditing.md)，這會將資料庫事件寫入您 Azure 儲存體帳戶中的稽核記錄。  

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示

适用于 Azure SQL 数据库的高级威胁防护可检测异常活动（指示异常和可能有害的数据库访问或使用企图），并可触发以下警报：

- **SQL 插入式攻擊的弱點**：應用程式在資料庫中產生錯誤的 SQL 陳述式時，會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。 錯誤的陳述式之所以產生，有兩項可能的原因：

  - 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
  - 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
- **潛在的 SQL 插入式攻擊**：若有主動攻擊是藉由 SQL 插入中已識別到的已知應用程式弱點來發動時，就會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。
- **從不尋常的位置存取**：有人從不尋常的地理位置登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從不尋常的 Azure 資料中心存取**：有人從不尋常的 Azure 資料中心登入 SQL Server (在近期曾在此伺服器上發現此資料中心)，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (您在 Azure、Power BI、Azure SQL 查詢編輯器中使用新的應用程式)。 在其他情況下，警示則是偵測來自 Azure 資源/服務的惡意動作 (離職員工、外部攻擊者)。
- **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。
- **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>偵測到可疑事件時探索異常資料庫活動

偵測到異常資料庫活動時，您會收到電子郵件通知。 電子郵件會提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱、應用程式名稱和事件時間。 此外，該電子郵件還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。

![異常活動報告](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. 按一下電子郵件中的 [檢視最近的 SQL 警示] 連結來啟動 Azure 入口網站，並顯示 Azure 資訊安全中心警示頁面，其中會概述在 SQL 資料庫上偵測到的作用中威脅。

   ![活動威脅](./media/sql-database-threat-detection/active_threats.png)

2. 按一下特定警示可取得其他詳細資料和調查此威脅的建議，並對未來的威脅採取補救措施。

   例如，SQL 插入式攻擊是網際網路上最常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，破壞或修改資料庫中的資料。 針對 SQL 插入式攻擊，警示的詳細資料會包括已遭利用且有弱點的 SQL 陳述式。

   ![特定警示](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 门户中浏览针对数据库发出的高级威胁防护警报

進階的威脅防護整合自有的警示與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)。 Azure 门户中“数据库和 SQL ADS”边栏选项卡内的“实时 SQL 高级威胁防护”磁贴会跟踪活动威胁的状态。

单击“高级威胁防护警报”以启动“Azure 安全中心警报”页，并获取在数据库或数据仓库中检测到的活动 SQL 威胁的概述。

   ![高级威胁防护警报](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![高级威胁防护警报 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>後續步驟

- 详细了解[单一数据库和共用数据库中的高级威胁防护](sql-database-threat-detection.md)。
- 深入了解[受管理的執行個體中的進階威脅防護](sql-database-managed-instance-threat-detection.md)。
- 详细了解[高级数据安全性](sql-database-advanced-data-security.md)。
- 深入了解 [Azure SQL Database 稽核](sql-database-auditing.md)
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需有關定價的詳細資訊，請參閱 [SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
