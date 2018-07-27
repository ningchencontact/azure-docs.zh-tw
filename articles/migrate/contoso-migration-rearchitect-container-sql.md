---
title: 在 Azure 容器和 Azure SQL Database 中重新建構 Contoso 應用程式 | Microsoft Docs
description: 在 Azure Windows 容器和 Azure SQL Database 中重新建構應用程式。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003192"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso 移轉：將內部部署應用程式重新建構至 Azure 容器和 Azure SQL Database

本文說明 Contoso 如何在 Azure 中遷移及重新建構其 SmartHotel 應用程式。 他們會將應用程式前端 VM 遷移至 Azure Windows 容器，以及將應用程式資料庫遷移至 Azure SQL 資料庫中。

這份文件是系列文章中的其中一篇，說明 Contoso 這家虛構的公司如何將其內部部署資源移轉至 Microsoft Azure 雲端。 整個系列文章中包含背景資訊和說明如何設定移轉基礎結構的案例、評估內部部署資源的移轉，以及執行不同類型的移轉。 案例會變得越來越複雜，而我們也將不定期增加其他文章。

**文章** | **詳細資料** | **狀態**
--- | --- | ---
[文章 1：概觀](contoso-migration-overview.md) | 簡單介紹 Contoso 的移轉策略、文章系列以及我們使用的範例應用程式。 | 可用
[文章 2：部署 Azure 基礎結構](contoso-migration-infrastructure.md) | 說明 Contoso 如何準備其內部部署和 Azure 基礎結構以進行移轉。 所有移轉文章都使用相同的基礎結構。 | 可用
[文章 3：評估內部部署資源](contoso-migration-assessment.md)  | 顯示 Contoso 如何評估在 VMware 上執行的內部部署兩層式 SmartHotel 應用程式。 Contoso 利用 [Azure Migrate](migrate-overview.md) 服務來評估應用程式 VM，並利用 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) 來評估應用程式 SQL Server 資料庫。 | 可用
[文章 4：將應用程式重新裝載至 Azure VM 和 SQL 受控執行個體](contoso-migration-rehost-vm-sql-managed-instance.md) | 說明 Contoso 如何為 SmartHotel 應用程式執行隨即移轉至 Azure 的作業。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 來移轉應用程式前端 VM，並使用 [Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)將應用程式資料庫移轉至 SQL 受控執行個體。 | 可用
[文章 5：將應用程式重新裝載至 Azure VM](contoso-migration-rehost-vm.md) | 說明 Contoso 如何單純憑藉 Site Recovery 來移轉 SmartHotel 應用程式 VM。 | 可用
[文章 6：將應用程式重新裝載至 Azure VM 和 SQL Server AlwaysOn 可用性群組](contoso-migration-rehost-vm-sql-ag.md) | 說明 Contoso 如何遷移 SmartHotel 應用程式。 Contoso 使用 Site Recovery 來移轉應用程式 VM，並使用資料庫移轉服務，將應用程式資料庫移轉至受到 SQL Server 可用性群組保護的 SQL Server 叢集。 | 可用
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](contoso-migration-rehost-linux-vm.md) | 說明 Contoso 如何使用 Site Recovery 執行將 Linux osTicket 應用程式隨即移轉至 Azure VM 的作業 | 可用
[文章 8：將 Linux 應用程式重新裝載至 Azure VM 和 Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | 說明 Contoso 如何使用 Site Recovery 將 Linux osTicket 應用程式移轉至 Azure VM，並使用 MySQL Workbench 將應用程式資料庫移轉至 Azure MySQL Server 執行個體。 | 可用
[文章 9：將應用程式重構到 Azure Web 應用程式和 Azure SQL 資料庫](contoso-migration-refactor-web-app-sql.md) | 示範 Contoso 如何將 SmartHotel 應用程式遷移至 Azure Web 應用程式，以及如何將應用程式資料庫遷移至 Azure SQL Server 執行個體 | 可用
[文章 10：將 Linux 應用程式重構到 Azure Web Apps 和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 示範 Contoso 如何將 Linux osTicket 應用程式遷移至多個網站中的 Azure Web Apps (與 GitHub 整合以達到持續傳遞)。 他們會將應用程式資料庫遷移至 Azure MySQL 執行個體。 | 可用
文章 11：在 Azure 容器 和 Azure SQL Database 上重新建構應用程式 | 示範 Contoso 如何將其 SmartHotel 應用程式遷移和重新建構至 Azure。 他們會將應用程式的 Web 層重新建構為 Windows 容器及 Azure SQL Database 中的應用程式資料庫。 | 本文。
[文章 12：在 Azure 容器和 Azure SQL Database 上重新建構應用程式](contoso-migration-rearchitect-container-sql.md) | 示範 Contoso 如何將其 SmartHotel 應用程式遷移和重新建構至 Azure。 他們會將應用程式的 Web 層重新建構為 Windows 容器及 Azure SQL Database 中的應用程式資料庫。 | 可用
[文章 13：重建 Azure 中的應用程式](contoso-migration-rebuild.md) | 示範 Contoso 如何使用各種 Azure 功能和服務 (包括應用程式服務、Azure Kubernetes、Azure Functions、認知服務及 Cosmos DB) 重建其 SmartHotel 應用程式。 | 可用

在本文中，Contoso 會遷移兩層式 Windows。 .NET SmartHotel 應用程式移轉至 Azure。 如果您想使用此應用程式，您可以從 [GitHub](https://github.com/Microsoft/SmartHotel360) 下載其開放原始碼。

## <a name="business-drivers"></a>商業動機

IT 領導小組與其商務合作夥伴密切合作，以了解他們從此次移轉想要實現什麼目標：

- **因應業務成長**：Contoso 的業務量日益增多，對內部部署系統和基礎結構造成了壓力。
- **提高效率**：Contoso 必須移除不必要的程序，並且簡化開發人員和使用者的程序。  企業亟需快速且不浪費時間或金錢的 IT 服務，進而更快滿足客戶的需求。
- **提高靈活性**：Contoso IT 必須能夠更快因應企業的需求。 其因應速度必須能夠比市場變化更快，才能更在全球經濟中獲致成功。  它不得礙事，或成為企業的絆腳石。
- **調整**：隨著企業順利成長，Contoso IT 必須提供能夠同步成長的系統。
- **成本**：Contoso 想要將授權成本降至最低。

## <a name="migration-goals"></a>移轉目標

Contoso 雲端小組已針對此次移轉擬定好各項目標。 並用這些目標用來判斷最合適的移轉方法。

**目標** | **詳細資料**
--- | --- 
**應用程式需求** | Azure 中的應用程式應與現在一樣重要。<br/><br/> 應具有目前 VMWare 中的相同效能功能。<br/><br/> 他們想要停止支援目前執行應用程式的 Windows Server 2008 R2，而且願意將心力投注在應用程式上。<br/><br/> 他們想要從 SQL Server 2008 R2 移動到現代的 PaaS 資料庫平台，這樣可減少管理需求。<br/><br/> Contoso 想要盡可能運用 SQL Server 授權及軟體保證中的投資。<br/><br/> 他們想要可以相應增加應用程式 Web 層。
**限制** | 應用程式由相同 VM 上執行的 ASP.NET 應用程式和 WCF 服務所組成。 他們想要將其分成兩個使用 Azure App Service 的 Web 應用程式。 
**Azure 需求** | 他們想要將應用程式移至 Azure，並在容器中執行以延長應用程式的壽命。 他們不想要完全從頭開始實作 Azure 中的應用程式。 

## <a name="solution-design"></a>解決方案設計

擬定好目標和需求後，Contoso 會設計和檢閱部署解決方案，並識別移轉程序，包括將用於移轉的 Azure 服務。

### <a name="current-app"></a>目前的應用程式

- SmartHotel 內部部署應用程式會分層至兩個 VM (WEBVM 和 SQLVM)。
- 這些 VM 位於 VMware ESXi 主機 **contosohost1.contoso.com** (6.5 版)
- VMware 環境是由 VM 上執行的 vCenter Server 6.5 (**vcenter.contoso.com**) 進行管理。
- Contoso 有內部部署資料中心 (contoso-datacenter) 以及內部部署網域控制站 (**contosodc1**)。
- 移轉完成之後，將會解除委任 Contoso 資料中心的內部部署 VM。


### <a name="proposed-architecture"></a>建議的架構

- 針對應用程式的資料庫層，Contoso 已透過[這篇文章](https://docs.microsoft.com/azure/sql-database/sql-database-features)來比較 Azure SQL Database 和 SQL Server。 因為下列幾個原因，他們決定使用 Azure SQL Database：
    - Azure SQL Database 是由關聯式資料庫管理的服務。 其在多個服務層級上提供可預測的效能，而且幾乎免管理。 優點包括無須停機的動態延展性、內建智慧最佳化及全球延展性和可用性。
    - 他們可以利用輕量型 Data Migration Assistant (DMA) 來評估內部部署資料庫，並將其遷移到 Azure SQL。
    - 並且可以透過軟體保證使用適用於 SQL Server 的 Azure Hybrid Benefit，以折扣優惠在 SQL Database 上交換現有授權。 這可以提供最多 30% 的折扣。
    - SQL Database 提供多種安全性功能，包括永遠加密、動態資料遮罩和資料列層級的防護/威脅偵測。
- 針對應用程式 Web 層，他們已決定將其轉換成使用 Visual Studio 的 Windows 容器。
    - 他們會使用 Azure Service Fabric 部署應用程式，並從 Azure Container Registry (ACR) 提取 Windows 容器映像。
    - 用來擴充應用程式以包含情感分析的原型會實作為 Service Fabric 中的另一個服務，並連線至 Cosmos DB。  這將會讀取推文中的資訊，並顯示在應用程式上。

    ![案例架構](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>解決方案檢閱
Contoso 會透過比較一份優缺點清單，來評估其建議設計。

**考量** | **詳細資料**
--- | ---
**優點** | 必須變更 SmartHotel 應用程式的程式碼才能遷移至 Azure Service Fabric。 不過，這很簡單，使用 Service Fabric SDK Tools 來執行變更即可。<br/><br/> 移轉到 Service Fabric 後，他們可以開始開發微服務，並在一段時間後快速地將微服務新增至應用程式，而這完全不會對原始程式碼基底造成不良影響。<br/><br/> Windows 容器會提供與一般容器相同的優點。 其提升了靈活度、可攜性和控制能力。<br/><br/> 他們可以使用 SQL Server 和 Windows Server 的 Azure Hybrid Benefit 來妥善運用軟體保證中的投資。<br/><br/> 在移轉之後，他們將不再需要支援 Windows Server 2008 R2。 [深入了解](https://support.microsoft.com/lifecycle)。<br/><br/> 他們可以使用多個執行個體來設定應用程式的 Web 層，使它不再是單一失敗點。<br/><br/> 他們將不再依賴過時的 SQL Server 2008 R2。<br/><br/> SQL Database 可支援 Contoso 的技術需求。 他們使用 Data Migration Assistant 來評估內部部署資料庫，並發現其可相容。<br/><br/> SQL Database 擁有內建容錯功能，無須 Contoso 進行設定。 這可確保資料層不再是單一的容錯移轉點。
**缺點** | 容器比其他移轉選項複雜得多。 容器上的學習曲線 (learning curve) 可能會是 Contoso 的難題。  儘管有曲線，容器還是會帶來新的複雜等級，以提供更多價值。<br/><br/> Contoso 的營運團隊必須提升能力，以了解和支援適用於應用程式的 Azure、容器和微服務。<br/><br/> 如果他們使用 Data Migration Assistant 來遷移資料庫，而不是使用 Data Migration Service，Contoso 將不會有可進行大規模移轉的基礎結構。



### <a name="migration-process"></a>移轉程序

1. Contoso 會佈建適用於 Windows 的 Azure 服務網狀架構叢集。
2. 他們佈建 Azure SQL 執行個體，並將 SmartHotel 資料庫遷移至此。
3. 他們會使用 Service Fabric SDK Tools 將 Web 層 VM 轉換成 Docker 容器。
4. 他們會使服務網狀架構叢集與 ACR 連線，並使用 Azure 服務網狀架構部署應用程式。

    ![移轉程序](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 服務

**服務** | **說明** | **成本**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 他們會使用 DMA 來評定和偵測可能影響 Azure 中資料庫功能的相容性問題。 DMA 會評定 SQL 來源與目標之間的功能同位，並提出效能和可靠性改善建議。 | 此工具可免費下載。
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | 完全受控的智慧型關聯式雲端資料庫服務。 | 根據功能、輸送量和大小計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | 儲存所有容器部署類型的映像。 | 根據功能、儲存體和使用期間計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | 建置及操作全年持續運作，並能隨時調整的分散式應用程式 | 根據大小、位置和計算節點的持續時間計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/service-fabric/)。

## <a name="prerequisites"></a>必要條件

在執行此案例之前，您 (和 Contoso) 必須準備好下列事項：

**需求** | **詳細資料**
--- | ---
**Azure 訂用帳戶** | 當您在這系列的第一篇文章中執行評量時，您應該已經建立訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。<br/><br/> 如果您使用現有訂用帳戶，而且您不是系統管理員，則需要與系統管理員合作，讓其指派擁有者或參與者權限給您。
**Azure 基礎結構** | [了解](contoso-migration-infrastructure.md) Contoso 如何設定 Azure 基礎結構。
**開發人員必要條件** | 在開發人員工作站上，Contoso 需要下列工具：<br/><br/> - [Visual Studio 2017 Community 版本：15.5 版](https://www.visualstudio.com/)<br/><br/> - 已啟用.NET 工作負載。<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 或更新版本](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> 已設定為使用 Windows 容器的 - [Docker CE (Windows 10) 或 Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>案例步驟

以下是 Contoso 執行移轉的方式：

> [!div class="checklist"]
> * **步驟 1：在 Azure 中佈建 SQL Database 執行個體**：Contoso 會在 Azure 中佈建 SQL 執行個體。 將前端 Web VM 遷移至 Azure 容器後，應用程式前端 Web 的容器執行個體會指向這個資料庫。
> * **步驟 2：佈建 Azure Service Fabric**：他們會佈建 Service Fabric 叢集。
> * **步驟 4：使用 DMA 來遷移資料庫**：他們會使用 Data Migration Assistant 來遷移應用程式資料庫。
> * **步驟 5：將應用程式轉換成容器**：他們會使用 Visual Studio 和 SDK Tools 來將應用程式轉換成容器。
> * **步驟 6：發佈應用程式**：他們會將應用程式發佈至 ACR 與 Service Fabric 叢集。
> * **步驟 7：擴充應用程式**：公開應用程式之後，他們會將其擴充以善用 Azure 功能，並將它重新發佈至 Azure。



## <a name="step-1-provision-an-azure-sql-database"></a>步驟 1：佈建 Azure SQL Database

1. 他們會選擇在 Azure 中建立 SQL Database。 

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. 指定資料庫名稱以符合在內部部署 VM 上執行的資料庫 (**SmartHotel.Registration**)。 他們會將資料庫置於 ContosoRG 資源群組。 這是用於 Azure 中生產資源的資源群組。

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. 在主要區域中設定新的 SQL Server 執行個體 (**sql-smarthotel-eus2**)。

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. 他們會設定符合其伺服器和資料庫需求的定價層。 並選擇使用 Azure Hybrid Benefit 來節省成本，因為他們已經有 SQL Server 授權。
5. 針對大小，他們使用以虛擬核心為基礎的購買方式，並為預期的需求設定限制。

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. 然後，他們會建立資料庫執行個體。

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. 建立執行個體之後，他們會開啟資料庫，並記下在使用 Database Migration Assistance 進行移轉時所需的詳細資料。

    ![佈建 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**需要其他協助？**

- [說明](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)如何佈建 SQL Database。
- [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)虛擬核心的資源限制。



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>步驟 2：建立 ACR 和佈建 Azure 容器

Azure 容器會使用 Web VM 的匯出檔案來建立。 容器會裝載在 Azure Container Registry (ACR)。


1. Contoso 會在 Azure 入口網站中建立 Container Registry。

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. 他們會提供登錄的名稱 (**contosoacreus2**)，並將它放在主要區域中，也就是基礎結構資源所用的資源群組。 他們會啟用管理使用者的存取權，並將它設為進階 SKU，以便他們可以利用異地複寫。

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>步驟 3：佈建 Azure Service Fabric

SmartHotel 容器會在 Azure Service Fabric 叢集中執行。 Contoso 會建立 Service Fabric 叢集，如下所示：

1. 從 Azure Marketplace 建立 Service Fabric 資源

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. 在 [基本資料] 中，他們會提供叢集的唯一 DS 名稱，用來存取內部部署 VM 的認證。 他們會將資源放在主要區域 (美國東部 2) 的生產資源群組 (**ContosoRG**) 中。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. 在 [節點類型組態] 中，他們會輸入節點類型名稱、持久性設定、VM 大小和應用程式端點。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. 在 [建立金鑰保存庫] 中，他們會在其基礎結構資源群組中建立新的金鑰保存庫，以用來存放憑證。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. 在 [存取原則] 中，他們會啟用虛擬機器的存取，以便部署金鑰保存庫。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. 指定憑證的名稱。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. 在 [摘要] 頁面中，他們會複製用來下載憑證的連結。 這是連線至 Service Fabric 叢集的必要項目。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. 通過驗證之後，他們就會佈建叢集。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. 在 [憑證匯入精靈] 中，他們會將下載的憑證匯入開發人員機器。 憑證會用來對叢集進行驗證。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. 佈建叢集之後，他們就可連線至 Service Fabric 叢集的 Explorer。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. 必須選取正確的憑證。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer 會隨即載入，而 Contoso 系統管理員可以管理該叢集。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>步驟 3：使用 DMS 遷移資料庫

Contoso 將會使用 DMA 來遷移 SmartHotel 資料庫。

### <a name="install-dma"></a>安裝 DMA

1. 他們從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595)將工具下載至內部部署的 SQL Server VM (**SQLVM**)。
2. 他們在 VM 上執行安裝程式 (DownloadMigrationAssistant.msi)。
3. 在 [完成] 頁面上，他們會先選取 [啟動 Microsoft Data Migration Assistant]，再完成精靈。

### <a name="configure-the-firewall"></a>設定防火牆

若要連線至 Azure SQL Database，防火牆規則是必要的。

1. 在資料庫的 [防火牆與虛擬網路] 內容中，他們會允許存取 Azure 服務，並針對內部部署 SQL Server VM 的用戶端 IP 位址新增規則。
2. 伺服器層級的防火牆規則已建立。

    ![防火牆](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

需要其他協助？

[深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules)建立及管理 Azure SQL Database 的防火牆規則。

### <a name="migrate"></a>移轉

1. 在 DMA 中建立新專案 (**SmartHotelDB**)，然後選取 [移轉] 
2. 他們將來源伺服器類型選取為 [SQL Server]，並以 **Azure SQL Database** 作為目標。 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. 在移轉詳細資料中，他們新增 **SQLVM** 作為來源伺服器，並新增 **SmartHotel.Registration** 資料庫。 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. 他們收到錯誤，似乎與驗證有關。 不過，在調查之後，問題是出自資料庫名稱中的句號 (.)。 他們決定使用 **SmartHotel-Registration** 名稱佈建新的 SQL 資料庫，以作為解決此問題的方法。 當他們再次執行 DMA 時，他們就能選取 **SmartHotel-Registration**，並繼續執行精靈。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. 在 [選取物件] 中，他們會選取資料庫資料表，並產生 SQL 指令碼。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. 在 DMS 建立指令碼後，按一下 [部署結構描述]。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA 會確認部署成功。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. 現在，他們可以開始進行移轉。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. 移轉完成之後，Contoso 可以驗證資料庫是否正在 Azure SQL 執行個體上執行。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. 他們會在 Azure 入口網站中刪除額外的 SQL 資料庫 **SmartHotel.Registration**。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>步驟 4：將應用程式轉換成容器

內部部署應用程式是傳統的三層式應用程式：

- 其中包含 WebForms 和連線至 SQL Server 的 WCF 服務。
- 它會使用 Entity Framework 來整合 SQL 資料庫中的資料，並透過 WCF 服務將其公開。
- WebForms 應用程式會與 WCF 服務互動。

Contoso 會使用 Visual Studio 和 SDK Tools 將應用程式轉換成容器，如下所示：

1. 他們會從本機將存放庫複製到開發人員機器：

    **git 複製https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![容器](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. 使用 Visual Studio 從本機存放庫的 **SmartHotel360-internal-booking-apps\src\Registration** 目錄開啟方案檔 (SmartHotel.Registration.sln)。  其中會顯示兩個應用程式。 Web 前端 SmartHotel.Registration.Web 和 WCF 服務應用程式 SmartHotel.Registration.WCF。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. 使用滑鼠右鍵按一下 [Web 應用程式] > [新增]  >  [容器協調器支援]。
4. 在 [新增容器協調器支援]中，選取 [Service Fabric]。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso 會針對 SmartHotel.Registration.WCF 應用程式重複此程序。
6. 現在，Contoso 會檢查此解決方案有什麼改變。

    - 新的應用程式是 **SmartHotel.RegistrationApplication/**
    - 它包含兩個服務：**SmartHotel.Registration.WCF**和 **SmartHotel.Registration.Web**。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio 已建立 Docker 檔案，並將所需的映像從本機提取到開發人員機器。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Visual Studio 會建立並開啟資訊清單檔 (**ServiceManifest.xml**)。 此檔案會告知 Service Fabric 如何在容器部署至 Azure 時設定該容器。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. 另一個資訊清單檔 (**ApplicationManifest.xml) 包含適用於容器的組態應用程式。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>步驟 5：發佈應用程式


最後，Contoso 可以將應用程式發佈至 ACR 與 Service Fabric 叢集。

> [!NOTE]
> SmartHotel 應用程式中已進行一些與 Service Fabric 叢集相關的變更。 您可以從 [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) 下載原始和現代化應用程式的程式碼。 已變更的檔案是 **AppliationModern/ApplicationParameters/Cloud.xml**。


1. 在 Visual Studio 中，更新連接字串以將應用程式連線至 Azure SQL Database。 連接字串可在 Azure 入口網站的資料庫中找到。

    ![發佈](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso 會使用 Visual Studio 將應用程式發佈到 Service Fabric。 以滑鼠右鍵按一下 Service Fabric 應用程式 > [發佈]。

    ![發佈](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. 他們會選取訂用帳戶、連線端點和 ACR。 然後按一下 [發佈]。

    ![發佈](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. 部署完成之後，SmartHotel 現在會執行 Service Fabric。

    ![發佈](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. 若要連線至應用程式，Contoso 會將流量導向 Azure 負載平衡器的公用 IP 位址 (位於 Service Fabric 節點的前面)。

    ![發佈](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>步驟 6：擴充應用程式並重新發佈

當 SmartHotel 應用程式和資料庫在 Azure 中執行之後，Contoso 想要擴充應用程式。

- Contoso 的開發人員正在設計新的.NET Core 應用程式原型，讓其在 Service Fabric 叢集上執行。
- 此應用程式將用來從 CosmosDB 提取情感資料。
- 此資料將會以推文的格式呈現，並由無伺服器的 Azure 函式和認知服務的文字分析 API 進行處理。

### <a name="provision-azure-cosmos-db"></a>佈建 Azure Cosmos DB

第一個步驟，Contoso 會佈建 Azure Cosmos 資料庫。

1. 他們會從 Azure Marketplace 建立 Azure Cosmos DB 資源。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. 提供資料庫名稱 (**contososmarthotel**)、選取 SQL API，並將資源放在主要區域 (美國東部 2) 的生產資源群組中。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. 在 [使用者入門] 中，他們選取 [資料總管] 並加入新的集合。
4. 在 [新增集合] 中，他們提供識別碼並設定儲存體容量與輸送量。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. 在入口網站中，開啟新的資料庫 > [集合] > [文件]，然後按一下 [新文件]。
6. 他們會將下列 JSON 程式碼貼到文件視窗中。 這是單一推文形式的範例資料。

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. 他們會找出 Cosmos DB 端點和驗證金鑰。 這些會用來將應用程式連線到集合。 在資料庫中，按一下 [金鑰]，並將 URI 和主要金鑰複製到記事本。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>更新情感應用程式

佈建好 Cosmos DB 後，Contoso 可以設定應用程式來與之連線。

1. 在 Visual Studio 中，從 [方案總管] 開啟 ApplicationModern\ApplicationParameters\cloud.xml 檔案。

    ![情感應用程式](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. 填入下列兩個參數：

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![情感應用程式](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>重新發佈應用程式

擴充應用程式之後，Contoso 會將其重新發佈到 Azure。

1. 在入口網站中，以滑鼠右鍵按一下 Service Fabric 應用程式 > [發佈]。

    ![重新發佈](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. 他們會選取訂用帳戶、連線端點和 ACR。 然後按一下 [發佈]。

    ![重新發佈](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. 部署完成之後，SmartHotel 現在會執行 Service Fabric。 Servie Fabric 管理主控台現在會顯示三個服務。

    ![重新發佈](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso 可以逐一點選服務，以查看 SentimentIntegration 應用程式是否已啟動並且正在執行

    ![重新發佈](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>移轉之後進行清除

移轉之後，Contoso 必須完成以下的清除步驟：  

- 從 vCenter 清查中移除內部部署 VM。
- 從本機備份作業中移除 VM。
- 更新內部文件以顯示 SmartHotel 應用程式的新位置。 資料庫會顯示為在 Azure SQL 資料庫中執行，而前端會顯示為在 Service Fabric 中執行。
- 檢閱與已解除委任 VM 互動的任何資源，並更新任何相關的設定或文件，以反映新的組態。


## <a name="review-the-deployment"></a>檢閱部署

對於 Azure 中的移轉後資源，Contoso 必須能執行一切功能並保護其新的基礎結構。

### <a name="security"></a>安全性

- Contoso 必須確保其新的 **SmartHotel-Registration** 資料庫很安全。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特別是，他們應將容器更新為搭配使用 SSL 與憑證。
- 他們應該考慮使用金鑰保存庫來保護其 Service Fabric 應用程式的祕密。 [深入了解](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups"></a>備份

- Contoso 需要檢閱 Azure SQL Database 的備份需求。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 他們應考慮實作容錯移轉群組，為該資料庫提供區域性容錯移轉。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- 他們可以使用適用於 ACR 進階 SKU 的異地複寫。 [深入了解](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- 當用於容器的 Web App 可供使用時，Contoso 須考慮在主要的美國東部 2 和美國中部區域部署 Web 應用程式。 他們可以設定流量管理員，以確保在發生區域性的運行中斷時可進行容錯移轉。

### <a name="licensing-and-cost-optimization"></a>授權和成本最佳化

- 部署好所有資源之後，Contoso 應根據[基礎結構規劃](contoso-migration-infrastructure.md#set-up-tagging)來指派 Azure 標記。
- 所有授權費用都會併入 Contoso 使用的 PaaS 服務中。 這將會從 EA 中扣除。
1. Contoso 會啟用 Microsoft 子公司 Cloudyn 授權的 Azure 成本管理。 它是一種多雲端成本管理解決方案，可協助您利用和管理 Azure 和其他雲端資源。  [深入了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 

## <a name="conclusion"></a>結論

在本文中，Contoso 已透過將應用程式前端 VM 遷移至 Service Fabric，來重構 SmartHotel 應用程式。 他們已將應用程式資料庫遷移至 Azure SQL 資料庫。





