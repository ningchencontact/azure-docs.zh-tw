---
title: 使用 DMA 和 Azure Migrate 評估要移轉到 Azure 的內部部署工作負載 | Microsoft Docs
description: 了解如何準備 Azure，以使用 Data Migration Assistant (DMA) 和 Azure Migrate 服務來移轉內部部署機器。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fb102cc43c6e1d17afaa78a2833ae447600a96af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>案例 1：評估要移轉至 Azure 的內部部署工作負載

Contoso 公司在考慮移轉至 Azure 時，想要進行技術和財務方面的評估，以了解其內部部署工作負載是否適合移轉至雲端。 尤其是，他們想要評估機器和資料庫是否能與移轉作業相容，並預估在 Azure 中執行其資源所需的容量和成本。

為了熟悉並更加了解其中所涉及的技術，他們將會評估和遷移小型的內部部署旅遊應用程式。 這個應用程式分為兩層，其中一個 VM 上有 Web 應用程式在執行，第二個 VM 上則有 SQL Server 資料庫。 應用程式部署在 VMware 中，環境則是由 vCenter Server 來管理。 他們會使用 Data Migration Assistant (DMA) 和 Azure Migrate 服務來進行評估。 如果您想試用使用此說明旅遊應用程式的這個案例，您可以從 [github](https://github.com/Microsoft/SmartHotel360) 進行下載。

**Technology** | **說明** | **成本**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA 會評估和偵測可能影響 Azure 中資料庫功能的相容性問題。 此外，也會評估 SQL Server 來源和目標之間的功能同位，並針對目標環境的效能和可靠性提出改善建議。 | 此工具可免費下載。 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | 此服務可協助您評估內部部署機器是否可移轉至 Azure。 它會評估機器是否適合移轉，並提供在 Azure 中執行機器的大小調整和成本預估。 Azure Migrate 服務目前可評估內部部署 VMware VM 是否能移轉至 Azure。 | 使用此服務目前 (2018 年 4 月) 不需付費。
[服務對應](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate 會使用服務對應來顯示您想要遷移的機器彼此之間的相依性。 |  服務對應是 Azure Log Analytics 的一部分。 目前可使用 180 天而不會產生費用。 

在此案例中，我們會下載並執行 DMA，以評估旅遊應用程式的內部部署 SQL Server 資料庫。 我們會搭配使用 Azure Migrate 和相依性對應來評估應用程式的 VM，再將其遷移至 Azure。

> [!NOTE]
> 在此案例中，我們的資料庫評估目標會是「Azure VM 上的 SQL Server」。 不過，在我們的下一個案例文章中，則會執行目標為 Azure SQL 受控執行個體的移轉。 由於 DMA 目前不支援對 Azure SQL 受控執行個體目標進行評估，所以才會使用這種方法。

## <a name="architecture"></a>架構


![移轉評估架構](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

在此情節中：
- Contoso 是代表一般企業組織的虛構名稱。 Contoso 想要評估並遷移其兩層式內部部署旅遊應用程式。
- Contoso 有內部部署資料中心 (**contoso-datacenter**) 以及內部部署網域控制站 (**contosodc1**)。
- 內部旅遊應用程式分為兩個 VM 層級 (分別是 **WEBVM** 和 **SQLVM**)，而且位於 VMware ESXi 主機 **contosohost1.contoso.com**。
- VMware 環境是由 VM 上所執行的 VCenter Server (**vcenter.contoso.com**) 進行管理。




## <a name="prerequisites"></a>先決條件

以下是部署此案例時所需的項目：

- 執行 5.5、6.0 或 6.5 版的內部部署 vCenter 伺服器。
- vCenter 伺服器中的唯讀帳戶，或是可供建立帳戶的權限。 
- 可在 vCenter 伺服器上使用 .OVA 範本建立 VM 的權限。
- 至少一個執行 5.0 版或更新版本的 ESXi 主機。
- 至少兩個內部部署 VMware VM，且其中一個要執行 SQL Server 資料庫。
- 您應該擁有可在每個 VM 上安裝 Azure Migrate 代理程式的權限。
- VM 應該要能直接連線到網際網路。
        - 您可以限制只能透過網際網路存取[所需的 URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites)。
        - 如果您的機器沒有辦法連線到網際網路，則需要下載並安裝 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。
- 執行 SQL Server 執行個體的 VM 所具有的 FQDN，以供評估資料庫。
- SQL Server VM 上所執行的 Windows 防火牆應該在 TCP 通訊埠 1433 (預設值) 允許外部連線，以供 DMA 連線。


## <a name="scenario-overview"></a>案例概觀

以下是所要執行的步驟：


> [!div class="checklist"]
> * **步驟 1：準備 Azure**。 我們只需要 Azure 訂用帳戶。
> * **步驟 2：下載並安裝 DMA**：準備 DMA 以便評估內部部署 SQL Server 資料庫。
> * **步驟 3：評估資料庫**：執行資料庫評估並加以分析。
> * **步驟 4：準備使用 Azure Migrate 評估 VM**：設定內部部署帳戶並調整 VMware 設定。
> * **步驟 5：探索內部部署 VM**：建立Azure Migrate 收集器 VM。 然後，執行收集器以探索要評估的 VM。
> * **步驟 6：準備相依性分析**：在 VM 上安裝 Azure Migrate 代理程式，以便能了解 VM 之間的相依性對應。
> * **步驟 7：評估 VM**：檢查相依性、將 VM 分組並執行評估。 評估完成後，加以分析以準備移轉。


## <a name="step-1-prepare-azure"></a>步驟 1：準備 Azure

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。
- 如果您使用既有的訂用帳戶，而且您不是管理員，則需要與管理員合作，讓其指派訂用帳戶或您在此案例中所用資源群組的擁有者或參與者權限給您。


## <a name="step-2-download-and-install-the-dma"></a>步驟 2：下載並安裝 DMA

1. 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595)下載 DMA。
    - 您可以在任何可以連線到 SQL 執行個體的機器上安裝 Assistant。 您不需要在 SQL Server 機器上執行此工具。
    - 請勿在 SQL Server 主機機器上執行此工具。
2. 按兩下所下載的安裝檔案 (DownloadMigrationAssistant.msi)，以開始安裝。
3. 在 [完成] 頁面上，確定已選取 [啟動 Microsoft Data Migration Assistant]，然後按一下 [完成]。

## <a name="step-3-run-and-analyze-the-database-assessment"></a>步驟 3：執行資料庫評估並加以分析

執行評估以針對指定目標分析來源 SQL Server 執行個體。

1. 在 [新增] 中選取 [評估]，然後對評估提供專案名稱。
2. 在 [來源伺服器類型] 中選取 [SQL Server]。 在 [目標伺服器類型] 中選取 [Azure 虛擬機器上的 SQL Server]。

    ![選取來源](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      現階段，DMA 不支援目標為 SQL 受控執行個體的遷移評估。 為解決此問題，我們會使用「Azure VM 上的 SQL Server」作為我們的假設評估目標。

1.  在 [選取目標版本] 中，指定您要在 Azure 中執行的目標 SQL Server 版本，以及您要在評估中探索的項目：
    - [相容性問題] 會告訴您可能會中斷移轉的變更，或告訴您必須略做調整再移轉。 此外，也會讓您知道您目前所使用、但已過時的功能。 所有問題會以相容性層級來加以組織。 
    - [新功能的建議] 會讓您知道，資料庫可於移轉後所使用的目標 SQL Server 平台中，有什麼新功能。 這些新功能會以效能、安全性和儲存來加以組織。 

    ![選取目標](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. 在 [連線到伺服器] 中，指定執行 SQL Server 執行個體的機器名稱、驗證類型和連線詳細資料。 然後按一下 [ **連接**]。

    ![選取目標](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. 在 [新增來源] 中，選取您想要評估的資料庫，然後按一下 [新增]。
4. 系統便會以您指定的名稱建立評估。

    ![建立評估](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  按 [下一步] 以開始評估。
6. 在 [檢閱結果] 中，您會看到所啟用評估測試的結果。


### <a name="analyze-the-database-assessment"></a>分析資料庫評估結果

一有結果就會顯示在 Assistant 中。 

1. 在 [相容性問題] 報告中，檢查資料庫在每個相容性層級是否有問題，如果有，該如何加以修正。 相容性層級與 SQL Server 版本的對應如下：
    - 100：SQL Server 2008/Azure SQL Database
    - 110：SQL Server 2012/Azure SQL Database
    - 120：SQL Server 2014/Azure SQL Database
    - 130：SQL Server 2016/Azure SQL Database
    - 140：SQL Server 2017/Azure SQL Database

    ![相容性問題](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. 在 [功能建議] 報告中，檢視評估作業建議您應在移轉後設定的效能、安全性和儲存功能。 其會針對各種功能提出建議，包括記憶體內部 OLTP 和資料行存放區、Stretch Database、Always Encrypted、動態資料遮罩和透明資料加密。

    ![功能建議](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. 如有修正任何問題，請按一下 [重新開始評估] 來加以重新執行。 
4. 按一下 [匯出報告] 來取得 JSON 或 CSV 格式的評估報告。

如果您要執行較大規模的評估：

- 您可以同時執行多個評估，並開啟 [所有評估] 頁面來檢視這些評估的狀態。
- 您可以[將評估合併至 SQL Server 資料庫](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)。
- 您可以[將評估合併至 PowerBI 報告](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)。


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>步驟 4：準備使用 Azure Migrate 評估 VM

建立 VMware 帳戶以供 Azure Migrate 用來自動探索要評估的 VM、確認您有建立 VM 的權限、記下必須開啟的連接埠，並設定統計資料設定層級。

### <a name="set-up-a-vmware-account"></a>設定 VMware 帳戶

 您必須擁有 vCenter 的唯讀帳戶。 如果沒有這樣的帳戶，請使用下列屬性建立 VMware 帳戶：

- 使用者類型：至少是唯讀使用者。
- 權限：資料中心物件 –> 傳播至子物件、角色=唯讀。
- 詳細資訊：在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。
- 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。

### <a name="verify-permissions-to-create-a-vm"></a>確認用來建立 VM 的權限

確認您有權限可藉由匯入 .OVA 格式的檔案來建立 VM。 [深入了解](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>確認連接埠

在此案例中，我們將會設定相依性對應。 要使用此功能，就必須在您要評估的 VM 上安裝代理程式。 該代理程式必須能從每個 VM 上的 TCP 連接埠 443 連線到 Azure。 [深入了解](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)連線需求。


### <a name="set-statistics-settings"></a>設定統計資料設定

在開始部署前，應將 vCenter Server 的統計資料設定設為層級 3。 請注意：
- 設定層級之後，必須至少等待一天才能執行評估。 否則，其運作可能會不如預期。
- 如果層級高於 3，評估仍會運作，但：
    - 不會收集磁碟和網路的效能資料。
    - 針對儲存，Azure Migrate 的建議是在 Azure 中使用標準磁碟，且大小要與內部部署磁碟相同。
    - 針對網路功能，每個內部部署網路介面卡都建議在 Azure 中有一個對應的網路介面卡。
    - 針對計算，Azure Migrate 會查看 VM 核心和記憶體大小，並建議使用有相同設定的 Azure VM。 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。
   
    
[深入了解](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)層級 3 的大小調整。

如下所示地設定層級：

1. 在 vSphere Web 用戶端中，開啟 vCenter 伺服器執行個體。
2. 選取 [管理] 索引標籤，然後在 [設定] 底下按一下 [一般]。
3. 按一下 [編輯]，然後在 [統計資料] 中將統計資料層級的設定設為 [層級 3]。

    ![vCenter 統計資料層級](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>步驟 5：探索 VM

建立 Azure Migrate 專案，下載並設定收集器 VM。 然後，執行收集器來探索 VM。

### <a name="create-a-project"></a>建立專案

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [建立資源]。
2. 搜尋 **Azure Migrate**。 在搜尋結果中選取 [Azure Migrate]，然後按一下 [建立]。
3. 指定專案名稱和 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定專案的所在位置，然後按一下 [建立]。

    - 您只能在「美國中西部」或「美國東部」區域建立 Azure Migrate 專案。
    - 您可以針對任何目標位置規劃移轉。
    - 專案位置只會用來儲存從內部部署 VM 收集到的中繼資料。

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>下載收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此 VM 會探索內部部署 VMware VM，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將它匯入內部部署 vCenter Server，以建立 VM。

1. 在 Azure Migrate 專案中，按一下 [使用者入門] > [探索及評估] > [探索機器]。
2. 在 [探索機器] 中，按一下 [下載] 以下載 .OVA 檔案。
3. 在 [複製專案認證] 中，複製專案識別碼和金鑰。 在設定收集器時，您會需要這些資料。

    ![下載 .ova 檔案](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 .OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 產生的雜湊應符合這些設定 (1.0.9.7 版)
    
    **演算法** | **雜湊值**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>建立收集器設備

將下載的檔案匯入 vCenter Server。

1. 在 vSphere 用戶端主控台中，按一下 [檔案] > [部署 OVF 範本]。

    ![部署 OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. 在 [部署 OVF 範本精靈] > [來源] 中，指定 .OVA 檔案的位置，然後按 [下一步]。
3. 在 [OVF 範本詳細資料] 中，按 [下一步]。 在 [使用者授權合約] 中，按一下 [接受] 以接受合約，然後按 [下一步]。
4. 在 [名稱和位置] 中，指定收集器 VM 的易記名稱，以及 VM 將裝載所在的清查位置，然後按 [下一步]。 指定收集器設備的執行所在主機或叢集。
5. 在 [儲存體] 中指定要用來儲存設備檔案的位置，然後按 [下一步]。
6. 在 [磁碟格式] 中，指定您要如何佈建儲存體。
7. 在 [網路對應] 中，指定收集器 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure。 
8. 在 [準備完成] 中檢閱設定，選取 [在部署後開啟電源]，然後按一下 [完成]。

訊息建立好之後，就會發出訊息確認已成功完成。

### <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

在開始之前，請注意收集器目前只支援以「英文 (美國)」作為作業系統語言和收集器介面語言。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]。
2. 提供設備的語言、時區和密碼喜好設定。
3. 在桌面上，按一下 [執行收集器] 捷徑。

    ![收集器捷徑](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. 在 Azure Migrate 收集器中，開啟 [設定必要條件]。
    - 接受授權條款，並閱讀第三方資訊。
    - 收集器會確認 VM 是否可存取網際網路、時間是否同步，以及收集器服務是否正在執行 (已依預設安裝在 VM 上)。 它也會確認是否已安裝 VMWare PowerCLI。 
    
    > [!NOTE]
    > 我們會假設 VM 可直接存取網際網路，而不會使用 Proxy。

    ![驗證必要條件](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. 在 [指定 vCenter Server 詳細資料] 中，執行下列動作：
    - 指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。
    - 在 [使用者名稱] 和 [密碼] 中，指定收集器要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。
    - 在 [選取範圍] 中，選取虛擬機器探索的範圍。 收集器只能探索指定範圍內的 VM。 範圍可以設定為特定資料夾、資料中心或叢集。 它不應包含超過 1500 個 VM。 

    ![連線到 vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. 在 [指定移轉專案] 中，指定您從入口網站所複製的 Azure Migrate 專案識別碼和金鑰。 如果您之前未複製，請從收集器 VM 開啟 Azure 入口網站。 在專案的 [概觀] 頁面中，按一下 [探索機器]，然後複製值。  

    ![連接到 Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. 在 [檢視收集進度] 中監視探索，然後確認從虛擬機器收集到的中繼資料位於範圍內。 收集器會提供概略的探索時間。

    ![收集進行中](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

收集完成後，請確認 VM 有出現在入口網站中。

1. 在 Azure Migrate 專案中，按一下 [管理] > [機器]。
2. 確認您想要探索的 VM 出現在頁面內。

    ![探索到的機器](./media/migrate-scenarios-assessment/discovery-complete.png)

3. 請注意，機器目前未安裝 Azure Migrate 代理程式。 我們需要安裝這些代理程式，才能檢視相依性。
    
    ![探索到的機器](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>步驟 6︰準備相依性分析

為了檢視所要評估的 VM 彼此之間的相依性，我們在 Web 應用程式 VM (WEBVM 和 SQLVM) 上下載並安裝代理程式。

### <a name="take-a-snapshot"></a>擷取快照集

如果您想要先複製 VM 再加以修改，請於擷取快照集後再安裝代理程式。

![機器的快照集](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式

1.  在 Azure Migrate 專案 > [機器] 頁面上選取機器，然後按一下 [相依性] 資料行中的 [需要安裝]。
2.  在每個 VM 的 [探索機器] 頁面上，下載並安裝 Microsoft Monitoring Agent (MMA) 和相依性代理程式。
3.  複製工作區識別碼與金鑰。 安裝 MMA 時需要這些資料。

    ![代理程式下載](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>安裝 MMA

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留預設的安裝資料夾 > [下一步]。 
4. 在 [代理程式安裝選項] 中，選取 [將代理程式連線至 Azure Log Analytics] > [下一步]。 

    ![MMA 安裝](./media/migrate-scenarios-assessment/mma-install.png) 
5. 在 **Azure Log Analytics** 中貼上您從入口網站複製的工作區識別碼和金鑰。 按 [下一步] 。
    ![MMA 安裝](./media/migrate-scenarios-assessment/mma-install2.png) 

6. 在 [準備安裝] 中安裝 MMA。



#### <a name="install-the-dependency-agent"></a>安裝相依性代理程式

1.  對所下載的相依性代理程式按兩下。
2.  在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3.  在 [正在安裝] 中，等候安裝完成。 然後按 [下一步] 。

    ![相依性代理程式](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>步驟 7：執行 VM 評估並加以分析

確認機器相依性並建立群組。 然後，執行評估。

### <a name="verify-dependencies-and-create-a-group"></a>確認相依性並建立群組。

1.  在 [機器] 頁面上，針對您要分析的 VM 按一下 [檢視相依性]。

    ![檢視電腦相依性](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. SQLVM 的相依性對應顯示下列詳細資料：

    - 在指定的時間期間 (預設為一小時) 內，有作用中網路連線在 SQLVM 上執行的處理序群組/處理序
    - 所有相依機器的輸入 (用戶端) 和輸出 (伺服器) TCP 連線。
    - 已安裝 Azure Migrate 代理程式的相依機器會顯示為不同的方塊
    - 未安裝代理程式的機器則會顯示連接埠和 IP 位址資訊。
    
 3. 針對已安裝代理程式的機器 (WEBVM)，按一下機器方塊以檢視詳細資訊，包括 FQDN、作業系統、MAC 位址。 

    ![檢視群組相依性](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. 現在，選取您想要新增至群組的 VM (SQLVM 和 WEBVM)。  使用「CTRL+按一下滑鼠左鍵」來選取多個 VM。
5. 按一下 [建立群組]，然後指定名稱 (smarthotelapp)。

> [!NOTE]
    > 若要檢視更細微的相依性，您可以展開時間範圍。 您可以選取特定持續期間，也可以選取開始和結束日期。 


### <a name="run-an-assessment"></a>執行評估


1. 在 [群組]  頁面上，開啟群組 (smarthotelapp)
2. 按一下 [建立評估]。

    ![建立評估](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. 評估便會出現在 [管理] > [評估] 頁面中。


### <a name="modify-assessment-settings"></a>修改評估設定

在此教學課程中，我們已使用預設的評估設定，但您可以如下所示地自訂設定：

1. 在移轉專案的 [評量] 頁面中選取評量，然後按一下 [編輯屬性]。
2. 根據下表修改屬性：

    **設定** | **詳細資料** | **預設值**
    --- | --- | ---
    **目標位置** | 要作為移轉目的地的 Azure 位置 | 沒有預設值。
    **儲存體備援** | Azure VM 會在移轉後使用的儲存體備援類型。 | [本地備援儲存體 (LRS)](../storage/common/storage-redundancy-lrs.md) 是預設值。 Azure Migrate 只支援以受控磁碟為基礎的評估，而受控磁碟只支援 LRS，因此只支援 LRS 選項。 
    **調整大小準則** | Azure Migrate 用於為 Azure 設定適當 VM 大小的準則。 您可以執行*以效能為基礎*的大小調整或將 VM 調整為*內部部署*大小，而不必考量效能歷程記錄。 | 以效能為基礎的大小調整是預設選項。
    **效能歷程記錄** | 考量評估 VM 效能的持續時間。 當調整大小的準則為*以效能為基礎的大小調整*時，此屬性才適用。 | 預設值為一天。
    **百分位數使用率** | 要視為適當大小調整的效能取樣集百分位數值。 當調整大小的準則為*以效能為基礎的大小調整*時，此屬性才適用。  | 預設值為第 95 個百分位數。
    **定價層** | 您可以指定目標 Azure VM 的[定價層 (基本/標準)](../virtual-machines/windows/sizes-general.md)。 例如，如果您打算移轉到生產環境，則可考慮標準層，這會提供低延遲的 VM，但成本可能比較高。 另一方面，如果您具有開發測試環境，則可以考慮基本層，其中有較高延遲和較低成本的 VM。 | 預設會使用[標準](../virtual-machines/windows/sizes-general.md)層。
    **緩和因數** | Azure Migrate 會在評估期間考量緩衝區 (緩和因數)。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 例如，10 核心的 VM，如果使用率只達 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。 | 預設設定是 1.3x。
    **提供項目** | 您註冊取得的 [Azure 優惠](https://azure.microsoft.com/support/legal/offer-details/)。 | 預設值是[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)。
    **貨幣** | 帳單貨幣。 | 預設值為美元。
    **折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。 | 預設設定為 0%。
    **Azure Hybrid Benefit** | 指定您是否擁有軟體保證以及是否符合 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 的資格。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 | 預設值為 [是]。

3. 按一下 [儲存] 以更新評估設定。


### <a name="analyze-the-vm-assessment"></a>分析 VM 評估

Azure Migrate 評估包含內部部署 VM 是否與 Azure 相容、建議的 Azure VM 正確大小調整，以及預估的 Azure 每月成本等相關資訊。 

![評估報告](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>檢閱信賴評等

![評估顯示](./media/migrate-scenarios-assessment/assessment-display.png)

您的評估會獲得 1 顆星到 5 顆星的信賴評等 (1 顆星最低，5 顆星最高)。
- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 此評等可協助您預估 Azure Migrate 所提供大小建議的可靠性。
- 信賴評等在您進行「以效能為基礎的大小調整」時非常實用，因為 Azure Migrate 可能沒有足夠的資料點都可進行以使用率為基礎的大小調整。 對於「作為內部部署調整大小」，信賴評等一律是 5 顆星，因為 Azure Migrate 擁有調整 VM 大小所需的所有資料點。
- 根據可用資料點的百分比提供評量的信賴評等：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星

#### <a name="verify-readiness"></a>確認整備程度

![評估整備](./media/migrate-scenarios-assessment/azure-readiness.png)  

評估報告會顯示彙總在資料表中的資訊。 請注意，為了顯示以效能為基礎的大小調整，Azure Migrate 需要下列資訊。 如果無法收集此資訊，大小調整評估可能不會正確。

- CPU 和記憶體的使用量資料。
- 連結至 VM 的每個磁碟，其所具有的讀取/寫入 IOPS 及輸送量。
- 連結至 VM 的每個網路介面卡，其所具有的網路輸入/輸出資訊。


**設定** | **指示** | **詳細資料**
--- | --- | ---
**Azure VM 整備程度** | 指出 VM 是否已可移轉 | 可能的狀態：</br><br/>- 可供 Azure 使用<br/><br/>- 有條件的備妥 <br/><br/>- 未備妥供 Azure 使用<br/><br/>- 整備程度未知<br/><br/> 如果 VM 未準備好，我們會示範一些補救步驟。
**Azure VM 大小** | 針對準備好的 VM，我們會建議 Azure VM 大小。 | 大小調整建議取決於評估屬性：<br/><br/>- 如果您之前使用以效能為基礎的大小調整，大小調整會考慮 VM 的效能記錄。<br/><br/>- 如果您之前使用「內部部署形式」，則大小調整會以內部部署 VM 的大小為基礎，而不會使用使用量資料。
**建議的工具** | 因為我們的機器會執行代理程式，Azure Migrate 會查看在機器內部執行的程序，並識別機器是否為資料庫機器。
**VM 資訊** | 此報告會顯示內部部署 VM 的設定，包括作業系統、開機類型、磁碟和儲存體資訊。




#### <a name="review-monthly-cost-estimates"></a>檢閱每月成本預估值

此檢視會顯示在 Azure 中執行 VM 的計算和儲存總成本，以及每部機器的詳細資訊。 

![評估整備](./media/migrate-scenarios-assessment/azure-costs.png) 

- 會使用機器的大小建議來計算成本預估值。
- 系統會彙總群組內所有 VM 之計算和儲存的每月預估成本。 


## <a name="conclusion"></a>結論

在此案例中，我們已：

> [!div class="checklist"]
> * 使用 DMA 工具評估我們的內部部署資料庫。
> * 使用相依性對應與 Azure Migrate 服務評估我們的內部部署 VM。
> * 檢閱評估，以確保我們的內部部署資源已可供移轉至 Azure。

## <a name="next-steps"></a>後續步驟

讓我們繼續進行下一個案例，以將內部部署 VM 和資料庫[隨即移轉](migrate-scenarios-lift-and-shift.md)至 Azure。



