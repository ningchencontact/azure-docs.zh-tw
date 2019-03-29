---
title: Azure SQL Database - 複本的讀取查詢 |Microsoft 文件
description: Azure SQL Database 可讓您使用唯讀複本功能 (稱為讀取相應放大)，對唯讀工作負載進行負載平衡。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619886"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>載入平衡唯讀查詢工作負載使用唯讀複本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database，仍然支援 PowerShell 的 Azure Resource Manager 模組，但所有未來的開發是 Az.Sql 模組。 這些指令程式，請參閱 < [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 在 Az 模組和 AzureRm 模組中命令的引數是本質上相同的。

**讀取縮放**可讓您使用一個唯讀複本的容量對 Azure SQL Database 唯讀工作負載進行負載平衡。

進階層 ([ DTU 型購買模型](sql-database-service-tiers-dtu.md)) 或業務關鍵層 ([vCore 型購買模型](sql-database-service-tiers-vcore.md)) 中的每個資料庫，都會自動佈建數個 AlwaysOn 複本，以支援可用性 SLA。 下圖說明這種情形。

![唯讀複本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

次要複本會成為主要複本相同的計算大小與佈建。 **讀取相應放大**功能可讓您使用其中一個唯讀複本功能對 SQL Database 的唯讀工作負載進行負載平衡，而不共用讀寫複本。 這種方式的唯讀工作負載將會與主要讀寫工作負載隔離，而且不會影響其效能。 此功能適用於包含邏輯上分隔唯讀工作負載 (例如分析) 的應用程式，因此可在不需額外費用的情況下使用這個額外容量獲得效能優勢。

若要将读取横向扩展功能用于特定的数据库，必须在创建数据库时或者在之后通过更改其配置来显式启用此功能，可以采用以下方式执行此操作：使用 PowerShell 调用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 或 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) 命令，或者通过 Azure 资源管理器 REST API 使用[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)方法。

針對資料庫啟用讀取相應放大之後，連線到該資料庫的應用程式會將導向至讀寫複本或唯讀狀態的複本，根據該資料庫的閘道`ApplicationIntent`中設定的屬性應用程式的連接字串。 如需 `ApplicationIntent` 屬性的詳細資訊，請參閱[指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果讀取相應放大已停用，或您在不支援的服務層中設定 ReadScale 屬性，所有連線都會被導向至與 `ApplicationIntent` 屬性無關的讀寫複本。

> [!NOTE]
> 查詢資料存放區、 擴充的事件、 SQL Profiler 和稽核功能，不支援在唯讀複本。 
## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級一致性。 這表示，如果唯讀工作階段在無法使用複本所造成的連線錯誤之後重新連線，就可能會被重新導向至與讀寫複本不完全同步的複本。 同樣地，如果應用程式使用讀寫工作階段的資料寫入，並立即讀取它使用唯讀工作階段，就可以最新的更新不會立即顯示在複本上。 延遲是非同步的交易記錄重做作業所造成的。

> [!NOTE]
> 區域內的複寫延遲較低，這種情況很罕見。

## <a name="connect-to-a-read-only-replica"></a>連線至唯讀複本

當您為資料庫啟用讀取相應放大時，用戶端所提供的連接字串中的 `ApplicationIntent` 選項會指出連線應路由至寫入複本還是唯讀複本。 具體來說，如果 `ApplicationIntent` 值為 `ReadWrite` (預設值)，則連線將會導向至資料庫的讀寫複本。 這與現有行為相同。 如果 `ApplicationIntent` 值為 `ReadOnly`，則連線會路由傳送至唯讀的複本。

例如，下列連接字串會將用戶端連線至唯讀複本 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

下列連接字串會將用戶端連線至讀寫複本 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>確認已連線到唯讀複本

您可以執行下列查詢，確認是否已連線到唯讀複本。 它會在連線到唯讀複本時傳回 READ_ONLY。

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> 在任何指定時間，ReadOnly 工作階段只能存取其中一個 AlwaysON 複本。

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>監視和疑難排解唯讀複本

當連接到唯讀複本，您可以存取效能計量使用`sys.dm_db_resource_stats`DMV。 若要存取查詢計劃的統計資料，請使用`sys.dm_exec_query_stats`，`sys.dm_exec_query_plan`和`sys.dm_exec_sql_text`Dmv。

> [!NOTE]
> DMV`sys.resource_stats`邏輯 master 資料庫中傳回的主要複本的 CPU 使用量和儲存體資料。


## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取縮放

「讀取縮放」在[受控執行個體](sql-database-managed-instance.md)商務關鍵性層中預設為啟用狀態。 您應該在[位於 SQL Database 伺服器上的資料庫](sql-database-servers.md)進階和商務關鍵性層中明確加以啟用。 這裡會說明啟用和停用讀取縮放的方法。

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell：啟用和停用讀取縮放

要在 Azure PowerShell 中管理讀取相應放大，必須使用 2016 年 12 月版的 Azure PowerShell 或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

啟用或停用讀取的向外延展 Azure PowerShell 中叫用[組 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 並傳入所需的值 –`Enabled`或是`Disabled`-針對`-ReadScale`參數。 或者，可以使用 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 来创建已启用读取横向扩展的新数据库。

例如，若要為現有資料庫啟用讀取相應放大 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

若要為現有資料庫停用讀取相應放大 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

若要建立會啟用讀取相應放大的新資料庫 (請將角括號中的項目取代為您的環境適用的值，並去除角括號)：

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API：啟用和停用讀取縮放

若要建立會啟用讀取相應放大的資料庫，或是為現有資料庫啟用或停用讀取相應放大，請將 `readScale` 屬性設為 `Enabled` 或 `Disabled` 以建立或更新對應的資料庫實體，如下列範例要求所示。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

如需詳細資訊，請參閱[資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-tempdb-on-read-only-replica"></a>使用唯讀複本上的 TempDB

TempDB 資料庫不會複寫到唯讀複本。 每個複本都有自己的複本建立時所建立的 TempDB 資料庫的版本。 它會確保 TempDB 可以更新，並且可以在查詢執行期間修改。 如果您的唯讀工作負載相依於使用 TempDB 的物件，您應該建立這些物件做為您的查詢指令碼的一部分。 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>對異地複寫的資料庫使用讀取縮放

如果您在異地複寫 （例如，做為容錯移轉群組的成員） 的資料庫上的平衡唯讀工作負載進行負載使用讀取的相應放大，請確定主要和異地複寫次要資料庫上啟用該讀取的相應放大。 此組態可確保您的應用程式容錯移轉之後連接至新的主要複本時，會繼續相同的負載平衡體驗。 如果您要連線到啟用讀取縮放的異地複寫次要資料庫，則會使用與路由傳送主要資料庫上連線的相同方式，將設定 `ApplicationIntent=ReadOnly` 的工作階段路由傳送至其中一個複本。  未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 由於異地複寫的次要資料庫具有與主要資料庫不同的端點，因此在過去若要存取次要，不需要設定 `ApplicationIntent=ReadOnly`。 為了確保回溯相容性，`sys.geo_replication_links` DMV 會顯示 `secondary_allow_connections=2` (允許所有用戶端連線)。

> [!NOTE]
> 不支持在辅助数据库的本地副本之间执行轮循机制或任何其他负载均衡路由。

## <a name="next-steps"></a>後續步驟

- 有关使用 PowerShell 设置读取横向扩展的信息，请参阅 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 或 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet。
- 如需使用 REST API 來設定讀取相應放大的相關資訊，請參閱[資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。
