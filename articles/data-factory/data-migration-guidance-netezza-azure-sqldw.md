---
title: 使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 Azure |Microsoft Docs
description: 使用 Azure Data Factory, 將資料從內部部署 Netezza 伺服器遷移至 Azure。
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259557"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 Azure 

Azure Data Factory 提供高效能、穩固且符合成本效益的機制, 將大規模的資料移轉至內部部署 Netezza 伺服器, 以 Azure 儲存體或 Azure SQL 資料倉儲。 本文提供資料工程師和開發人員的下列資訊:

> [!div class="checklist"]
> * 效能 
> * 複製復原能力
> * 網路安全性
> * 高階解決方案架構 
> * 實施最佳做法  

## <a name="performance"></a>效能

Azure Data Factory 提供無伺服器架構, 允許不同層級的平行處理原則, 讓開發人員能夠建立管線來充分利用您的網路頻寬以及資料庫頻寬, 以最大化您的資料移動輸送量環境.

![效能](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- 單一複製活動可以利用可調整的計算資源: 使用 Azure Integration Runtime 時, 您可以用無伺服器的方式為每個複製活動指定[最多 256 diu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) ;使用自我裝載的 Integration Runtime 時, 您可以手動相應增加機器或相應放大至多部電腦 ([最多4個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), 而單一複製活動會將其分割區分散到所有節點。 
- 單一複製活動會使用多個執行緒讀取和寫入資料存放區。 
- Azure Data Factory 控制流程可以平行啟動多個複製活動, 例如,[針對每個迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

您可以從[複製活動的效能指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)取得更多詳細資料

## <a name="resilience"></a>復原能力

在單一複製活動執行中, Azure Data Factory 具有內建的重試機制, 因此它可以處理資料存放區或基礎網路中特定層級的暫時性失敗。

Azure Data Factory 複製活動也提供兩種方式, 可在來源和接收資料存放區之間複製資料時, 處理不相容的資料列。 當遇到不相容的資料時, 您可以中止並使複製活動失敗, 或略過不相容的資料列來繼續複製 rest 資料。 此外, 您可以在 Azure Blob 儲存體或 Azure Data Lake 存放區中記錄不相容的資料列, 以便了解失敗的原因、修正資料來源上的資料, 然後重試複製活動。

## <a name="network-security"></a>網路安全性 

根據預設, Azure Data Factory 會將資料從內部部署 Netezza 伺服器傳輸到 Azure 儲存體, 或使用透過 HTTPS 通訊協定的加密連接來 Azure SQL 資料倉儲。 它提供傳輸中的資料加密, 並防止竊聽和攔截式攻擊。

或者, 如果您不想透過公用網際網路傳送資料, 您可以透過 Azure Express Route 透過私人對等互連連結傳輸資料, 以達到更高的安全性。 請參閱下列解決方案架構, 以瞭解如何達成此目的。

## <a name="solution-architecture"></a>方案架構

透過公用網際網路遷移資料:

![解決方案-架構-公用-網路](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- 在此架構中, 資料會使用 HTTPs over 公用網際網路安全地傳輸。
- 您必須在公司防火牆後方的 windows 電腦上安裝 Azure Data Factory 自我裝載整合執行時間, 以達成此架構。 請確定您在 windows 機器上的 Azure Data Factory 自我裝載整合執行時間可以直接取得 Netezza 伺服器的存取權。 您可以手動將電腦相應增加或相應放大至多部電腦, 以充分利用您的網路和資料存放區頻寬來複製資料。
- 初始快照集資料移轉和差異資料移轉都可以使用此架構來達成。

透過私人連結遷移資料: 

![解決方案-架構-私人-網路](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- 在此架構中, 資料移轉是透過 Azure Express Route 透過私用對等互連連結進行, 因此資料永遠不會透過公用網際網路進行。 
- 您需要在 Azure 虛擬網路內的 Windows VM 上安裝 Azure Data Factory 自我裝載整合執行時間, 以達成此架構。 您可以手動相應增加 Vm 或相應放大至多個 Vm, 以充分利用您的網路和資料存放區頻寬來複製資料。
- 初始快照集資料移轉和差異資料移轉都可以使用此架構來達成。

## <a name="implementation-best-practices"></a>實施最佳做法 

### <a name="authentication-and-credential-management"></a>驗證和認證管理 

- 若要向 Netezza 進行驗證, 您可以透過[連接字串使用 ODBC 驗證](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 
- 支援多個驗證類型來連接到 Azure Blob 儲存體。  強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)識別: 建置於自動受控 Azure Data Factory 在 Azure AD 中識別, 可讓您設定管線, 而不需要在連結服務定義中提供認證。  或者, 您可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用存取](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)簽章或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication), 向 Azure Blob 儲存體進行驗證。 
- 也支援多個驗證類型來連接到 Azure Data Lake Storage Gen2。  強烈建議使用[Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)識別, 雖然也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 也支援多個驗證類型來連接到 Azure SQL 資料倉儲。 強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)識別, 雖然也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)或[SQL 驗證](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。
- 當您未使用 Azure 資源的受控識別時, 強烈建議您將[認證儲存在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault), 以便在不修改 Azure Data Factory 連結服務的情況下, 更輕鬆地集中管理和旋轉金鑰。  這也是[CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)的其中一個最佳作法。 

### <a name="initial-snapshot-data-migration"></a>初始快照集資料移轉 

針對小型資料表, 當其磁片區大小小於 100 GB, 或可在2小時內遷移至 Azure 時, 您可以讓每個複製作業在每個資料表中載入資料。 您可以執行多個 Azure Data Factory 複製工作, 同時載入不同的資料表以獲得更好的輸送量。 

在每個複製作業中, 您也可以透過使用[parallelCopies 設定](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)與資料分割選項來執行平行查詢, 並依分割區複製資料, 來達到某種程度的平行處理原則。 有兩個數據分割選項可供選擇, 詳細資訊如下。
- 建議您從資料配量開始, 因為它更有效率。  請確定 [parallelCopies] 設定中的 [平行處理原則數目] 低於 Netezza 伺服器上資料表中的資料配量分割區總數。  
- 如果每個資料配量分割區的磁片區大小仍然很大 (例如大於 10 GB), 建議您切換到動態範圍分割區, 讓您有更大的彈性可定義資料分割的數目和每個分割區的磁片區大小依資料分割資料行、上限和下限。

針對大型資料表, 當其磁片區大小大於 100 GB, 或在2小時內無法遷移至 Azure 時, 建議您依自訂查詢分割資料, 然後讓每個複製作業一次複製一個分割區。 您可以同時執行多個 Azure Data Factory 複製工作, 以獲得更好的輸送量。 請注意, 每個複製作業目標若要透過自訂查詢載入一個分割區, 您仍然可以透過資料配量或動態範圍來啟用平行處理原則, 以增加輸送量。 

如果有任何複製作業因為網路或資料存放區暫時性問題而失敗, 您可以重新執行失敗的複製作業, 從資料表重新重載該特定的分割區。 載入其他分割區的所有其他複製作業都不會受到影響。

將資料載入 Azure SQL 資料倉儲時, 建議使用 Azure blob 儲存體作為預備環境, 在複製作業中啟用 Polybase。

### <a name="delta-data-migration"></a>差異資料移轉 

從您的資料表中識別新的或更新的資料列的方式, 是在架構內使用 timestamp 資料行或遞增索引鍵, 然後將最新的值儲存為外部資料表中的高水位線, 以便在下次載入資料時用來篩選差異資料。 

不同的資料表可以使用不同的浮水印資料行來識別新的或更新的資料列。 我們建議您建立外部控制資料表, 其中每個資料列都代表 Netezza 伺服器上的一個資料表及其特定浮水印資料行名稱和高水位線值。 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Azure VM 或電腦上的自我裝載整合執行時間設定

假設您要將資料從 Netezza 伺服器遷移至 Azure, 無論 Netezza 伺服器是在公司防火牆後方或 VNET 環境中的承諾, 您都必須在 windows 機器或 VM 上安裝自我裝載整合執行時間, 這是要移動的引擎data.

- 針對每部電腦或 VM 開始使用的建議設定是使用 32 vCPU 和 128 GB 的記憶體。 您可以持續監視 IR 機器在資料移轉期間的 CPU 和記憶體使用量, 以瞭解您是否需要進一步相應增加機器以提升效能, 或相應減少電腦以節省成本。
- 您也可以將最多4個節點與單一的自我裝載 IR 產生關聯, 以相應放大。 針對自我裝載 IR 執行的單一複製作業會自動利用所有 VM 節點, 以平行方式複製資料。 為達高可用性, 建議您從2個 VM 節點開始, 以避免在資料移轉期間發生單一失敗點。

### <a name="rate-limiting"></a>速率限制

最佳做法是使用具代表性的範例資料集來進行效能 POC, 讓您可以針對每個複製活動判斷適當的分割區大小。 建議您讓每個分割區在2小時內載入至 Azure。  

從單一複製活動開始, 使用單一自我裝載 IR 機器來複製資料表。 根據您資料表中的資料配量分割區數目, 逐漸增加 parallelCopies 設定, 並根據您從複製作業看到的輸送量, 查看是否可以在2小時內將整個資料表載入至 Azure。 

如果無法達成此目的, 而且同時也無法充分利用自我裝載 IR 節點和資料存放區的容量, 請逐漸增加並行複製活動的數目, 直到達到您的網路或頻寬限制的限制為止。 

在自我裝載的 IR 機器上持續監視 CPU/記憶體使用率, 並準備好相應增加機器或相應放大至多部電腦, 當您看到 CPU/記憶體已完全使用時。 

當您遇到 Azure Data Factory 複製活動所回報的節流錯誤時, 請減少 Azure Data Factory 中的並行或 parallelCopies 設定, 或考慮增加網路和資料存放區的頻寬/IOPS 限制。 


### <a name="estimating-price"></a>預估價格 

請考慮下列管線, 其結構是用來將資料從內部部署 Netezza 伺服器遷移至 Azure SQL 資料倉儲:

![定價-管線](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

讓我們假設下列各項: 

- 總數據量為 50 TB。 
- 使用第一個解決方案架構來遷移資料 (Netezza 伺服器位於防火牆後方的內部部署)
- 50 TB 會分割成500個磁碟分割, 且每個複製活動會移動一個資料分割。
- 每個複製活動都會設定一個針對4部機器的自我裝載 IR, 並達到 20 MBps 的輸送量。 (在複製活動中, parallelCopies 設定為 4, 而每個從資料表載入資料的執行緒都會達到 5 MBps 的輸送量)
- ForEach concurrency 已設定為 3, 而匯總輸送量為 60 MBps。
- 總共需要243小時才能完成遷移。

以下是以上述假設為基礎的預估價格: 

![定價-資料表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 這是假設性的定價範例。 您的實際定價取決於您環境中的實際輸送量。 不包含 windows 機器的價格 (已安裝自我裝載整合執行時間)。 

### <a name="additional-references"></a>其他參考 
- [使用 Azure Data Factory 從內部部署關聯式資料倉儲將資料移轉至 Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza 連接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 連接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 資料倉儲連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [複製活動效能微調指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [建立和設定自我裝載 Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自我裝載整合執行時間 HA 和擴充性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [資料移動安全性考慮](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [將認證儲存在 Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [以累加方式從一個資料表複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [以累加方式從多個資料表複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure Data Factory 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)