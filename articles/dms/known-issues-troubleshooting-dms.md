---
title: 有關疑難排解常見的問題/錯誤與使用 Azure 資料庫移轉服務相關聯的已知文章 |Microsoft Docs
description: 深入了解如何疑難排解常見已知的問題/錯誤與使用 Azure 資料庫移轉服務相關聯。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: 1d639a8b1d5c7a5dd2b7bac7c5e020be7c8b1c50
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190952"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>疑難排解常見的 Azure 資料庫移轉服務的問題和錯誤

本文說明一些常見的問題和 Azure 資料庫移轉服務的使用者可能會遇到的錯誤。 本文也包含有關如何解決這些問題和錯誤的資訊。

## <a name="migration-activity-in-queued-state"></a>已排入佇列的狀態中的移轉活動

當您的 Azure 資料庫移轉服務專案中建立新的活動時，活動會維持在已排入佇列的狀態。

| 原因         | 解決方案 |
| ------------- | ------------- |
| Azure 資料庫移轉服務執行個體達到最大容量的進行中同時執行的工作時，就會發生這個問題。 任何新的活動已排入佇列，直到可用的容量。 | 驗證資料移轉服務執行個體已執行跨專案的活動。 您可以繼續建立新的活動，會自動新增至執行的佇列。 只要任何現有執行中的活動完成時下, 一個已排入佇列的活動開始執行和狀態會變更為自動執行狀態。 您不需要採取任何額外的動作，來開始移轉已排入佇列的活動。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>選取要移轉資料庫的最大數目

建立活動資料庫移轉專案移至 Azure SQL Database 或 Azure SQL Database 受控執行個體時，就會發生下列錯誤：

* **錯誤**：移轉設定驗證錯誤 」、 「 errorDetail":"超過最大數目 '4' 物件的 [資料庫] 已選取進行移轉。 」

| 原因         | 解決方案 |
| ------------- | ------------- |
| 您已選取單一的移轉活動的四個以上資料庫時，會顯示此錯誤。 目前，每個移轉活動則會限制為 4 個資料庫項目。 | 選取四個或更少的資料庫，每個移轉活動。 如果您需要四個以上同時移轉資料庫，佈建 Azure 資料庫移轉服務的另一個執行的個體。 目前，每個訂用帳戶支援最多兩個的 Azure 資料庫移轉服務執行個體。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>MySQL 移轉至 Azure MySQL 復原失敗的錯誤

移轉時從 MySQL 到 Azure Database for MySQL 使用 Azure 資料庫移轉服務，移轉活動失敗，發生下列錯誤：

* **錯誤**：資料庫移轉錯誤-'TaskID' 工作已因 [n] 的後續復原失敗而停用。

| 原因         | 解決方案 |
| ------------- | ------------- |
| 在移轉使用者遺漏 ReplicationAdmin 角色和 （或） 的複寫用戶端、 複寫複本和超級 （MySQL 5.6.6 之前的版本） 的權限時，可能會發生這個錯誤。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 請確定[必要的權限](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)使用者帳戶已正確上 Azure Database for MySQL 執行個體。 例如，若要建立名為 'migrateuser' 與必要權限的使用者可以遵循下列步驟：<br>1.CREATE USER migrateuser@'%' 識別的 'secret'; <br>2.授與 'migrateuser'@'%' 'secret'; 所識別的 db_name.* 的所有權限重複此步驟，授與在多個資料庫的存取權 <br>3.在授與複寫從屬 *。* 若要識別 'secret'; 'migrateuser'@'%'<br>4.授與複寫用戶端上的 *。* 若要識別 'secret'; 'migrateuser'@'%'<br>5.排清的權限; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>正在嘗試停止 Azure 資料庫移轉服務時發生錯誤

您會收到下列錯誤時停止的 Azure 資料庫移轉服務執行個體：

* **錯誤**：服務無法停止。 錯誤: {'error': {'code': 'InvalidRequest'、 'message':' 目前正在執行一個或多個活動。 若要停止服務，等到活動已完成，或手動停止這些活動，並再試一次。 '}}

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當您嘗試停止的服務執行個體包含在移轉專案中的活動，仍在執行，或出現時，則會顯示此錯誤。 <br><br><br><br><br><br> | 請確定沒有任何您想要停止的 Azure 資料庫移轉服務的執行個體中執行的活動。 您也可能會嘗試停止服務之前，刪除活動或專案。 下列步驟說明如何移除專案，以清除 刪除所有執行中工作的 移轉服務執行個體：<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4.Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>嘗試啟動 Azure 資料庫移轉服務時發生錯誤

您會收到下列錯誤開始的 Azure 資料庫移轉服務執行個體時：

* **錯誤**：服務無法啟動。 錯誤: {'errorDetail': '無法啟動，請連絡 Microsoft 支援服務'}

| 原因         | 解決方案 |
| ------------- | ------------- |
| 當先前的執行個體無法在內部，則會顯示此錯誤。 很少會發生這個錯誤，工程小組會知道它。 <br> | 刪除服務無法啟動，，然後佈建新項目來取代它的執行個體。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>還原資料庫時移轉 SQL 到 Azure SQL DB 受控執行個體時發生錯誤

當您從 SQL Server 執行線上移轉至 Azure SQL Database 受控執行個體時，完全移轉失敗，發生下列錯誤：

* **錯誤**：還原作業失敗。 作業識別碼 'operationId'。 程式碼 'AuthorizationFailed'，訊息 'client 'clientId'，物件識別碼為 'objectId' 並沒有執行動作 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' 範圍' /subscriptions/subscriptionId'。 '。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 此錯誤表示用於從 SQL Server 的線上移轉至 Azure SQL Database 受控執行個體的應用程式主體不提供訂用帳戶的權限。 目前與受控執行個體的特定 API 呼叫會需要此權限，訂用帳戶上的，還原作業。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用`Get-AzureADServicePrincipal`PowerShell cmdlet 搭配`-ObjectId`提供的錯誤訊息，列出所使用的應用程式識別碼的顯示名稱。<br><br> 驗證此應用程式的權限，並確定它具有[參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)訂用帳戶層級。 <br><br> Azure 資料庫移轉服務工程小組努力限制需要從目前的存取權提供訂用帳戶上的角色。 如果您有不允許使用的商務需求提供角色、 連絡 Azure 支援人員以取得其他協助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>刪除 NIC 相關聯 Azure 資料庫移轉服務時發生錯誤

當您嘗試刪除 Azure 資料庫移轉服務相關聯的網路介面卡時，嘗試刪除失敗會發生此錯誤：

* **錯誤**：無法刪除相關聯的 Azure 資料庫移轉服務因為 DMS 服務使用的 NIC 的 NIC

| 原因         | 解決方案    |
| ------------- | ------------- |
| Azure 資料庫移轉服務執行個體仍可能出現時，會發生這個問題及使用的 nic。 <br><br><br><br><br><br><br><br> | 若要刪除此 NIC，請刪除 DMS 服務執行個體，會自動刪除該服務所使用的 NIC。<br><br> **重要说明**：確定要刪除的 Azure 資料庫移轉服務執行個體具有任何執行中的活動。<br><br> 刪除所有的專案和 Azure 資料庫移轉服務執行個體相關聯的活動之後，您可以刪除服務執行個體。 刪除服務的一部分，會自動清除服務執行個體所使用的 NIC。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 時的連線錯誤

當您嘗試連接到 Azure 資料庫移轉服務專案精靈 中的來源時，如果來源使用 ExpressRoute 連線的連線會失敗之後延長逾時。

| 原因         | 解決方案    |
| ------------- | ------------- |
| 使用時[ExpressRoute](https://azure.microsoft.com/services/expressroute/)，Azure 資料庫移轉服務[需要](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)佈建服務相關聯的虛擬網路子網路上的三個服務端點：<br> --服務匯流排端點<br> -儲存體端點<br> -目標資料庫端點 （例如 SQL 端點，Cosmos DB 端點）<br><br><br><br><br> | [啟用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)來源和 Azure 資料庫移轉服務之間的 ExpressRoute 連線能力的所需的服務端點。 <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>將 MySQL 資料庫移轉至 Azure MySQL 時的逾時錯誤

當您移轉到 Azure Database for MySQL 執行個體，透過 Azure 資料庫移轉服務的 MySQL 資料庫時，移轉會失敗，發生下列的逾時錯誤：

* **錯誤**：資料庫移轉錯誤-無法載入檔案-無法啟動檔案的載入程序 'n' RetCode:SQL_ERROR SqlState:HY000 NativeError:1205 訊息: [MySQL] [ODBC Driver] [mysqld] 鎖定等候的逾時超過;請嘗試重新啟動的交易

| 原因         | 解決方案    |
| ------------- | ------------- |
| 當在移轉期間移轉失敗時因為鎖定等候逾時，就會發生此錯誤。 | 請考慮增加伺服器參數的值 **'innodb_lock_wait_timeout'** 。 允許的最大值是 1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>使用動態連接埠時，連接至來源 SQL Server 或具名執行個體時發生錯誤

當您嘗試連接到具名執行個體或動態連接埠執行的 SQL Server 來源的 Azure 資料庫移轉服務時，連接失敗會發生此錯誤：

* **錯誤**:-1-失敗的 SQL 連接。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認執行個體名稱正確，且 SQL Server 設定為允許遠端連接。 (提供者：SQL 網路介面，錯誤：26-尋找指定的伺服器/執行個體時發生錯誤)

| 原因         | 解決方案    |
| ------------- | ------------- |
| Azure 資料庫移轉服務會嘗試連接至來源 SQL Server 執行個體具有動態連接埠，或使用具名執行個體時，就會發生這個問題。 使用動態連接埠時，SQL Server Browser 服務會接聽內送連接到具名執行個體或 UDP 連接埠 1434年。 動態連接埠可能會變更每個 SQL Server 服務重新啟動的時間。 您可以檢查指派給執行個體，透過 SQL Server 組態管理員 」 中的網路設定的動態連接埠。<br><br><br> |確認 Azure 資料庫移轉服務可以連線到來源 UDP 通訊埠 1434年的 SQL Server Browser 服務和 SQL Server 執行個體，透過動態指派的 TCP 連接埠，視情況。 |

## <a name="additional-known-issues"></a>其他已知的問題

* [使用線上移轉到 Azure SQL Database 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [使用線上移轉至 Azure Database for MySQL 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [使用線上移轉至 Azure Database for PostgreSQL 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 檢視發行項[Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 檢視發行項[如何使用 Azure 入口網站中的 Azure 資料庫設定伺服器參數適用於 MySQL](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 檢視發行項[使用 Azure 資料庫移轉服務的必要條件概觀](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 請參閱[使用 Azure 資料庫移轉服務相關常見問題集](https://docs.microsoft.com/azure/dms/faq)。
