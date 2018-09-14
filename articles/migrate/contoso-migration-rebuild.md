---
title: 在 Azure 中重建 Contoso 內部部署應用程式 | Microsoft Docs
description: 了解 Contoso 如何使用 Azure App Service、Kubernetes 服務、CosmosDB、Azure Functions 和認知服務在 Azure 中重建應用程式。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 17212c076ef296a24021213b0aa887de930a44ac
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783350"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso 移轉：在 Azure 中重建內部部署應用程式

本文會示範 Contoso 如何在 Azure 中移轉及重建其 SmartHotel360 應用程式。 其會將應用程式的前端 VM 遷移至 Azure App Services Web 應用程式。 應用程式後端的建置，則是使用 Azure Kubernetes Service (AKS) 所管理容器中部署的微服務來進行。 該網站會與提供寵物相片功能的 Azure Functions 互動。 

這份文件是系列文章中的其中一篇，說明 Contoso 這家虛構的公司如何將其內部部署資源移轉至 Microsoft Azure 雲端。 整個系列文章中包含背景資訊和說明如何設定移轉基礎結構的案例、評估內部部署資源的移轉，以及執行不同類型的移轉。 案例會變得越來越複雜，而我們也將不定期增加其他文章。

**文章** | **詳細資料** | **狀態**
--- | --- | ---
[文章 1：概觀](contoso-migration-overview.md) | 簡單介紹 Contoso 的移轉策略、文章系列以及我們使用的範例應用程式。 | 可用
[文章 2：部署 Azure 基礎結構](contoso-migration-infrastructure.md) | 說明 Contoso 如何準備其內部部署和 Azure 基礎結構以進行移轉。 所有移轉文章都使用相同的基礎結構。 | 可用
[文章 3：評估內部部署資源](contoso-migration-assessment.md)  | 顯示 Contoso 如何評估在 VMware 上執行的內部部署兩層式 SmartHotel360 應用程式。 Contoso 利用 [Azure Migrate](migrate-overview.md) 服務來評估應用程式 VM，並利用 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) 來評估應用程式 SQL Server 資料庫。 | 可用
[文章 4：在 Azure VM 和 SQL 受控執行個體上重新裝載應用程式](contoso-migration-rehost-vm-sql-managed-instance.md) | 說明 Contoso 如何為 SmartHotel360 應用程式執行隨即移轉至 Azure 的作業。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 來移轉應用程式前端 VM，並使用 [Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)將應用程式資料庫移轉至 SQL 受控執行個體。 | 可用
[文章 5：在 Azure VM 上重新裝載應用程式](contoso-migration-rehost-vm.md) | 說明 Contoso 如何單純憑藉 Site Recovery 來移轉 SmartHotel360 應用程式 VM。 | 可用
[文章 6：將應用程式重新裝載至 Azure VM 和 SQL Server AlwaysOn 可用性群組](contoso-migration-rehost-vm-sql-ag.md) | 說明 Contoso 如何移轉 SmartHotel360 應用程式。 Contoso 使用 Site Recovery 來移轉應用程式 VM，並使用資料庫移轉服務，將應用程式資料庫移轉至受到 SQL Server 可用性群組保護的 SQL Server 叢集。 | 可用
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](contoso-migration-rehost-linux-vm.md) | 說明 Contoso 如何使用 Site Recovery 執行將 Linux osTicket 應用程式隨即移轉至 Azure VM 的作業 | 可用
[文章 8：在 Azure VM 和 Azure MySQL Server 上重新裝載 Linux 應用程式](contoso-migration-rehost-linux-vm-mysql.md) | 說明 Contoso 如何使用 Site Recovery 將 Linux osTicket 應用程式移轉至 Azure VM，並使用 MySQL Workbench 將應用程式資料庫移轉至 Azure MySQL Server 執行個體。 | 可用
[文章 9：在 Azure Web 應用程式和 Azure SQL 資料庫上重構應用程式](contoso-migration-refactor-web-app-sql.md) | 說明 Contoso 如何將 SmartHotel360 應用程式移轉至 Azure Web 應用程式，以及如何將應用程式資料庫移轉至 Azure SQL Server 執行個體 | 可用
[文章 10：將 Linux 應用程式重構到 Azure Web Apps 和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 示範 Contoso 如何將 Linux osTicket 應用程式遷移至多個網站中的 Azure Web Apps (與 GitHub 整合以達到持續傳遞)。 他們會將應用程式資料庫遷移至 Azure MySQL 執行個體。 | 可用
[文章 11：在 VSTS 上重構 TFS](contoso-migration-tfs-vsts.md) | 示範 Contoso 如何藉由將內部部署 Team Foundation Server (TFS) 部署遷移至 Azure 中的 Visual Studio Team Services (VSTS) 來對其進行遷移。 | 可用
[文章 12：在 Azure 容器和 SQL Database 上重新架構應用程式](contoso-migration-rearchitect-container-sql.md) | 說明 Contoso 如何將其 SmartHotel360 應用程式移轉和重新建構至 Azure。 他們會將應用程式的 Web 層重新建構為 Windows 容器及 Azure SQL Database 中的應用程式資料庫。 | 可用
文章 13：在 Azure 中重建應用程式 | 說明 Contoso 如何使用各種 Azure 功能和服務 (包括應用程式服務、Azure Kubernetes、Azure Functions、認知服務及 Cosmos DB) 重建其 SmartHotel360 應用程式。 | 本文。

在本文中，Contoso 會遷移兩層式 Windows。 NET SmartHotel360 應用程式 (執行於 VMware VM)，移轉至 Azure。 如果您想使用此應用程式，您可以從 [GitHub](https://github.com/Microsoft/SmartHotel360) 下載其開放原始碼。

## <a name="business-drivers"></a>商業動機

IT 領導小組與其商務合作夥伴密切合作，以了解他們從此次移轉想要實現什麼目標：

- **解決業務成長**：Contoso 正在成長。 他們想要在網站上為客戶提供差異化的體驗。
- **靈活度**：Contoso 的因應速度必須能夠比市場變化更快，才能更在全球經濟中獲致成功。 
- **調整**：隨著企業順利成長，Contoso IT 必須提供能夠同步成長的系統。
- **成本**：Contoso 想要將授權成本降至最低。

## <a name="migration-goals"></a>移轉目標

Contoso 雲端小組已針對此次移轉擬定好各項應用程式需求。 這些需求已用來判斷最合適的移轉方法：
 - Azure 中的應用程式應與現在一樣重要。 其應該妥善執行並可輕鬆調整。
 - 應用程式不應使用 IaaS 元件。 所有項目皆應建置為使用 PaaS 或無伺服器服務。
 - 應用程式組建應該在雲端服務中執行，且容器應該位於雲端中的全企業私人容器登錄中。
 - 其旅館必須接受應用程式所做的決策，所以用於寵物相片的 API 服務在真實世界中應該要準確可靠。 任何獲准進入的寵物都可以留在旅館內。

## <a name="solution-design"></a>解決方案設計

擬定好目標和需求後，Contoso 會設計和檢閱部署解決方案，並識別移轉程序，包括將用於移轉的 Azure 服務。

### <a name="current-app"></a>目前的應用程式

- SmartHotel360 內部部署應用程式會分層至兩個 VM (WEBVM 和 SQLVM)。
- 這些 VM 位於 VMware ESXi 主機 **contosohost1.contoso.com** (6.5 版)
- VMware 環境是由 VM 上執行的 vCenter Server 6.5 (**vcenter.contoso.com**) 進行管理。
- Contoso 有內部部署資料中心 (contoso-datacenter) 以及內部部署網域控制站 (**contosodc1**)。
- 移轉完成之後，將會解除委任 Contoso 資料中心的內部部署 VM。


### <a name="proposed-architecture"></a>建議的架構

- 應用程式的前端會部署成為其主要區域中的 Azure App Service Web 應用程式。
- Azure 函式會提供寵物相片上傳功能，且該網站會與這項功能互動。
- 寵物相片函式會利用認知服務視覺 API 和 CosmosDB。
- 網站的後端會使用微服務來建置。 這些微服務會部署到 Azure Kubernetes Service (AKS) 上所管理的容器。
- 容器會使用 VSTS 來建置，並會推送至 Azure Container Registry (ACR)。
- 目前，Contoso 會以手動方式使用 Visual Studio 部署 Web 應用程式和函式程式碼。
- 微服務會使用 PowerShell 指令碼，呼叫 Kubernetes 命令列工具來進行部署。

    ![案例架構](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>解決方案檢閱
Contoso 會透過比較一份優缺點清單，來評估其建議設計。

**考量** | **詳細資料**
--- | ---
**優點** | 使用 PaaS 和無伺服器解決方案來進行端對端部署可大幅減少 Contoso 必須提供的管理時間。<br/><br/> 移往微服務架構可讓 Contoso 在一段時間過後輕鬆地延伸其解決方案。<br/><br/> 在將新功能上線時，不必中斷任何現有的解決方案程式碼基底。<br/><br/> Web 應用程式會設定為配有多個執行個體，所以不會有單一失敗點。<br/><br/> 會啟用自動調整功能，讓應用程式能夠處理不同的流量。<br/><br/> 移往 PaaS 服務後，Contoso 可以淘汰在 Windows Server 2008 R2 作業系統上執行的過時解決方案。<br/><br/> CosmosDB 有內建容錯能力，Contoso 不必進行設定。 這表示資料層不再是單一的容錯移轉點。
**缺點** | 容器比其他移轉選項複雜得多。 其學習曲線可能會是 Contoso 的難題。  儘管有學習曲線的問題，但容器帶來了新的複雜度等級，而可提供許多價值。<br/><br/> Contoso 的營運團隊必須提升能力，以了解和支援適用於應用程式的 Azure、容器和微服務。<br/><br/> Contoso 尚未針對整個解決方案完全實作 DevOps。 他們必須在將服務部署至 AKS、函式和 App Service 時，考慮到這一點。



### <a name="migration-process"></a>移轉程序

1. Contoso 會佈建 ACR、AKS 和 CosmosDB。
2. 其會佈建基礎結構以供部署使用，包括 Azure Web 應用程式、儲存體帳戶、函式和 API。 
3. 基礎結構到位後，他們會使用 VSTS 建置其微服務容器映像，以將映像推送至 ACR。
4. Contoso 會使用 PowerShell 指令碼將這些微服務部署至 ASK。
5. 最後，他們會部署 Azure 函式和 Web 應用程式。

    ![移轉程序](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure 服務

**服務** | **說明** | **成本**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 簡化 Kubernetes 管理、部署和作業。 提供完全受控的 Kubernetes 容器協調流程服務。  | AKS 是免費服務。  只需就取用的虛擬機器以及相關聯的儲存體和網路資源支付費用。 [深入了解](https://azure.microsoft.com/pricing/details/kubernetes-service/)。
[Azure Functions](https://azure.microsoft.com/services/functions/) | 以事件驅動的無伺服器計算體驗，加快開發速度。 依需求進行調整。  | 只需就取用的資源支付費用。 根據每秒的資源取用量和執行次數計算方案的費用。 [深入了解](https://azure.microsoft.com/pricing/details/functions/)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | 儲存所有容器部署類型的映像。 | 根據功能、儲存體和使用期間計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | 快速建置、部署和調整在任何平台上執行的企業級 Web、行動裝置和 API 應用程式。 | App Service 方案以每秒計費。 [深入了解](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>必要條件

在執行此案例之前，您 (和 Contoso) 必須準備好下列事項：

**需求** | **詳細資料**
--- | ---
**Azure 訂用帳戶** | 當您在這系列的第一篇文章中執行評量時，您應該已經建立訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。<br/><br/> 如果您使用現有訂用帳戶，而且您不是系統管理員，則需要與系統管理員合作，讓其指派擁有者或參與者權限給您。
**Azure 基礎結構** | [了解](contoso-migration-infrastructure.md) Contoso 如何設定 Azure 基礎結構。
**開發人員必要條件** | 在開發人員工作站上，Contoso 需要下列工具：<br/><br/> - [Visual Studio 2017 Community 版本：15.5 版](https://www.visualstudio.com/)<br/><br/> 已啟用 .NET 工作負載。<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> 已設定為使用 Windows 容器的 [Docker CE (Windows 10) 或 Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>案例步驟

以下是 Contoso 執行移轉的方式：

> [!div class="checklist"]
> * **步驟 1：佈建 AKS 和 ACR**：Contoso 會使用 PowerShell 佈建受控 AKS 叢集和 Azure 容器登錄
> * **步驟 2：建置 Docker 容器**：他們會使用 VSTS 設定 Docker 容器的 CI，並將其推送至 ACR。
> * **步驟 3：部署後端微服務**：他們會部署基礎結構的其餘部分，以供後端微服務運用。
> * **步驟 4：部署前端基礎結構**：他們會部署前端基礎結構，包括寵物電話的 Blob 儲存體、Cosmos DB 和視覺 API。
> * **步驟 5：遷移後端**：他們會部署微服務並在 AKS 上執行，以遷移後端。
> * **步驟 6：發佈前端**：他們會將 SmartHotel360 應用程式發佈至 Azure App Service 以及寵物服務所會呼叫的函式應用程式。



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>步驟 1：佈建 AKS 叢集和 ACR

Contoso 會執行部署指令碼，使用 AKS 和 Azure Container Registry 建立受控 Kubernetes 叢集。

- 本節的指示會使用 **SmartHotel360-Azure-backend** 存放庫。
- **SmartHotel360-Azure-backend** GitHub 存放庫包含這部分部署的所有軟體。

其佈建方式如下：

1. 在開始之前，Contoso 會確保所使用的開發機器上已安裝所有先決條件軟體。 
2. 他們會使用 Git 從本機將存放庫複製到開發機器。

    **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso 會使用 Visual Studio Code 開啟資料夾並移至 **/deploy/k8s** 目錄，其中包含指令碼 **gen-aks-env.ps1**。
4. 他們會執行指令碼，使用 AKS 和 Container Registry 建立受控 Kubernetes 叢集。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  在檔案開啟時，他們會將 $location 參數更新為 **eastus2**，並儲存檔案。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. 他們會按一下 [檢視] > [整合式終端機] 以在 Code 中開啟整合式終端機。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. 在 PowerShell 整合式終端機中，他們會使用 Connect-AzureRmAccount 命令登入 Azure。 [深入了解](https://docs.microsoft.com/powershell/azure/get-started-azureps)如何開始使用 PowerShell。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. 他們會執行 **az login** 命令來驗證 Azure CLI，並遵循指示以使用其網頁瀏覽器進行驗證。 [深入了解](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)如何使用 Azure CLI 來登入。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. 他們會執行下列命令，以傳遞 ContosoRG 的資源群組名稱、AKS 叢集 smarthotel-aks-eus2 的名稱和新的登錄名稱。

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure 會建立另一個資源群組，其中包含 AKS 叢集的資源。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. 部署完成之後，Contoso 會安裝 **kubectl** 命令列工具。 Azure CloudShell 上已安裝此工具。

    **az aks install-cli**

12. 他們會執行 **kubectl get nodes** 命令來確認對叢集的連線。 節點的名稱和所自動建立資源群組中的 VM 名稱相同。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. 他們會執行下列命令來啟動 Kubernetes 儀表板： 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. 隨即會有瀏覽器索引標籤開啟為儀表板。 這是使用 Azure CLI 來建立通道的連線。 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>步驟 2：建置 Docker 容器

### <a name="create-a-vsts-and-build"></a>建立 VSTS 和組件

Contoso 會建立 VSTS 專案，並設定 CI 組建以建立容器，再將它推送至 ACR。 本節中的指示會使用 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存放庫。

1. 他們會從 visualstudio.com 建立新的帳戶 (**contosodevops360.visualstudio.com**)，並將它設定為使用 Git。

2. 他們會建立新的專案 (**smarthotelrefactor**)，此專案使用 Git 來進行版本控制，並使用 Agile 來進行工作流程。

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. 他們會匯入 GitHub 存放庫。

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. 在 [建置和發行] 中，他們會使用 VSTS Git 作為來源從匯入的存放庫建立新的定義。 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. 他們會選取從空白管線來開始。

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. 他們會選取 [裝載的 Linux 預覽] 作為組建定義。

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. 在 [第 1 階段] 中，他們會新增 [Docker Compose] 工作。 此工作會建置 Docker Compose。

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. 他們會重複相同步驟，以新增另一個 [Docker Compose] 工作。 此工作會將容器推送至 ACR。

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. 他們會選取第一個工作 (用來建置)，並為此組件設定 Azure 訂用帳戶、授權和 ACR。 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. 他們會指定 **docket-compose.yaml** 檔案在存放庫 **src** 資料夾中的路徑。 他們會選取建置服務映像，並包含最新的標記。 當動作變更為 [建置服務映像] 時，VSTS 工作的名稱會變更為 [自動建置服務]

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. 現在，Contoso 會設定第二個 Docker 工作 (用來推送)。 他們會選取訂用帳戶和 [smarthotelacreus2] ACR。 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. 他們會再次輸入 docker-compose.yaml 檔案的路徑，然後選取 [推送服務映像] 並包含最新的標記。 當動作變更為 [推送服務映像] 時，VSTS 工作的名稱會變更為 [自動推送服務]

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. 設定好 VSTS 工作後，Contoso 會儲存組建定義，並啟動建置程序。

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. 他們會按一下建置作業以檢查進度。

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. 建置完成之後，ACR 就會顯示新的存放庫，其中會填入微服務所使用的容器。

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>步驟 3：部署後端微服務

擁有所建立的 AKS 叢集和 Docker 映像組建後，Contoso 現在會部署基礎結構的其餘部分，以供後端微服務運用。

- 本節中的指示會使用 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存放庫。
- 在 **/deploy/k8s/arm** 資料夾中有一個指令碼可建立所有項目。 

他們會如下所示地進行部署：

1. Contoso 會輸入下列命令，使用 deploy.cmd 檔案在 ContosoRG 資源群組和 EUS2 區域中部署 Azure 資源：

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![部署後端](./media/contoso-migration-rebuild/backend1.png)

2. 他們會擷取每個資料庫的連接字串以供稍後使用。

    ![部署後端](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>步驟 4：部署前端基礎結構

Contoso 需要部署基礎結構以供前端應用程式使用。 他們會建立 Blob 儲存體容器來儲存寵物影像；建立 Cosmos 資料庫來儲存具有寵物資訊的文件；以及建立視覺 API 來供網站使用。 

本節的指示會使用 [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) 存放庫。

### <a name="create-storage-containers"></a>建立儲存體容器

1.  在 Azure 入口網站中，Contoso 會開啟已建立的儲存體帳戶，並按一下 [Blob]。
2.  他們會建立新的容器 (**Pets**)，並將公用存取層級設定為容器。 使用者會將其寵物相片上傳至此容器。

    ![儲存體 Blob](./media/contoso-migration-rebuild/blob1.png)

3. 他們會建立第二個名為 **settings** 的新容器。 具有所有前端應用程式設定的檔案將會放在此容器中。

    ![儲存體 Blob](./media/contoso-migration-rebuild/blob2.png)

4. 他們會擷取文字檔中的儲存體帳戶存取詳細資料，以供日後參考。

    ![儲存體 Blob](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>佈建 Cosmos 資料庫

Contoso 會佈建 Cosmos 資料庫以供寵物資訊使用。

1. 他們會在 Azure Marketplace 中建立 **Azure Cosmos DB**。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 他們會指定名稱 (**contososmarthotel**)、選取 SQL API，並將其放在美國東部 2 主要區域中的生產資源群組 ContosoRG 內。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 他們會在資料庫內新增集合，並為其設定預設容量與輸送量。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 他們會記下資料庫的連線資訊，以供日後參考。 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>佈建電腦視覺

Contoso 會佈建電腦視覺 API。 函式會呼叫此 API 來評估使用者所上傳的圖片。

1. 他們會在 Azure Marketplace 中建立**電腦視覺**執行個體。

     ![電腦視覺](./media/contoso-migration-rebuild/vision1.png)

2. 他們會在美國東部 2 主要區域的生產資源群組 ContosoRG 中佈建 API (**smarthotelpets**)。

    ![電腦視覺](./media/contoso-migration-rebuild/vision2.png)

3. 他們會將 API 的連線設定儲存至文字檔，以供日後參考。

     ![電腦視覺](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>步驟 5：在 Azure 中部署後端服務

現在，Contoso 需要部署 NGINX 輸入控制器，以允許輸入流量流向服務，然後再於 AKS 叢集中部署微服務。

本節中的指示會使用 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存放庫。



1. 他們會使用 Visual Studio Code 來更新 **/deploy/k8s/config_loal.yml** 檔案。 他們會透過先前記下的資訊更新各種資料庫連線。

     ![部署微服務](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > 本文未涵蓋某些組態設定 (例如 Active Directory B2C)。 存放庫中有這些設定的詳細資訊。

2. deploy.ps1 檔案會刪除所有叢集內容 (輸入和輸入控制器除外)，並部署微服務。 他們會以下列方式來執行：

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. 他們會執行下列命令來檢查服務的狀態：

    ```
    kubectl get services
    ```
4. 他們會開啟 Kubernetes 儀表板來檢閱部署：

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>步驟 6：發佈前端

在最後一個步驟中，Contoso 會將 SmartHotel360 應用程式發佈至 Azure App Service，以及寵物服務所呼叫的函式應用程式中。

本節中的指示會使用 [SmartHotel-public-web 存放庫。](https://github.com/Microsoft/SmartHotel360-public-web) 存放庫。

### <a name="set-up-web-app-to-use-contoso-resources"></a>將 Web 應用程式設定為使用 Contoso 資源

1. Contoso 會使用 Git 從本機將存放庫複製到開發機器：

    **git clone https://github.com/Microsoft/SmartHotel360-public-web.git**

2. 在 Visual Studio 中，他們會開啟資料夾以顯示存放庫中的所有檔案。

    ![發佈前端](./media/contoso-migration-rebuild/front-publish1.png)

3. 他們會對預設設定進行所需的設定變更。

    - 當 Web 應用程式啟動時，它會尋找 **SettingsUrl** 應用程式設定。
    - 這個變數必須包含設定檔的 URL。
    - 根據預設，所使用的設定會是公用端點。

4. 他們會更新 **/config-sample.json/sample.json** 檔案。 這是 Web 在使用公用端點時的設定檔。

    - 他們會使用 AKS API 端點、儲存體帳戶和 Cosmos 資料庫的值來編輯 **urls** 和 **pets_config** 區段。 
    - URL 應符合 Contoso 所會建立的新 Web 應用程式 DNS 名稱。
    - 對於 Contoso 來說，這是 **smarthotelcontoso.eastus2.cloudapp.azure.com**。

    ![發佈前端](./media/contoso-migration-rebuild/front-publish2.png)

5. 他們會在檔案更新後將其重新命名為 **smarthotelsettingsurl**，並將其上傳至他們稍早建立的儲存體 Blob 中。

     ![發佈前端](./media/contoso-migration-rebuild/front-publish3.png)

6. 他們會按一下檔案以取得 URL。 應用程式會在開始提取設定檔時使用此 URL。

    ![發佈前端](./media/contoso-migration-rebuild/front-publish4.png)

7. 他們會將 **appsettings.Production.json** 檔案中的 **SettingsUrl** 更新為新檔案的 URL。

    ![發佈前端](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>將網站部署至 Azure App Service

現在，Contoso 可以發佈其網站。


1. 他們會在 Visual Studio 2017 中啟用 Application Insights 監視。 為了這樣做，他們會在 [方案總管] 中選取 **PublicWeb** 專案，然後搜尋**新增 Application Insights**。 他們會向其部署基礎結構時所建立的 Application Insights 執行個體註冊應用程式。

    ![發佈網站](./media/contoso-migration-rebuild/deploy-website1.png)

2. 在 Visual Studio 中，他們會將 PublicWeb 專案連線至 App Insights，並進行更新以顯示其已完成設定。
 
    ![發佈網站](./media/contoso-migration-rebuild/deploy-website2.png)

3. 在 Visual Studio 中，他們會建立及發佈其 Web 應用程式。

    ![發佈網站](./media/contoso-migration-rebuild/deploy-website3.png)

5. 他們會指定應用程式名稱，並將其放在美國東部 2 主要區域中的生產資源群組 **ContosoRG** 內。

    ![發佈網站](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>將函式部署到 Azure

1. 他們會使用 Visual Studio 來建立和發佈函式。 為了這樣做，他們會以滑鼠右鍵按一下 [PetCheckerFunction] > [發佈]。 然後，他們會建立新的 App Service 函式。

     ![部署函式](./media/contoso-migration-rebuild/function1.png)

2. 他們會指定名稱 **smarthotelpetchecker**，並將其放在 ContosoRG 資源群組和新的 App Service 方案內。

     ![部署函式](./media/contoso-migration-rebuild/function2.png)

3. 他們會選取儲存體帳戶，並建立函式。

    ![部署函式](./media/contoso-migration-rebuild/function3.png)

4. 部署會開始，並將函式應用程式佈建至 Azure。 在 [發佈] 中，Contoso 會新增儲存體、Cosmos 資料庫和電腦視覺 API 的應用程式設定。

    ![部署函式](./media/contoso-migration-rebuild/function4.png)

5. 為了先在本機上執行函式，他們會在 **PetCheckerFunction/local.settings.json** 中更新設定。

    ![部署函式](./media/contoso-migration-rebuild/function5.png)

6. 函式部署完成後會出現在 Azure 入口網站中，且狀態為**執行中**。

    ![部署函式](./media/contoso-migration-rebuild/function6.png)


7. 他們會瀏覽至應用程式以測試 Pet Checker 應用程式是否如預期般運作，其位置在 [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets)。
8. 他們會按一下虛擬人偶以上傳圖片。

    ![部署函式](./media/contoso-migration-rebuild/function7.png)

9. 他們想要檢查的第一張相片是一頭小型犬。

    ![部署函式](./media/contoso-migration-rebuild/function8.png)

10. 應用程式傳回已接受的訊息。

    ![部署函式](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>檢閱部署

對於 Azure 中的移轉後資源，Contoso 必須能執行一切功能並保護其新的基礎結構。

### <a name="security"></a>安全性

- Contoso 必須確保其新的資料庫很安全。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 應用程式必須更新為搭配使用 SSL 與憑證。 容器執行個體應重新部署為會在 443 上接聽。
- 他們應該考慮使用金鑰保存庫來保護其 Service Fabric 應用程式的祕密。 [深入了解](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>備份和災害復原

- Contoso 需要檢閱 Azure SQL Database 的備份需求。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 他們應考慮實作 SQL 容錯移轉群組，為該資料庫提供區域性容錯移轉。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- 他們可以使用適用於 ACR 進階 SKU 的異地複寫。 [深入了解](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>授權和成本最佳化

- 部署好所有資源之後，Contoso 應根據[基礎結構規劃](contoso-migration-infrastructure.md#set-up-tagging)來指派 Azure 標記。
- 所有授權費用都會併入 Contoso 使用的 PaaS 服務中。 這將會從 EA 中扣除。
- Contoso 會啟用 Microsoft 子公司 Cloudyn 授權的 Azure 成本管理。 它是一種多雲端成本管理解決方案，可協助您利用和管理 Azure 和其他雲端資源。  [深入了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

## <a name="conclusion"></a>結論

在本文中，Contoso 在 Azure 中重建了 SmartHotel360 應用程式。 他們已在 Azure App Services Web 應用程式中重建內部部署應用程式的前端 VM。 他們已使用 Azure Kubernetes Service (AKS) 所管理容器中部署的微服務來建置應用程式後端。 他們已使用寵物相片應用程式增強應用程式的功能。




