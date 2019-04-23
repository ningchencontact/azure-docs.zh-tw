---
title: Azure SQL Database - 複本的讀取查詢 |Microsoft 文件
description: Azure SQL Database 可進行負載平衡唯讀工作負載使用唯讀複本，稱為讀取相應放大的容量。
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
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006143"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用唯讀複本，以進行負載平衡唯讀查詢工作負載

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

做為一部分[高可用性架構](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)，Premium、 業務關鍵或超大規模的服務層中每個資料庫都會自動佈建的主要複本與數個次要複本。 为次要副本预配的计算大小与主要副本相同。 **讀取相應放大**功能可讓您將負載平衡的 SQL 資料庫唯讀工作負載使用其中一個唯讀複本的容量，而不共用讀寫複本。 這種方式的唯讀工作負載將會與主要讀寫工作負載隔離，而且不會影響其效能。 此功能適用於包含邏輯上分隔唯讀工作負載，例如分析的應用程式。 它們可以獲得效能優勢，使用此額外的容量不會有任何額外的費用。

下圖說明使用業務關鍵資料庫。

![唯讀複本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

新的 Premium、 重要商務和超大規模資料庫預設會啟用讀取相應放大功能。 如果您的 SQL 連接字串設定為使用`ApplicationIntent=ReadOnly`，應用程式會被重新導向至唯讀複本，該資料庫的閘道。 如需有關如何使用資訊`ApplicationIntent`屬性，請參閱 <<c2> [ 指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果您想要確保應用程式連接到主要複本，不論`ApplicationIntent`設定 SQL 連接字串中，您必須明確地停用讀取的相應放大或改變它的組態時建立資料庫。 例如，如果您從標準或一般目的層將資料庫升級至 Premium、 業務關鍵或超大規模的層，並想要確定您的連線繼續移至主要複本時，停用讀取相應放大。如需有關如何停用它的詳細資訊，請參閱 <<c0> [ 啟用和停用讀取相應放大](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 只读副本不支持查询数据存储、扩展事件、SQL Profiler 和审核功能。 

## <a name="data-consistency"></a>資料一致性

複本的其中一個優點是複本一律處於交易一致狀態，但在不同的時間點，不同的複本之間可能會有些微延遲。 讀取相應放大支援工作階段層級一致性。 這表示如果唯讀工作階段重新連線無法使用複本所造成的連線錯誤之後，它可能會重新導向至不是最新的讀寫複本的 100%的複本。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则最新的更新可能不会在副本中立即可见。 延迟是由异步事务日志重做操作导致的。

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

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>对只读副本进行监视和故障排除

连接到只读副本后，可以使用 `sys.dm_db_resource_stats` DMV 访问性能指标。 若要访问查询计划统计信息，请使用 `sys.dm_exec_query_stats`、`sys.dm_exec_query_plan` 和 `sys.dm_exec_sql_text` DMV。

> [!NOTE]
> 逻辑 master 数据库中的 DMV `sys.resource_stats` 返回主要副本的 CPU 使用率和存储数据。


## <a name="enable-and-disable-read-scale-out"></a>啟用和停用讀取縮放

Premium、 業務關鍵及超大規模的服務層上的預設會啟用讀取相應放大。 在 Basic、 Standard 或一般用途服務層，就無法啟用讀取相應放大。 由 0 複本所設定的超大規模資料庫時，會自動停用讀取相應放大。 

您可以停用並重新啟用讀取相應放大，在單一資料庫和彈性集區使用下列方法的進階或業務關鍵服務層中的資料庫。

> [!NOTE]
> 停用讀取相應放大的功能被提供回溯相容性。

### <a name="azure-portal"></a>Azure 入口網站

您可以管理讀取 Sacle 外設定上**設定**database 刀鋒視窗。 

### <a name="powershell"></a>PowerShell

要在 Azure PowerShell 中管理讀取相應放大，必須使用 2016 年 12 月版的 Azure PowerShell 或更新版本。 如需最新 PowerShell 版本的相關資訊，請參閱 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

您可以停用或重新啟用讀取相應放大 Azure PowerShell 中叫用[組 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 並傳入所需的值 –`Enabled`或是`Disabled`-針對`-ReadScale`參數。 

若要停用在現有的資料庫 （使用正確的值，為您的環境取代角括號中的項目，並去除角括號） 的讀取的相應放大：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
若要停用新的資料庫 （使用正確的值，為您的環境取代角括號中的項目，並去除角括號） 上的讀取的相應放大：

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

若要重新啟用現有的資料庫 （使用正確的值，為您的環境取代角括號中的項目，並去除角括號） 上的讀取的相應放大：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

若要建立含有停用讀取相應放大的資料庫，或變更現有資料庫的設定，使用下列方法加`readScale`屬性設定為`Enabled`或`Disabled`中下方範例要求。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

如需詳細資訊，請參閱[資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-tempdb-on-read-only-replica"></a>对只读副本使用 TempDB

TempDB 数据库不会复制到只读副本。 每个副本具有自身的 TempDB 数据库版本，该版本是创建该副本时创建的。 系统确保 TempDB 可更新，并可以在执行查询期间进行修改。 如果只读工作负荷依赖于使用 TempDB 对象，则应创建这些对象作为查询脚本的一部分。 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>對異地複寫的資料庫使用讀取縮放

如果您用讀取相應放大的資料庫，異地複寫 （例如，做為容錯移轉群組的成員） 上的負載平衡唯讀工作負載，請確定主要和異地複寫次要資料庫上啟用該讀取的相應放大。 此配置可确保应用程序在故障转移后连接到新的主数据库时，相同的负载均衡体验能够继续。 如果您要連線到啟用讀取縮放的異地複寫次要資料庫，則會使用與路由傳送主要資料庫上連線的相同方式，將設定 `ApplicationIntent=ReadOnly` 的工作階段路由傳送至其中一個複本。  未設定 `ApplicationIntent=ReadOnly` 的工作階段會路由傳送至異地複寫次要的主要複本，這也是唯讀狀態。 因為異地複寫的次要資料庫比主要資料庫不同的端點，在過去以存取次要複本就不需要設定`ApplicationIntent=ReadOnly`。 為了確保回溯相容性，`sys.geo_replication_links` DMV 會顯示 `secondary_allow_connections=2` (允許所有用戶端連線)。

> [!NOTE]
> 不支援循環配置資源或任何其他負載平衡之間的路由，次要資料庫的本機複本。

## <a name="next-steps"></a>後續步驟

- 如需 SQL Database 的超大規模的供應項目資訊，請參閱[超大規模的服務層](./sql-database-service-tier-hyperscale.md)。
