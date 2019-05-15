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
ms.date: 05/11/2019
ms.openlocfilehash: ba79e2b9552f0c27ac11501b2b125a126e40eb1d
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551629"
---
# <a name="sql-database-serverless-preview"></a>Azure SQL Database 無伺服器 (預覽)

## <a name="what-is-the-serverless-compute-tier"></a>什麼是無伺服器計算層級

SQL Database 無伺服器 (預覽) 是一個計算層級，可針對單一資料庫每秒所用的計算數量計費。 無伺服器的性價已針對採用間歇性、無法預測使用模式的單一資料庫最佳化，這些資料庫可接受在閒置使用期間之後，計算準備有些延遲。

無伺服器計算層級中的資料庫會依照其可使用的計算範圍和自動暫停延遲參數化。

![無伺服器計費](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>效能

- `MinVcore` 和 `MaxVcore` 均為可設定的參數，其定義資料庫可用計算容量的範圍。 記憶體和 IO 限制會與指定的虛擬核心範圍成比例。  
- 自動暫停延遲是可設定的參數，其定義資料庫在自動暫停前必須處於非使用中的時間期間。 資料庫會在下次登入發生時自動繼續。

### <a name="pricing"></a>價格

- 無伺服器資料庫的帳單總計為計算帳單和與儲存體帳單的加總。
計算的計費是以每秒使用的虛擬核心數量和每秒使用的記憶體數量為基礎。
- 最小計算的計費是以最小虛擬核心數和最小記憶體為基礎。
- 當資料庫暫停時，只有儲存體會計費。

## <a name="scenarios"></a>案例

無伺服器的性價針對採用間歇性、無法預測使用模式的單一資料庫最佳化，這些資料庫可接受在閒置使用期間之後，計算準備有些延遲。 相反地，佈建計算層級的性價已針對平均使用量較高的單一或集區資料庫最佳化，這些資料庫無法接受計算準備有任何延遲。

### <a name="scenarios-well-suited-for-serverless-compute"></a>適合用於無伺服器計算的案例

- 採用間歇性、無法預測的使用模式，並穿插非使用期間的單一資料庫，可受益於使用計算數量每秒計費所帶來的價格節省。
- 資源需求難以預測且客戶偏好將計算大小調整委派給服務的單一資料庫。
- 佈建計算層級中經常變更效能層級的單一資料庫。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>適合用於佈建計算的案例

- 在一段時間內，計算使用率較為固定且較為大量的單一資料庫。
- 無法容忍以下情況造成效能妥協的資料庫：經常修剪記憶體或從暫停狀態自動繼續以致延遲。
- 採用間歇性、無法預測使用模式的多個資料庫，其可合併成單一伺服器並使用彈性集區來促進價格最佳化。

## <a name="comparison-with-provisioned-compute-tier"></a>與佈建計算層級比較

下表摘要說明無伺服器計算層級與佈建計算層級之間的差異：

| | **無伺服器計算** | **佈建計算** |
|:---|:---|:---|
|**典型使用案例**| 採用間歇性、無法預測的使用方式，並穿插非使用中期間的資料庫。 | 使用方式較為固定的資料庫或彈性集區。|
| **效能管理投入量** |較低|較高|
|**計算調整**|自動|手動|
|**計算回應性**|在非使用期間後降低|立即|
|**計費細微度**|每秒|每小時|

## <a name="purchasing-model-and-service-tier"></a>購買模型和服務層級

目前只有虛擬核心購買模型中第 5 代硬體的一般用途層級支援 SQL Database 無伺服器。

## <a name="autoscaling"></a>自動調整

### <a name="scaling-responsiveness"></a>調整回應性

一般來說，資料庫會在具有足夠容量的機器上執行，以滿足資源需求，而不會中斷在 `maxVcores` 值所設定限制內要求的任何計算數量。 有時候，如果電腦無法在幾分鐘內滿足資源需求，負載平衡就會自動發生。 捨棄連線後，除了在作業結束時的短暫期間，資料庫都會在負載平衡期間保持線上狀態。

### <a name="memory-management"></a>記憶體管理

無伺服器資料庫的記憶體回收頻率比佈建資料庫還要高。 此行為對於無伺服器成本控制十分重要。 不同於佈建計算，當 CPU 或快取使用率很低時，就會從無伺服器資料庫回收 SQL 快取中的記憶體。

## <a name="autopause-and-autoresume"></a>自動暫停和自動繼續

### <a name="autopause"></a>自動暫停

如果下列條件在自動暫停延遲的持續時間內全部成立，就會觸發自動暫停：

- 工作階段數 = 0
- CPU = 0 (適用於在使用者集區中執行的使用者工作負載)

已提供視需要停用自動暫停的選項。

### <a name="autoresume"></a>自動繼續

如果下列任一條件在任何時候成立，就會觸發自動繼續：

|功能|自動繼續觸發程序|
|---|---|
|驗證和授權|登入|
|威脅偵測|啟用/停用資料庫或伺服器層級的威脅偵測設定<br>修改資料庫或伺服器層級的威脅偵測設定|
|資料探索與分類|新增、修改、刪除或檢視敏感度標籤|
|稽核|檢視稽核記錄。<br>更新或檢視稽核原則|
|資料遮罩|新增、修改、刪除或檢視資料遮罩處理規則|
|透明資料加密|檢視透明資料加密的狀態|
|查詢 (效能) 資料存放區|修改或檢視查詢存放區設定；自動微調|
|自動微調|自動微調建議的應用和驗證，例如自動編製索引|
|資料庫複製|建立資料庫作為複本<br>匯出至 BACPAC 檔案|
|SQL 資料同步|按照可設定的排程執行或定期執行中樞與成員資料庫之間的同步|
|修改特定資料庫中繼資料|新增資料庫標籤<br>變更最大虛擬核心數、最小虛擬核心數、自動暫停延遲|
|SQL Server Management Studio (SSMS)|使用 SSMS 第 18 版並針對伺服器中的任何資料庫開啟新的查詢視窗，就會在相同伺服器中繼續任何自動暫停的資料庫。 如果使用已關閉 IntelliSense 的 SSMS 17.9.1 版，則不會發生這種行為。|

### <a name="connectivity"></a>連線能力

如果無伺服器的資料庫已暫停，則首次登入會繼續執行資料庫並傳回錯誤 (錯誤碼 40613)，指出該資料庫無法使用。 資料庫一旦繼續，則必須重試登入來建立連線。 具有連線重試邏輯的資料庫用戶端應該不需要修改。

### <a name="latency"></a>Latency

自動暫停或自動繼續無伺服器資料庫的延遲通常大約 1 分鐘。

### <a name="feature-support"></a>功能支援

下列功能不支援自動暫停和自動繼續。 也就是說，如果使用下列任何功能，則不管資料庫的非使用持續時間，資料庫都會保持線上狀態：

- 異地複寫 (主動式異地複寫和自動容錯移轉群組)
- 長期備份保留 (LTR)
- SQL 資料同步中使用的同步資料庫。


## <a name="on-boarding-into-the-serverless-compute-tier"></a>在無伺服器計算層級上架

建立新資料庫或將現有資料庫移到無伺服器計算層級，遵循與在佈建計算層級中建立新資料庫相同的模式，其中包含下列兩個步驟：

1. 指定服務目標名稱。 服務目標可指定服務層級、硬體世代和最大虛擬核心數。 下表顯示服務目標選項：

   |服務目標名稱|服務層級|硬體世代|最大虛擬核心數|
   |---|---|---|---|
   |GP_S_Gen5_1|一般用途|Gen5|1|
   |GP_S_Gen5_2|一般用途|Gen5|2|
   |GP_S_Gen5_4|一般用途|Gen5|4|

2. 選擇性地指定最小虛擬核心數和自動暫停延遲，以變更其預設值。 下表顯示這些參數的可用值。

   |參數|值選擇|預設值|
   |---|---|---|---|
   |最小虛擬核心數|{0.5, 1, 2, 4} 中未超過最大虛擬核心數的任一項|0.5 個虛擬核心|
   |自動暫停延遲|最小值：360 分鐘 (6 小時)<br>最大值：10080 分鐘 (7 天)<br>增量：60 分鐘<br>停用自動暫停：-1|360 分鐘|

> [!NOTE]
> 目前不支援使用 T-SQL 將現有資料庫移到無伺服器中或變更其計算大小，但可以透過 Azure 入口網站或 PowerShell 進行。

### <a name="create-new-database-using-the-azure-portal"></a>使用 Azure 入口網站建立新的資料庫

請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](sql-database-single-database-get-started.md)。

### <a name="create-new-database-using-powershell"></a>使用 PowerShell 建立新的資料庫

下列範例會在具有最小虛擬核心數和自動暫停延遲預設值的服務目標 (名為 GP_S_Gen5_4) 所定義的無伺服器計算層級中建立新資料庫。

無伺服器需要目前資源庫中較新版的 PowerShell，因此執行 `Update-Module Az.Sql` 以取得已啟用無伺服器的最新 Cmdlet。

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

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>將現有資料庫移到無伺服器計算層級中

下列範例會將現有單一資料庫從佈建計算層級移到無伺服器計算層級。 此範例明確指定最小虛擬核心數、最大虛擬核心數和自動暫停延遲。

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

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>將資料庫從無伺服器計算層級移出

無伺服器資料庫可以移到佈建計算層級中，方法如同將佈建計算資料庫移到無伺服器計算層級中。

## <a name="modify-serverless-configuration-parameters"></a>修改無伺服器組態參數

### <a name="maximum-vcores"></a>最大虛擬核心數

在 PowerShell 中使用 [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 命令搭配 `MaxVcore` 引數，可修改最大虛擬核心數。

### <a name="minimum-vcores"></a>最小虛擬核心數

在 PowerShell 中使用 [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 命令搭配 `MinVcore` 引數，可修改最大虛擬核心數。

### <a name="autopause-delay"></a>自動暫停延遲

在 PowerShell 中使用 [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 命令搭配 `AutoPauseDelay` 引數，可修改最大虛擬核心數。

## <a name="monitor-serverless-database"></a>監視無伺服器資料庫

### <a name="resources-used-and-billed"></a>使用和計費的資源

無伺服器資料庫的資源是由下列實體所封裝：

#### <a name="app-package"></a>應用程式套件

不論資料庫是在無伺服器或佈建計算層級中，應用程式套件都是資料庫的最外層資源管理界限。 應用程式套件包含 SQL 執行個體和外部服務，其可一起界定 SQL Database 中資料庫所用的所有使用者和系統資源。 外部服務的範例包括 R 和全文檢索搜尋。 SQL 執行個體通常會支配整個應用程式套件的整體資源使用率。

#### <a name="user-resource-pool"></a>使用者資源集區

不論資料庫是在無伺服器或佈建計算層級中，使用者資源集區都是資料庫的最內層資源管理界限。 使用者資源集區可界定 DDL 查詢 (例如，CREATE、ALTER 等) 和 DML 查詢 (例如，SELECT、INSERT、UPDATE、DELETE 等) 所產生使用者工作負載的 CPU 和 IO。 這些查詢通常代表應用程式套件內很大的使用率比例。

### <a name="metrics"></a>度量

|實體|計量|說明|Units|
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
> Azure 入口網站中的計量可在單一資料庫的資料庫窗格中的 [監視] 之下取得。

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
- **計費數量 ($)**：虛擬核心單價 * 最大值 (最小虛擬核心數, 使用的虛擬核心, 最小記憶體 GB * 1/3, 使用的記憶體 GB * 1/3) 
- **計費頻率**：每秒

每個虛擬核心每秒成本中的虛擬核心單價。 如需指定區域中的特定單位價格，請參閱 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)。

計費的計算數量會依下列度量公開：

- **計量**：app_cpu_billed (虛擬核心秒數)
- **定義**：最大值 (最小虛擬核心數, 使用的虛擬核心, 最小記憶體 GB * 1/3, 使用的記憶體 GB * 1/3)
- **報告頻率**：每分鐘

此數量會每秒計算，並彙總超過 1 分鐘。

**範例**：請考慮使用 GP_S_Gen5_4 且在一小時內具有下列使用量的資料庫：

|時間 (小時：分鐘)|app_cpu_billed (虛擬核心秒數)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||總計：1631|

假設計算單價為 $0.000073/虛擬核心/秒。 接著使用下列公式來判斷針對這一小時期間內計費的計算：**$0.000073/虛擬核心/秒 * 1631 虛擬核心秒數 = $0.1191**

## <a name="available-regions"></a>可用區域

除了下列區域以外，可在所有區域使用無伺服器計算層級：澳大利亞中部、中國東部、中國北部、法國南部、德國中部、德國東北部、印度西部、韓國南部、南非西部、英國北部、英國南部、英國西部及美國中西部

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[快速入門：使用 Azure 入口網站在 Azure SQL Database 中建立單一資料庫](sql-database-single-database-get-started.md)。
- 如需資源限制，請參閱[無伺服器計算層級資源限制](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)。