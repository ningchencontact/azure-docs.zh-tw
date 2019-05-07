---
title: Azure SQL Database 無伺服器 」 （預覽） |Microsoft Docs
description: 本文章說明新的無伺服器計算層，並比較它與現有的佈建的計算層
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 2ab8f272fc264f153144803be772d381c1780512
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143275"
---
# <a name="sql-database-serverless-preview"></a>SQL Database 無伺服器 （預覽）

## <a name="what-is-the-serverless-compute-tier"></a>什麼是無伺服器計算層

SQL Database 無伺服器 （預覽） 是帳單的計算上以每秒為單位的單一資料庫所使用的計算層。 無伺服器是適用於單一資料庫與叢發性使用模式可以容忍某些延遲的計算情節之後閒置的使用量期間的價格效能。
相較之下，公開可用的供應項目，SQL Database 的今日的帳單中計算每小時進行佈建。 此佈建的計算層的價格效能最佳化的單一資料庫或彈性集區具有較高的平均使用量，無法計算準備的任何延遲情形。

無伺服器的電腦層中的資料庫是由它可以使用的計算範圍和 autopause 延遲參數化。

![無伺服器的計費](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>效能

- `MinVcore` 和`MaxVcore`所定義的計算容量可供資料庫範圍的可設定參數。 記憶體和 IO 限制是指定的虛擬核心範圍成正比。  
- Autopause 延遲是時間的可設定的參數定義的資料庫必須閒置多久以後，它會自動暫停之前。 下次登入時發生時，資料庫會自動繼續。

### <a name="pricing"></a>價格

- 無伺服器資料庫的總收費為計算的帳單和儲存體計費的總和。
計算的計費根據使用的虛擬核心和每秒使用的記憶體數量。
- 最小計費的計算是以虛擬核心下限 」 和 「 最小記憶體為基礎。
- 當資料庫暫停時，就會只儲存體計費。

## <a name="scenarios"></a>案例

無伺服器是適用於單一資料庫與叢發性使用模式可以容忍某些延遲的計算情節之後閒置的使用量期間的價格 / 性能。 佈建的計算層是適用於較高的平均使用量無法容忍任何的計算情節的延遲與單一或集區資料庫的價格 / 性能。

下表比較與佈建的計算層的無伺服器計算層次：

||無伺服器計算|佈建的計算|
|---|---|---|
|**一般使用案例**|暴增，無法預期的使用量，穿插停用期間的資料庫|資料庫或彈性集區具有多個一般使用方式|
|**效能管理工作**|較低|較高|
|**計算縮放比例**|自動|手動|
|**計算回應**|在非使用中期間後較低|立即|
|**計費資料粒度**|每秒|每小時|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>適合無伺服器計算的案例

- 單一資料庫與穿插沒有活動的期間的叢發性使用模式可以受益於根據每秒使用的計算時數計費的價格折扣。
- 單一資料庫很難預測的資源需求和想要委派的客戶與計算服務調整大小。
- 佈建的計算層中的單一資料庫經常變更效能層級。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>適用於佈建的計算案例

- 單一資料庫與更多一般和更多有很多計算經過一段時間的使用量。
- 無法容許效能取捨所產生的多個資料庫經常修剪的記憶體，或延遲 autoresuming 從暫停狀態。
- 內含可合併到單一伺服器，並使用更好的價格最佳化彈性集區的叢發性使用模式的多個資料庫。


## <a name="purchasing-model-and-service-tier"></a>購買模型和服務層

無伺服器的 SQL Database 目前只支援一般用途層中的購買模型的 vcore 第 5 代硬體上。

## <a name="autoscaling"></a>自動調整

### <a name="scaling-responsiveness"></a>調整回應性

資料庫在一般情況下，在機器上執行足夠的容量以滿足資源需求而不會中斷任何數量的要求所設定的限制範圍內的計算`maxVcores`值。 有時候，如果電腦已無法滿足資源需求，在幾分鐘內，就會發生自動平衡負載。 在資料庫保持上線期間的負載平衡除了短暫的時間內，在作業結束時的連接會中斷。

### <a name="memory-management"></a>記憶體管理

無伺服器資料庫的記憶體回收頻率較高比佈建資料庫。 此行為，務必要在無伺服器的控制成本。 不同於佈建的計算，SQL 快取的記憶體會回收無伺服器資料庫中，CPU 或快取的使用率較低時。

## <a name="autopause-and-autoresume"></a>Autopause 和 autoresume

### <a name="autopause"></a>Autopause

如果下列條件全部成立 autopause 延遲的持續時間，就會觸發 Autopause:

- 數字的工作階段 = 0
- CPU = 0 （用於使用者的使用者集區中執行的工作負載）

若要停用 autopause，視提供的選項。

### <a name="autoresume"></a>Autoresume

如果下列任一條件會在任何時間，則為 true，就會觸發 Autoresume:

|功能|Autoresume 觸發程序|
|---|---|
|驗證和授權|登入|
|威脅偵測|啟用/停用資料庫或伺服器層級的威脅偵測設定<br>修改資料庫或伺服器層級的威脅偵測設定|
|資料探索與分類|加入、 修改、 刪除或檢視敏感度標籤|
|稽核|檢視稽核記錄。<br>更新或檢視稽核原則|
|資料遮罩|加入、 修改、 刪除或檢視的資料遮罩規則|
|透明資料加密|檢視狀態] 或 [透明資料加密狀態|
|查詢 （效能） 資料存放區|修改或檢視的查詢存放區設定;自動調整|
|自動調整將|應用程式，例如自動編製索引的自動調整將建議的驗證|
|資料庫複製|做為複本建立資料庫<br>匯出到 BACPAC 檔案|
|SQL 資料同步|執行可設定的排程或手動執行的中樞和成員資料庫之間的同步處理|
|修改特定資料庫的中繼資料|加入新的資料庫標籤<br>變更的最大虛擬核心、 最小虛擬核心、 autopause 延遲|
|SQL Server Management Studio (SSMS)|使用 SSMS 第 18 版及伺服器中開啟新的 [查詢] 視窗的任何資料庫會繼續在相同的伺服器中任何自動暫停的資料庫。 如果使用 IntelliSense 的 SSMS 版本 17.9.1 關閉，則不會發生這種行為。|

### <a name="connectivity"></a>連線能力

如果無伺服器的資料庫已暫停，然後首次登入時會繼續資料庫，並傳回錯誤，指出資料庫目前無法使用。 一旦繼續資料庫時，則必須重試登入來建立連線。 資料庫用戶端連接重試邏輯應該不需要修改。

### <a name="latency"></a>Latency

Autopause 或 autoresume 無伺服器資料庫的延遲是通常大約 1 分鐘。

### <a name="feature-support"></a>功能支援

Autopausing 和 autoresuming 不支援下列功能。 也就是說，如果使用任何下列功能，在資料庫保持上線不論資料庫的非使用狀態的持續時間：

- 異地複寫 （作用中異地複寫和自動容錯移轉群組）
- 長期備份保留 (LTR)
- 使用 SQL 資料同步中同步處理資料庫。


## <a name="on-boarding-into-the-serverless-compute-tier"></a>上架到無伺服器計算層

建立新的資料庫，或移動到無伺服器計算層中選擇現有的資料庫遵循相同的模式，以建立新的資料庫中佈建計算層級，並包含下列兩個步驟：

1. 指定服務的目標名稱。 下表顯示可用的服務層和目前已提供公開預覽版本中的計算大小。

   |服務層|計算大小|
   |---|---|
   |一般用途|GP_S_Gen5_1|
   |一般用途|GP_S_Gen5_2|
   |一般用途|GP_S_Gen5_4|

2. 選擇性地指定最小的虛擬核心及 autopause 延遲，以變更其預設值。 下表顯示可用的值，以這些參數。

   |參數|值選項|預設值|
   |---|---|---|---|
   |最高 vCore|{0.5，1，2，4} 的任何未超過最大的虛擬核心|0.5 的 v 核心|
   |Autopause 延遲|最小值：360 分鐘 （6 個小時）<br>最大值：10080 分鐘 （7 天）<br>增加：60 Minuten<br>停用 autopause:-1|360 分鐘|

> [!NOTE]
> 使用 T-SQL 將現有的資料庫移到無伺服器，或變更其計算大小目前不支援，但可以透過 Azure 入口網站或 PowerShell 來完成。

### <a name="create-new-database-using-the-azure-portal"></a>建立新的資料庫，使用 Azure 入口網站

請參閱[快速入門：使用 Azure 入口網站的 Azure SQL Database 中建立單一資料庫](sql-database-single-database-get-started.md)。

### <a name="create-new-database-using-powershell"></a>建立新的資料庫，使用 PowerShell

下列範例會建立新的資料庫名 GP_S_Gen5_4 具有預設值為最小虛擬核心及 autopause 延遲的服務目標所定義的無伺服器計算層中。

無伺服器需要較新版的 PowerShell 目前位於資源庫中，因此執行非`Update-Module Az.Sql`以取得最新的無伺服器啟用 cmdlet。

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>將現有的資料庫移入無伺服器計算層

下列範例會將現有單一資料庫佈建的計算層移到無伺服器計算層。 此範例會使用預設值，最小虛擬核心、 最大的虛擬核心和 autopause 延遲。

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>移出資料庫的無伺服器計算層

無伺服器資料庫可以變成相同的方式將佈建的計算資料庫移到無伺服器計算層中佈建的計算層。

## <a name="modify-serverless-configuration-parameters"></a>修改無伺服器的組態參數

### <a name="maximum-vcores"></a>最低 vCore

修改虛擬核心上限透過執行[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`MaxVcore`引數。

### <a name="minimum-vcores"></a>最高 vCore

修改虛擬核心上限透過執行[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`MinVcore`引數。

### <a name="autopause-delay"></a>Autopause 延遲

修改虛擬核心上限透過執行[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`AutoPauseDelay`引數。

## <a name="monitor-serverless-database"></a>監視無伺服器資料庫

### <a name="resources-used-and-billed"></a>資源使用和計費

無伺服器資料庫的資源被封裝的下列實體：

#### <a name="app-package"></a>應用程式套件

應用程式套件是針對的資料庫，不論資料庫是否為無伺服器或已佈建的計算層中的外部大部分的資源管理界限。 應用程式封裝包含 SQL 執行個體，而外部服務該放在一起的範圍使用的 SQL Database 中的所有使用者和系統資源。 外部服務的範例包括 R 和全文檢索搜尋。 SQL 執行個體通常會支配跨應用程式套件的整體資源使用量。

#### <a name="user-resource-pool"></a>使用者資源集區

使用者資源集區是內部資料庫，不論資料庫是否為無伺服器或已佈建的計算層中的大部分資源管理界限。 使用者資源集區範圍 （例如，CREATE、 ALTER 等） 的 DDL 查詢和 DML 查詢 （例如，SELECT、 INSERT、 UPDATE、 DELETE、 等等。） 所產生的使用者工作負載的 CPU 和 IO。 這些查詢通常代表應用程式套件內的使用量的最重大的比例。

### <a name="metrics"></a>指标

|實體|計量|描述|Units|
|---|---|---|---|
|應用程式套件|app_cpu_percent|相對於應用程式所允許的最大虛擬核心應用程式所使用的虛擬核心的百分比。|百分比|
|應用程式套件|app_cpu_billed|在報告期間向您收取應用程式的計算數量。 在這段期間所支付的金額是此計量和 vCore 單價的乘積。<br>此計量的值取決於彙總一段時間的 CPU 最大值使用，並使用每秒的記憶體。<br>如果使用量低於最小的金額佈建所設定的最小虛擬核心和最小記憶體，則佈建的最少是計算費用。  若要比較 CPU 與記憶體，為了計費方便，記憶體會先正規化成單元的虛擬核心藉由調整以 gb 為單位的 3 GB 的記憶體數量，每個虛擬核心。|vCore 秒|
|應用程式套件|app_memory_percent|相對於應用程式所允許的最大記憶體的應用程式所使用的記憶體百分比。|百分比|
|使用者集區|cpu_percent|相對於使用者的工作負載所允許的最大虛擬核心的使用者工作負載所使用的虛擬核心的百分比。|百分比|
|使用者集區|data_IO_percent|百分比的資料相對於 IOPS 的最大資料的使用者工作負載所使用的 IOPS 可透過使用者的工作負載。|百分比|
|使用者集區|log_IO_percent|記錄檔百分比相對於最大記錄檔 MB/秒的使用者工作負載所使用的 MB/秒允許使用者的工作負載。|百分比|
|使用者集區|workers_percent|相對於使用者的工作負載所允許的最大背景工作角色的使用者工作負載所使用的背景工作的百分比。|百分比|
|使用者集區|sessions_percent|使用使用者的工作負載，相對於使用者的工作負載所允許的最大工作階段的工作階段的百分比。|百分比|
____

> [!NOTE]
> 在 Azure 入口網站中的計量可用於單一資料庫底下的 [資料庫] 窗格**監視**。

### <a name="pause-and-resume-status"></a>暫停和繼續的狀態

在 Azure 入口網站中，資料庫狀態會顯示在 [概觀] 窗格會列出它所包含的資料庫的伺服器。 資料庫狀態也會顯示資料庫的 [概觀] 窗格中。

您可以使用下列 PowerShell 命令來查詢暫停和繼續資料庫的狀態：

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>資源限制

如需資源限制，請參閱[無伺服器計算層](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>計費

計費每秒數量是計算的使用的 CPU 和記憶體使用每秒的最大值。 如果使用的 CPU 數量，使用的記憶體小於最小的金額佈建每個已佈建的容量的計費。 若要比較 CPU 與記憶體，為了計費方便，記憶體會先正規化成單元的虛擬核心藉由調整以 gb 為單位的 3 GB 的記憶體數量，每個虛擬核心。

- **計費資源**:CPU 和記憶體
- **容量計費 （$）**： 虛擬核心單位價格 * max (最小虛擬核心、 使用的虛擬核心、 最小記憶體 GB * 1/3，記憶體使用的 GB * 1/3) 
- **計費頻率**:每秒

計費的計算數量會依下列度量：

- **計量**: app_cpu_billed （vCore 秒）
- **定義**： 最大 (最小虛擬核心、 使用的虛擬核心、 最小記憶體 GB * 1/3，記憶體使用的 GB * 1/3) *
- **回報頻率**:每分鐘

> [!NOTE]
> \* 此數量會計算每秒，並彙總超過 1 分鐘。

**範例**：請考慮使用下列的使用方式中的 GP_S_Gen5_4，在一小時內的資料庫：

|時間 （小時： 分鐘）|app_cpu_billed （vCore 秒）|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||總計:1631|

假設計算單位的價格為 $0.2609/vCore/hour。 然後使用下列公式來決定此一小時期間向您收取計算： **$0.2609/vCore/hour * 1631 vCore 秒 * 1 小時/3600 秒 = $0.1232**

## <a name="available-regions"></a>可用區域

無伺服器計算層會在下列區域以外的所有區域上市：澳大利亞中部、 中國東部、 中國北部、 法國南部、 德國中部、 德國東北部、 印度西部、 韓國南部、 南非西部、 英國北部、 英國南部、 英國西部和美國西部

## <a name="next-steps"></a>後續步驟

如需資源限制，請參閱[無伺服器計算層級資源限制](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)。