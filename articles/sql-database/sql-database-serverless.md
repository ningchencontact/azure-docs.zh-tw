---
title: Azure SQL Database 無伺服器 (預覽) | Microsoft Docs
description: 本文說明新的無伺服器計算層級，並將它與現有佈建計算層級進行比較
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
ms.date: 06/12/2019
ms.openlocfilehash: b740b49e2decabd5f104d1db5d38b48f2bc2111c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116194"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database 無伺服器 （預覽）

每秒，使用無伺服器 （預覽） 是單一資料庫的自動調整工作負載需求，以及帳單運算量為基礎的計算的計算層的 azure SQL Database。 無伺服器計算層也會自動暫停資料庫，在唯一的儲存體時的計費和活動傳回時，會自動繼續資料庫的非作用中期間。

## <a name="serverless-compute-tier"></a>無伺服器計算層級

單一資料庫的無伺服器計算層是由計算自動調整範圍和 autopause 延遲參數化。  這些參數的組態圖形資料庫的效能經驗，並計算成本。

![無伺服器計費](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>效能設定

- **最小虛擬核心**並**虛擬核心上限**所定義的計算容量可供資料庫範圍的可設定參數。 記憶體和 IO 限制會與指定的虛擬核心範圍成比例。  
- **Autopause 延遲**是可設定的參數定義的時間週期資料庫必須閒置多久以後才會自動暫停。 下一步 的登入或其他活動發生時，資料庫會自動繼續。  或者，可以停用 autopausing。

### <a name="cost"></a>成本

- 無伺服器資料庫的成本是計算成本和儲存體成本的總和。
- 計算使用量是介於最小和最大限制設定，計算成本根據虛擬核心和使用的記憶體。
- 計算使用量低於設定的最小限制，計算成本根據最小虛擬核心和最小記憶體設定。
- 資料庫暫停時，計算成本為零，而只有儲存體成本。
- 佈建的計算層的相同方式決定的儲存體成本。

如需成本的詳細資訊，請參閱 <<c0> [ 計費](sql-database-serverless.md#billing)。

## <a name="scenarios"></a>案例

無伺服器的性價針對採用間歇性、無法預測使用模式的單一資料庫最佳化，這些資料庫可接受在閒置使用期間之後，計算準備有些延遲。 相反地，佈建的計算層會是適用於單一資料庫或彈性集區具有較高的平均使用量無法容忍任何延遲的計算情節中的多個資料庫的價格 / 性能。

### <a name="scenarios-well-suited-for-serverless-compute"></a>適合用於無伺服器計算的案例

- 單一資料庫與間歇性、 無法預測的使用模式會穿插的非使用狀態和較低的平均計算使用率，經過一段時間期間。
- 經常重新調整的佈建的計算層和客戶想要委派中的單一資料庫計算服務調整。
- 沒有其中計算大小很難或無法以預估在 SQL Database 中的部署之前的使用量歷程記錄的新單一資料庫。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>適合用於佈建計算的案例

- 單一資料庫與多個一般、 可預測的使用方式模式與更高的平均計算經過一段時間的使用量。
- 無法容忍以下情況造成效能妥協的資料庫：經常修剪記憶體或從暫停狀態自動繼續以致延遲。
- 具有間歇性、 無法預期的使用量模式，可合併到較佳的價格效能最佳化的彈性集區的多個資料庫。

## <a name="comparison-with-provisioned-compute-tier"></a>與佈建計算層級比較

下表摘要說明無伺服器計算層級與佈建計算層級之間的差異：

| | **無伺服器計算** | **佈建計算** |
|:---|:---|:---|
|**資料庫使用模式**| 間歇性、 無法預測的使用方式，與較低的平均計算經過一段時間的使用量。 |  多個一般使用模式，以較高的平均計算經過一段時間或多個資料庫使用彈性集區使用量。|
| **效能管理投入量** |較低|較高|
|**計算調整**|自動|手動|
|**計算回應性**|在非使用期間後降低|立即|
|**計費細微度**|每秒|每小時|

## <a name="purchasing-model-and-service-tier"></a>購買模型和服務層級

目前只有虛擬核心購買模型中第 5 代硬體的一般用途層級支援 SQL Database 無伺服器。

## <a name="autoscaling"></a>自動調整

### <a name="scaling-responsiveness"></a>調整回應性

一般情況下，無伺服器資料庫的電腦上執行足夠的容量以滿足資源需求而不會中斷任何數量的要求最大的虛擬核心值所設定的限制範圍內的計算。 有時候，如果電腦無法在幾分鐘內滿足資源需求，負載平衡就會自動發生。 比方說，如果資源需求不是 4 個 Vcore，但只有 2 個虛擬核心可用，則它可能需要幾分鐘的時間之前提供 4 個 Vcore，負載平衡。 捨棄連線後，除了在作業結束時的短暫期間，資料庫都會在負載平衡期間保持線上狀態。

### <a name="memory-management"></a>記憶體管理

無伺服器資料庫的記憶體回收頻率較高比佈建的計算資料庫。 此行為，請務必在無伺服器的控制成本，而且可能會影響效能。

#### <a name="cache-reclamation"></a>快取回收

佈建的計算與資料庫不同，SQL 快取的記憶體會回收從無伺服器資料庫，當 CPU 或快取的使用率很低。

- 快取使用量會被視為低，一段時間的最大小總計最近使用過的快取項目低於臨界值時。
- 觸發快取回收時，目標快取大小以累加方式縮減為其先前的大小的分數，並回收只會繼續使用如果仍然很低。
- 快取回收時，原則選取要收回快取項目時記憶體不足的壓力過高，就會是與佈建的計算資料庫相同的選取範圍原則。
- 快取大小永遠不會縮小到低於最小虛擬核心，可設定所定義的最小記憶體限制。

在無伺服器和佈建計算資料庫，可能會收回項目，如果使用所有可用的記憶體快取。

#### <a name="cache-hydration"></a>快取序列化

資料從磁碟中擷取及與佈建資料庫的相同速度的相同方式，就會隨著 SQL 快取。 當資料庫忙碌時，快取允許成長無限制的最大記憶體上限限制。

## <a name="autopausing-and-autoresuming"></a>Autopausing 和 autoresuming

### <a name="autopausing"></a>Autopausing

如果下列條件全部成立 autopause 延遲的持續時間，就會觸發 Autopausing:

- 工作階段數 = 0
- CPU = 0，使用者執行的工作負載中的使用者集區

若要停用 autopausing，視提供的選項。

下列功能不支援 autopausing。  也就是說，如果使用任何下列功能，在資料庫保持上線不論資料庫的非使用狀態的持續時間：

- 異地複寫 （作用中異地複寫和自動容錯移轉群組）。
- 長期備份保留 (LTR)。
- SQL 資料同步中使用的同步資料庫。

需要資料庫在線上的部分服務更新在部署期間暫時防止 Autopausing。  在這種情況下，，服務更新完成之後，會變成一次允許 autopausing。

### <a name="autoresuming"></a>Autoresuming

如果下列任一條件會在任何時間，則為 true，就會觸發 Autoresuming:

|功能|自動繼續觸發程序|
|---|---|
|驗證和授權|登入|
|威脅偵測|啟用/停用資料庫或伺服器層級的威脅偵測設定。<br>修改資料庫或伺服器層級的威脅偵測設定。|
|資料探索與分類|新增、修改、刪除或檢視敏感度標籤|
|稽核|檢視稽核記錄。<br>更新或檢視稽核原則。|
|資料遮罩|新增、修改、刪除或檢視資料遮罩處理規則|
|透明資料加密|檢視透明資料加密的狀態|
|查詢 (效能) 資料存放區|修改或檢視查詢存放區設定；自動微調|
|自動微調|自動微調建議的應用和驗證，例如自動編製索引|
|資料庫複製|做為複本建立資料庫。<br>將匯出到 BACPAC 檔案。|
|SQL 資料同步|按照可設定的排程執行或定期執行中樞與成員資料庫之間的同步|
|修改特定資料庫中繼資料|加入新的資料庫標記。<br>變更的最大的虛擬核心、 最小虛擬核心或 autopause 延遲。|
|SQL Server Management Studio (SSMS)|使用 SSMS 第 18 版並針對伺服器中的任何資料庫開啟新的查詢視窗，就會在相同伺服器中繼續任何自動暫停的資料庫。 如果使用已關閉 IntelliSense 的 SSMS 17.9.1 版，則不會發生這種行為。|

需要資料庫在線上的部分服務更新在部署期間，也會觸發 Autoresuming。

### <a name="connectivity"></a>連線能力

如果無伺服器的資料庫已暫停，然後首次登入時會繼續資料庫，並傳回錯誤，指出資料庫無法使用錯誤碼 40613。 資料庫一旦繼續，則必須重試登入來建立連線。 具有連線重試邏輯的資料庫用戶端應該不需要修改。

### <a name="latency"></a>Latency

Autoresume 和 autopause 無伺服器資料庫的延遲通常是為了 autopause autoresume，1-10 分鐘的 1 分鐘。

## <a name="onboarding-into-serverless-compute-tier"></a>上架到無伺服器計算層

建立新的資料庫，或移動到無伺服器計算層中選擇現有的資料庫遵循相同的模式，以建立新的資料庫中佈建計算層級，並包含下列兩個步驟。

1. 指定服務目標名稱。 服務目標描述服務層、 硬體世代和最大的虛擬核心。 下表顯示服務目標選項：

   |服務目標名稱|服務層|硬體世代|最大虛擬核心數|
   |---|---|---|---|
   |GP_S_Gen5_1|一般用途|Gen5|1|
   |GP_S_Gen5_2|一般用途|Gen5|2|
   |GP_S_Gen5_4|一般用途|Gen5|4|

2. 選擇性地指定最小虛擬核心及 autopause 延遲，以變更其預設值。 下表顯示這些參數的可用值。

   |參數|值選擇|預設值|
   |---|---|---|---|
   |虛擬核心下限|{0.5, 1, 2, 4} 中未超過最大虛擬核心數的任一項|0.5 個虛擬核心|
   |自動暫停延遲|最低：360 分鐘 (6 小時)<br>最大值：10080 分鐘 (7 天)<br>增量：60 Minuten<br>停用自動暫停：-1|360 分鐘|

> [!NOTE]
> 目前不支援使用 T-SQL 將現有資料庫移到無伺服器中或變更其計算大小，但可以透過 Azure 入口網站或 PowerShell 進行。

### <a name="create-new-database-in-serverless-compute-tier"></a>無伺服器計算層中建立新的資料庫 

#### <a name="use-azure-portal"></a>使用 Azure 入口網站

請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](sql-database-single-database-get-started.md)。

#### <a name="use-powershell"></a>使用 PowerShell

下列範例會建立新的資料庫，無伺服器計算層中。  此範例明確指定最小虛擬核心數、最大虛擬核心數和自動暫停延遲。

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
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>將資料庫從佈建的計算層移到無伺服器計算層

#### <a name="use-powershell"></a>使用 PowerShell

下列範例會將資料庫從佈建的計算層移至 無伺服器計算層。 此範例明確指定最小虛擬核心數、最大虛擬核心數和自動暫停延遲。

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
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>將資料庫從無伺服器計算層移到佈建的計算層

無伺服器資料庫可以移到佈建計算層級中，方法如同將佈建計算資料庫移到無伺服器計算層級中。

## <a name="modifying-serverless-configuration"></a>修改無伺服器的組態

### <a name="maximum-vcores"></a>最大虛擬核心數

#### <a name="use-powershell"></a>使用 PowerShell

修改最大的虛擬核心透過執行[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`MaxVcore`引數。

### <a name="minimum-vcores"></a>最小虛擬核心數

#### <a name="use-powershell"></a>使用 PowerShell

使用會修改核心下限[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`MinVcore`引數。

### <a name="autopause-delay"></a>自動暫停延遲

#### <a name="use-powershell"></a>使用 PowerShell

修改 autopause 延遲透過執行[組 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令在 PowerShell 中使用`AutoPauseDelayInMinutes`引數。

## <a name="monitoring"></a>監視

### <a name="resources-used-and-billed"></a>使用和計費的資源

無伺服器資料庫的資源所封裝的應用程式套件、 SQL 執行個體和使用者資源集區的實體。

#### <a name="app-package"></a>應用程式套件

不論資料庫是在無伺服器或佈建計算層級中，應用程式套件都是資料庫的最外層資源管理界限。 應用程式套件包含 SQL 執行個體和外部服務，其可一起界定 SQL Database 中資料庫所用的所有使用者和系統資源。 外部服務的範例包括 R 和全文檢索搜尋。 SQL 執行個體通常會支配整個應用程式套件的整體資源使用率。

#### <a name="user-resource-pool"></a>使用者資源集區

不論資料庫是在無伺服器或佈建計算層級中，使用者資源集區都是資料庫的最內層資源管理界限。 使用者資源集區範圍的 CPU 和 IO 這類 DDL 查詢，例如建立和改變和 DML 查詢所產生的使用者工作負載選取、 插入、 更新和刪除。 這些查詢通常代表應用程式套件內很大的使用率比例。

### <a name="metrics"></a>度量

|實體|計量|描述|Units|
|---|---|---|---|
|應用程式套件|app_cpu_percent|應用程式所使用的虛擬核心百分比，相對於應用程式所允許的最大虛擬核心數。|百分比|
|應用程式套件|app_cpu_billed|在報告期間內針對應用程式計費的計算數量。 在這段期間所支付的金額為此計量與虛擬核心單價的乘積。 <br><br>彙總一段時間內每秒使用的最大 CPU 與記憶體，即可判斷此計量的值。 如果使用的數量小於依照最小虛擬核心數與最小記憶體所設定的最小佈建數量，就會收取最小佈建數量的費用。 為了比較 CPU 與記憶體以供計費用途，記憶體會依每個虛擬核心 3 GB 重新調整記憶體量，藉此規範成虛擬核心單位。|虛擬核心秒數|
|應用程式套件|app_memory_percent|應用程式所使用的記憶體百分比，相對於應用程式所允許的最大記憶體。|百分比|
|使用者集區|cpu_percent|使用者工作負載所使用的虛擬核心百分比，相對於使用者工作負載所允許的最大虛擬核心數。|百分比|
|使用者集區|data_IO_percent|使用者工作負載所使用的資料 IOPS 百分比，相對於使用者工作負載所允許的最大資料 IOPS。|百分比|
|使用者集區|log_IO_percent|使用者工作負載所使用的記錄 MB/s 百分比，相對於使用者工作負載所允許的最大記錄 MB/s。|百分比|
|使用者集區|workers_percent|使用者工作負載所使用的背景工作角色百分比，相對於使用者工作負載所允許的最大背景工作角色數。|百分比|
|使用者集區|sessions_percent|使用者工作負載所使用的工作階段百分比，相對於使用者工作負載所允許的最大工作階段數。|百分比|
____

> [!NOTE]
> Azure 入口網站中的計量可在單一資料庫的資料庫窗格中的 [監視]  之下取得。

### <a name="pause-and-resume-status"></a>暫停與繼續狀態

在 Azure 入口網站中，資料庫狀態會顯示於伺服器的 [概觀] 窗格，其中列出它所包含的資料庫。 資料庫狀態也會顯示在資料庫的 [概觀] 窗格中。

使用下列 PowerShell 命令來查詢資料庫的暫停和繼續狀態：

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>資源限制

如需資源限制，請參閱[無伺服器計算層級](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>計費

計算數量的計費方式為每秒使用的最大 CPU 與記憶體。 若使用的 CPU 與使用的記憶體數量小於各自的最小佈建數量，就會收取佈建數量的費用。 為了比較 CPU 與記憶體以供計費用途，記憶體會依每個虛擬核心 3 GB 重新調整記憶體量，藉此規範成虛擬核心單位。

- **計費的資源**：CPU 和記憶體
- **帳單金額**： 虛擬核心單位價格 * max (最小虛擬核心、 使用的虛擬核心、 最小記憶體 GB * 1/3，記憶體使用的 GB * 1/3) 
- **計費頻率**：每秒

VCore 單價中每個虛擬核心每秒的成本。 如需指定區域中的特定單位價格，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。

計費的計算數量會依下列度量公開：

- **計量**：app_cpu_billed (虛擬核心秒數)
- **定義**：最大值 (最小虛擬核心數, 使用的虛擬核心, 最小記憶體 GB * 1/3, 使用的記憶體 GB * 1/3)
- **報告頻率**：每分鐘

此數量會每秒計算，並彙總超過 1 分鐘。

請考慮無伺服器資料庫設定為 1 分鐘虛擬核心和最大的 4 個 Vcore。  這會對應至約 3 GB 最小記憶體和 12 GB 的記憶體上限。  假設自動暫停延遲設定為 6 小時，而資料庫工作負載是 24 小時內的第一個 2 小時期間，否則為非作用中。    

在此情況下，資料庫是計算和儲存體計費期間第一次的 8 小時。  即使資料庫是在第二個小時之後的非作用中啟動，就仍會計費根據佈建資料庫在線上時的最小計算後續 6 小時內的計算。  資料庫暫停時，只有儲存體的計費期間 24 小時內的其餘部分。

更精確地說，在此範例中的計算帳單的計算方式如下：

|時間間隔|使用每秒的 v 核心|使用每秒 GB|計算計費的維度|虛擬核心計費一段時間間隔的秒數|
|---|---|---|---|---|
|0:00-1:00|4|9|使用的 v 核心|4 個 Vcore * 3600 秒 = 14400 vCore 秒|
|1:00-2:00|1|12|使用記憶體|12 GB * 1/3 * 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|佈建的最小記憶體|3 GB * 1/3 * 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|計費而暫停的任何計算|0 的 vCore 秒|
|總虛擬核心計費過去 24 小時內的秒數||||50400 vCore 秒|

假設計算單價為 $0.000073/虛擬核心/秒。  然後向您收取這 24 小時內的計算是計算單位價格和 vCore 秒計費的乘積： $0.000073/vCore/second * 50400 vCore 秒 = $3.68

## <a name="available-regions"></a>可用區域

無伺服器計算層全球適用除了下列區域：澳大利亞中部、 中國東部、 中國北部、 法國南部、 德國中部、 德國東北部、 印度西部、 韓國南部、 南非西部、 英國北部、 英國南部、 英國西部和美國西部。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](sql-database-single-database-get-started.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)。
