---
title: 如何設定 Azure SQL Database | Microsoft Docs
description: 了解如何設定與管理 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439535"
---
# <a name="how-to-use-azure-sql-database"></a>如何使用 Azure SQL Database

在本節中，您可以找到各種指南、指令碼，以及可協助您管理和設定您 Azure SQL Database 的說明。 您也可以尋找[單一資料庫](sql-database-howto-single-database.md)與[受控執行個體](sql-database-howto-managed-instance.md)的操作指南。

## <a name="load-data"></a>載入資料

- [在 Azure CLI 內複製單一資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [從 BACPAC 匯入 DB](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [將 DB 匯出到 BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [使用 BCP 載入資料](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [使用 ADF 載入資料](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>資料同步

- [SQL 資料同步](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [資料同步代理程式](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [複寫結構描述變更](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [透過 OMS 進行監視](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [資料同步最佳做法](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [資料同步疑難排解](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>監視和微調

-  [手動微調](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [使用 DMV 監視效能](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [使用查詢存放區監視效能](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [使用 Intelligent Insights 針對效能問題進行疑難排解](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [使用 Intelligent Insights 診斷記錄](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [監視記憶體內部 OLTP 空間](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>擴充事件

- [擴充事件](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [將擴充的事件儲存至事件檔案](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [將擴充的事件儲存至信號緩衝區](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>設定功能

- [設定 Azure AD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [設定條件式存取](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [多重要素 AAD 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [設定多因素驗證](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [設定時態性保留原則](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [使用 BYOK 設定 TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [旋轉 TDE BYOK 金鑰](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [移除 TDE 保護裝置](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [設定記憶體內部 OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [設定 Azure 自動化](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>開發應用程式

- [連線能力](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [使用 Spark 連接器](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [驗證應用程式](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [錯誤訊息](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [使用批次處理改善效能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [連線能力指導](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS 別名](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [DNS 別名 PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [連接埠 - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C 和 C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>設計應用程式

- [為災害復原而設計](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [為彈性集區而設計](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [為應用程式升級而設計](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>設計多租用戶 SaaS 應用程式

- [SaaS 設計模式](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS 影片索引子](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [SaaS 應用程式安全性](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>後續步驟
- 深入了解[受控執行個體中的操作指南](sql-database-howto-managed-instance.md)。
- 深入了解[單一資料庫中的操作指南](sql-database-howto-single-database.md)。
