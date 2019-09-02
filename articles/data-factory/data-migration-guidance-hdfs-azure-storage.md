---
title: 使用 Azure Data Factory 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體 |Microsoft Docs
description: 使用 Azure Data Factory, 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211605"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>使用 Azure Data Factory 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體 

Azure Data Factory 提供高效能、強大且符合成本效益的機制, 將大規模的資料移轉至內部部署的 HDFS, 以 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。 基本上, Azure Data Factory 包含兩種方法可將資料從內部部署 HDFS 遷移至 Azure。 您可以根據您的案例來選取每個專案。 

- ADF DistCp 模式。 ADF 支援使用[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html)將檔案依現有方式複製到 Azure Blob (包括[分段複製](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) 或 Azure Data Lake 存放區, 在此情況下, 它可以充分利用叢集的能力, 而不是在 ADF 自我裝載整合執行時間 (IR) 上執行。 它會提供更好的複製輸送量, 特別是當您的叢集非常強大時。 根據您在 Azure Data Factory 中的設定, 複製活動會自動建立 DistCp 命令、提交至您的 Hadoop 叢集, 以及監視複製狀態。 藉由使用與 DistCp 整合的 ADF, 客戶不僅可以利用您現有的強大叢集來達到最佳複製輸送量, 還能從 ADF 取得彈性排程和統一監視體驗的優點。 根據預設, ADF DistCp 模式是將資料從內部部署 hadoop 叢集遷移至 Azure 的建議方式。
- ADF 原生 IR 模式。 在某些情況下, DistCp 無法在您的案例中運作 (例如, 在 VNET 環境中, DistCp 工具不支援 Express Route 私用對等互連 + Azure 儲存體 VNet 端點)。 除此之外, 您有時不想使用現有的 hadoop 叢集作為遷移資料的引擎, 因為它會在您的叢集上帶來大量負載, 這可能會影響現有 ETL 作業的效能。 如果是這種情況, 您可以使用 ADF 原生功能, 其中 ADF integration runtime (IR) 可以是將資料從內部內部部署 HDFS 複製到 Azure 的引擎。

本文提供資料工程師和開發人員這兩種方法的下列資訊:
> [!div class="checklist"]
> * 效能 
> * 複製復原能力
> * 網路安全性
> * 高階解決方案架構 
> * 實施最佳做法  

## <a name="performance"></a>效能

在 ADF DistCp 模式中, 輸送量等同于獨立使用 DistCp 工具, 這會利用現有 hadoop 叢集的容量。 DistCp (分散式複製) 是用於大規模/內部叢集複製的工具。 它會使用 MapReduce 來影響其散發、錯誤處理和復原, 以及報告。 它會將檔案和目錄的清單展開為「對應」工作的輸入, 其中每個都將複製來源清單中指定之檔案的資料分割。 藉由使用與 DistCp 整合的 ADF, 您可以建立管線來充分利用您的網路頻寬, 以及儲存體 IOPS 和頻寬, 將您環境的資料移動輸送量最大化。  

在 ADF 原生 IR 模式中, 它也允許不同層級的平行處理, 其可充分利用您的網路頻寬, 以及儲存體 IOPS 和頻寬, 藉由手動相應增加自我裝載 IR 機器或相應放大, 將資料移動輸送量最大化自我裝載 IR 多部電腦。

- 單一複製活動可以利用可調整的計算資源。 使用自我裝載整合執行時間, 您可以手動相應增加機器或相應放大至多部電腦 ([最多4個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), 而單一複製活動會分割其在所有節點上的檔案集。 
- 單一複製活動會使用多個執行緒讀取和寫入資料存放區。 
- ADF 控制流程可以平行啟動多個複製活動, 例如,[針對每個迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

您可以從[複製活動的效能指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)取得更多詳細資料

## <a name="resilience"></a>復原能力

在 ADF DistCp 模式中, 您可以利用不同的 DistCp 命令列參數 (例如,-i, 忽略失敗;-update, 在原始程式檔和目的地檔案大小不同時寫入資料), 以達到不同層級的彈性。

在 ADF 原生 IR 模式中, 在單一複製活動執行中, ADF 具有內建的重試機制, 因此它可以處理資料存放區或基礎網路中特定層級的暫時性失敗。 執行從內部部署 HDFS 到 Blob 的二進位複製, 以及從內部部署的 HDFS 到 ADLS Gen2 時, ADF 會自動執行檢查點至較大的範圍。 如果複製活動執行失敗或已超時, 後續重試 (請務必重試計數 > 1), 複製會從最後一個失敗點繼續, 而不是從開頭開始。

## <a name="network-security"></a>網路安全性 

根據預設, ADF 會將資料從內部部署 HDFS 傳輸到 Azure Blob 儲存體, 或使用透過 HTTPS 通訊協定的加密連接 Azure Data Lake Storage Gen2。  HTTPS 提供傳輸中的資料加密, 並防止竊聽和攔截式攻擊。 

或者, 如果您不想透過公用網際網路傳送資料, 您可以透過 Azure Express Route 透過私人對等互連連結傳輸資料, 以達到更高的安全性。 請參閱下列解決方案架構, 以瞭解如何達成此目的。

## <a name="solution-architecture"></a>方案架構

透過公用網際網路遷移資料:

![解決方案-架構-公用-網路](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 在此架構中, 資料會使用 HTTPS over 公用網際網路安全地傳輸。 
- 建議您在公用網路環境中使用 ADF DistCp 模式。 如此一來, 您不僅可以利用現有的強大叢集來達到最佳複製輸送量, 還能從 ADF 取得彈性排程和統一監視體驗的優點。
- 您必須在公司防火牆後方的 Windows 電腦上安裝 ADF 自我裝載整合執行時間, 以將 DistCp 命令提交至您的 hadoop 叢集, 以及監視複製狀態。 假設這部電腦不是用來移動資料的引擎 (僅適用于控制目的), 則電腦的容量不會影響資料移動的輸送量。
- 支援來自 DistCp 命令的現有參數。


透過私人連結遷移資料: 

![解決方案-架構-私人-網路](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 在此架構中, 資料移轉是透過 Azure Express Route 透過私用對等互連連結進行, 因此資料永遠不會透過公用網際網路進行。
- DistCp 工具不支援 Express Route 私用對等互連 + Azure 儲存體 VNet 端點, 因此建議您透過整合執行時間使用 ADF 原生功能來遷移資料。
- 您需要在 Azure 虛擬網路內的 Windows VM 上安裝 ADF 自我裝載整合執行時間, 以達成此架構。 您可以手動相應增加 VM 或相應放大至多個 Vm, 以充分利用您的網路和儲存體 IOPS/頻寬。
- 針對每個 Azure VM (已安裝 ADF 自我裝載整合執行時間) 開始使用的建議設定是使用 32 vCPU 和 128 GB 的記憶體來 Standard_D32s_v3。 您可以在資料移轉期間持續監視 VM 的 CPU 和記憶體使用率, 以查看您是否需要進一步相應增加 VM 以獲得更佳的效能, 或相應減少 VM 以節省成本。
- 您也可以將最多4個 VM 節點與單一的自我裝載 IR 產生關聯, 以相應放大。 針對自我裝載 IR 執行的單一複製作業會自動分割盤案集, 並利用所有 VM 節點來平行複製檔案。 為了達到高可用性, 建議您從兩個 VM 節點開始, 以避免在資料移轉期間發生單一失敗點。
- 初始快照集資料移轉和差異資料移轉都可以使用此架構來達成。


## <a name="implementation-best-practices"></a>實施最佳做法 

### <a name="authentication-and-credential-management"></a>驗證和認證管理 

- 若要向 HDFS 驗證, 您可以使用[Windows (Kerberos) 或匿名](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)。 
- 支援多個驗證類型來連接到 Azure Blob 儲存體。  強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)識別: 建置於自動受管理的 ADF 識別 Azure AD 中, 它可讓您設定管線, 而不需要在連結服務定義中提供認證。  或者, 您可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用存取](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)簽章或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication), 向 Azure Blob 儲存體進行驗證。 
- 也支援多個驗證類型來連接到 Azure Data Lake Storage Gen2。  強烈建議使用[Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)識別, 雖然也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 當您未使用 Azure 資源的受控識別時, 強烈建議您將[認證儲存在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault), 讓您更輕鬆地集中管理和旋轉金鑰, 而不需要修改 ADF 連結的服務。  這也是[CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)的其中一個最佳作法。 

### <a name="initial-snapshot-data-migration"></a>初始快照集資料移轉 

在 ADF DistCp 模式中, 您可以建立一個複製活動來提交具有不同參數的 DistCp 命令, 以控制初始資料移轉行為。 

在 ADF 原生 IR 模式中, 建議您在遷移超過 10 TB 的資料時特別使用資料分割。 若要分割資料, 請利用 HDFS 上的資料夾名稱, 然後每個 ADF 複製作業一次只能複製一個資料夾分割。 您可以同時執行多個 ADF 複製工作, 以獲得更好的輸送量。
如果有任何複製作業因為網路或資料存放區暫時性問題而失敗, 您可以重新執行失敗的複製作業, 以從 HDFS 重新載入該特定的分割區。 載入其他分割區的所有其他複製作業都不會受到影響。

### <a name="delta-data-migration"></a>差異資料移轉 

在 ADF DistCp 模式中, 您可以利用 DistCp 命令列參數「-update, 在原始程式檔和目的地檔案大小差異時寫入資料」, 以達到差異資料移轉。

在 ADF 原生 IR 模式中, 從 HDFS 識別新的或已變更檔案的最高效能方法是使用時間分割的命名慣例–當 HDFS 中的資料已使用檔案或資料夾名稱中的時間配量資訊進行時間分割時 (例如,/yyyy/mm/dd/檔案 .csv), 然後您的管線就可以輕鬆地識別要以累加方式複製哪些檔案/資料夾。
或者, 如果您在 HDFS 中的資料不是時間分割, ADF 可以透過其 LastModifiedDate 來識別新的或已變更的檔案。 其運作方式是 ADF 會掃描 HDFS 中的所有檔案, 而且只會複製新的和更新的檔案, 其上次修改時間戳記大於特定值。 請注意, 如果您在 HDFS 中有大量檔案, 則不論符合篩選準則的檔案數目為何, 初始檔案掃描可能會花很長的時間。 在此情況下, 建議您先將資料分割, 並使用相同的分割區來進行初始快照集遷移, 讓檔案能夠以平行方式進行掃描。

### <a name="estimating-price"></a>預估價格 

請考慮下列管線, 以將資料從 HDFS 遷移至 Azure Blob 儲存體: 

![定價-管線](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

讓我們假設下列各項: 

- 總數據量為 1 PB
- 使用第二個解決方案架構來遷移資料 (ADF 原生 IR 模式)
- 1 PB 分為1000分割區, 而每個複本都會移動一個資料分割
- 每個複製活動都會設定一個與4部機器相關聯的自我裝載 IR, 並達到 500 MBps 的輸送量。
- ForEach concurrency 已設定為 4, 而匯總輸送量為 2 GBps
- 總共需要146小時才能完成遷移。


以下是以上述假設為基礎的預估價格: 

![定價-資料表](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 這是假設性的定價範例。  您的實際定價取決於您環境中的實際輸送量。
> 不包含 Azure Windows VM 的價格 (已安裝自我裝載整合執行時間)。

### <a name="additional-references"></a>其他參考 
- [HDFS 連接器](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [複製活動效能微調指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [建立和設定自我裝載 Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自我裝載整合執行時間 HA 和擴充性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [資料移動安全性考慮](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [將認證儲存在 Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [根據時間分割的檔案名, 以累加方式複製檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [根據 LastModifiedDate 複製新的和已變更的檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)