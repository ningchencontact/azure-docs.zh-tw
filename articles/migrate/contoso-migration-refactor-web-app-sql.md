---
title: 將 Contoso 應用程式遷移至 Azure VM 和 SQL Server AlwaysOn 可用性群組，以重構該應用程式 | Microsoft Docs
description: 了解 Contoso 如何將內部部署應用程式遷移至 Azure 容器 Web 應用程式和 Azure SQL Server 資料庫，以重新裝載該應用程式。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005185"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso 移轉：將內部部署應用程式重構至 Azure Web 應用程式和 Azure SQL 資料庫

本文說明 Contoso 如何在 Azure 中重構其 SmartHotel 應用程式。 他們會將應用程式前端 VM 遷移至 Azure Web 應用程式，以及將應用程式資料庫遷移至 Azure SQL 資料庫中。

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
文章 9：將應用程式重構到 Azure Web 應用程式和 Azure SQL 資料庫 | 示範 Contoso 如何將 SmartHotel 應用程式遷移至 Azure Web 應用程式，以及如何將應用程式資料庫遷移至 Azure SQL Server 執行個體 | 本文
[文章 10：將 Linux 應用程式重構到 Azure Web Apps 和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 示範 Contoso 如何將 Linux osTicket 應用程式遷移至多個網站中的 Azure Web Apps (與 GitHub 整合以達到持續傳遞)。 它們會將應用程式資料庫遷移至 Azure MySQL 執行個體。 | 可用
[文章 11：在 VSTS 上重構 TFS](contoso-migration-tfs-vsts.md) | 示範 Contoso 如何將其內部部署 Team Foundation Server (TFS) 部署遷移至 Azure 中的 Visual Studio Team Services (VSTS)，以便進行遷移。 | 可用
[文章 12：在 Azure 容器和 Azure SQL Database 上重新建構應用程式](contoso-migration-rearchitect-container-sql.md) | 示範 Contoso 如何將其 SmartHotel 應用程式遷移和重新建構至 Azure。 它們會將應用程式的 Web 層重新建構為 Windows 容器及 Azure SQL Database 中的應用程式資料庫。 | 可用
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

**需求** | **詳細資料**
--- | --- 
**應用程式** | Azure 中的應用程式應與現在一樣重要。<br/><br/> 應具有目前 VMWare 中的相同效能功能。<br/><br/> 他們不想將心力投注在應用程式。 現在，他們只想要安全地將其移至雲端。<br/><br/> 他們想要停止支援目前執行應用程式的 Windows Server 2008 R2。<br/><br/> 他們想要從 SQL Server 2008 R2 移動到現代的 PaaS 資料庫平台，這樣可減少管理需求。<br/><br/> Contoso 想要盡可能運用 SQL Server 授權及軟體保證中的投資。<br/><br/> 此外，Contoso 想要遷移 Web 層上的單一失敗點。
**限制** | 應用程式由相同 VM 上執行的 ASP.NET 應用程式和 WCF 服務所組成。 他們想要將其分成兩個使用 Azure App Service 的 Web 應用程式。 
**Azure** | 他們想要將應用程式移至 Azure，但不想在讓其在 VM 上執行。 他們想要充分利用 Web 和資料層的 Azure PaaS 服務。 

## <a name="solution-design"></a>解決方案設計

擬定好目標和需求後，Contoso 會設計和檢閱部署解決方案，並識別移轉程序，包括將用於移轉的 Azure 服務。

### <a name="current-app"></a>目前的應用程式

- SmartHotel 內部部署應用程式會分層至兩個 VM (WEBVM 和 SQLVM)。
- 這些 VM 位於 VMware ESXi 主機 **contosohost1.contoso.com** (6.5 版)
- VMware 環境是由 VM 上執行的 vCenter Server 6.5 (**vcenter.contoso.com**) 進行管理。
- Contoso 有內部部署資料中心 (contoso-datacenter) 以及內部部署網域控制站 (**contosodc1**)。
- 移轉完成之後，將會解除委任 Contoso 資料中心的內部部署 VM。


### <a name="proposed-solution"></a>建議的解決方案

- 針對應用程式的資料庫層，Contoso 已透過[這篇文章](https://docs.microsoft.com/azure/sql-database/sql-database-features)來比較 Azure SQL Database 和 SQL Server。 因為下列幾個原因，他們決定使用 Azure SQL Database：
    - Azure SQL Database 是由關聯式資料庫管理的服務。 其在多個服務層級上提供可預測的效能，而且幾乎免管理。 優點包括無須停機的動態延展性、內建智慧最佳化及全球延展性和可用性。
    - 它們可以利用輕量型 Data Migration Assistant (DMA) 來評估內部部署資料庫，並將其遷移到 Azure SQL。
    - 並且可以透過軟體保證使用適用於 SQL Server 的 Azure Hybrid Benefit，以折扣優惠在 SQL Database 上交換現有授權。 這可以提供最多 30% 的折扣。
    - SQL Database 提供多種安全性功能，包括永遠加密、動態資料遮罩和資料列層級的防護/威脅偵測。
- 針對應用程式 Web 層，他們已經決定使用 Azure App Service。 透過此 PaaS 服務，僅須變更一些組態即可部署應用程式。 他們會使用 Visual Studio 來進行變更，並部署兩個 Web 應用程式。 一個用於網站，另一個用於 WCF 服務。
  
### <a name="solution-review"></a>解決方案檢閱
Contoso 會透過比較一份優缺點清單，來評估其建議設計。

**考量** | **詳細資料**
--- | ---
**優點** | 不須變更 SmartHotel 應用程式的程式碼即可遷移至 Azure。<br/><br/> 他們可以使用 SQL Server 和 Windows Server 的 Azure Hybrid Benefit 來妥善運用軟體保證中的投資。<br/><br/> 在移轉之後，他們將不再需要支援 Windows Server 2008 R2。 [深入了解](https://support.microsoft.com/lifecycle)。<br/><br/> 他們可以使用多個執行個體來設定應用程式的 Web 層，使它不再是單一失敗點。<br/><br/> 他們將不再依賴過時的 SQL Server 2008 R2。<br/><br/> SQL Database 可支援 Contoso 的技術需求。 他們使用 Data Migration Assistant 來評估內部部署資料庫，並發現其可相容。<br/><br/> SQL Database 擁有內建容錯功能，無須 Contoso 進行設定。 這可確保資料層不再是單一的容錯移轉點。
**缺點** | Azure App Service 只支援對每個 Web 應用程式部署一個應用程式。 這表示必須佈建兩個 Web 應用程式 (一個用於網站，一個用於 WCF 服務)。<br/><br/> 如果他們使用 Data Migration Assistant 來遷移資料庫，而不是使用 Data Migration Service，Contoso 將不會有可進行大規模移轉的基礎結構。 他們必須建立另一個區域，以確保主要區域無法使用時可進行容錯移轉。

## <a name="proposed-architecture"></a>建議的架構

![案例架構](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>移轉程序

1. Contoso 會佈建 Azure SQL 執行個體，並將 SmartHotel 資料庫遷移至此。
2. 他們佈建及設定 Web 應用程式，並對其部署 SmartHotel 應用程式。

    ![移轉程序](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 服務

**服務** | **說明** | **成本**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 他們會使用 DMA 來評定和偵測可能影響 Azure 中資料庫功能的相容性問題。 DMA 會評定 SQL 來源與目標之間的功能同位，並提出效能和可靠性改善建議。 | 此工具可免費下載。
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | 完全受控的智慧型關聯式雲端資料庫服務。 | 根據功能、輸送量和大小計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure App Service - Web 應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | 使用受完整管理的平台建立強大的雲端應用程式 | 根據大小、位置和使用期間計算費用。 [深入了解](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>必要條件

在執行此案例之前，您 (和 Contoso) 必須準備好下列事項：

**需求** | **詳細資料**
--- | ---
**Azure 訂用帳戶** | 當您在這系列的第一篇文章中執行評量時，您應該已經建立訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。<br/><br/> 如果您使用現有訂用帳戶，而且您不是系統管理員，則需要與系統管理員合作，讓其指派擁有者或參與者權限給您。
**Azure 基礎結構** | [了解](contoso-migration-infrastructure.md) Contoso 如何設定 Azure 基礎結構。


## <a name="scenario-steps"></a>案例步驟

以下是 Contoso 執行移轉的方式：

> [!div class="checklist"]
> * **步驟 1：在 Azure 中佈建 SQL Database 執行個體**：Contoso 會在 Azure 中佈建 SQL 執行個體。 當應用程式網站遷移至 Azure 之後，WCF 服務的 Web 應用程式會指向這個執行個體。
> * **步驟 2：使用 DMA 來遷移資料庫**：他們會使用 Data Migration Assistant 來遷移應用程式資料庫。
> * **步驟 3：佈建 Web 應用程式**：他們會佈建兩個 Web 應用程式。
> * **步驟 4：設定連接字串**：他們會設定連接字串，以便 Web 層的 Web 應用程式、WCF 服務的 Web 應用程式和 SQL 執行個體進行通訊。
> * **步驟 5：發行 Web 應用程式**：這是最後一個步驟，Contoso 會將應用程式發佈至 Azure。


## <a name="step-1-provision-an-azure-sql-database"></a>步驟 1：佈建 Azure SQL Database

1. 他們會選擇在 Azure 中建立 SQL Database。 

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. 指定資料庫名稱以符合在內部部署 VM 上執行的資料庫 (**SmartHotel.Registration**)。 他們會將資料庫置於 ContosoRG 資源群組。 這是用於 Azure 中生產資源的資源群組。

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. 在主要區域中設定新的 SQL Server 執行個體 (**sql-smarthotel-eus2**)。

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. 他們會設定符合其伺服器和資料庫需求的定價層。 並選擇使用 Azure Hybrid Benefit 來節省成本，因為他們已經有 SQL Server 授權。
5. 為控制大小，他們會使用以虛擬核心為基礎的購買方式，並為預期的需求設定限制。

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. 然後，他們會建立資料庫執行個體。

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. 建立執行個體之後，他們會開啟資料庫，並記下在使用 Database Migration Assistance 進行移轉時所需的詳細資料。

    ![佈建 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**需要其他協助？**

- [說明](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)如何佈建 SQL Database。
- [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)虛擬核心的資源限制。


## <a name="step-2-migrate-the-database-with-dma"></a>步驟 2：使用 DMS 遷移資料庫

Contoso 將會使用 DMA 來遷移 SmartHotel 資料庫。

### <a name="install-dma"></a>安裝 DMA

1. 他們從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=53595)將工具下載至內部部署的 SQL Server VM (**SQLVM**)。
2. 他們在 VM 上執行安裝程式 (DownloadMigrationAssistant.msi)。
3. 在 [完成] 頁面上，他們會先選取 [啟動 Microsoft Data Migration Assistant]，再完成精靈。

### <a name="migrate-the-database-with-dma"></a>使用 DMS 遷移資料庫

1. 在 DMA 中建立新專案 (**SmartHotelDB**)，然後選取 [移轉] 
2. 他們將來源伺服器類型選取為 [SQL Server]，並以 **Azure SQL Database** 作為目標。 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. 在移轉詳細資料中，他們新增 **SQLVM** 作為來源伺服器，並新增 **SmartHotel.Registration** 資料庫。 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. 他們收到錯誤，似乎與驗證有關。 不過，在調查之後，問題是出自資料庫名稱中的句號 (.)。 他們決定使用 **SmartHotel-Registration** 名稱佈建新的 SQL 資料庫，以作為解決此問題的方法。 當他們再次執行 DMA 時，他們就能選取 **SmartHotel-Registration**，並繼續執行精靈。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. 在 [選取物件] 中，他們會選取資料庫資料表，並產生 SQL 指令碼。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. 在 DMS 建立指令碼後，按一下 [部署結構描述]。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA 會確認部署成功。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. 現在，他們可以開始進行移轉。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. 移轉完成之後，Contoso 可以驗證資料庫是否正在 Azure SQL 執行個體上執行。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. 他們會在 Azure 入口網站中刪除額外的 SQL 資料庫 **SmartHotel.Registration**。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>步驟 3：佈建 Web 應用程式

完成資料庫遷移之後，Contoso 可以立即佈建這兩個 Web 應用程式。

1. 在入口網站中選取 [Web 應用程式]。

    ![Web 應用程式](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. 他們會提供應用程式名稱 (**SHWEB-EUS2**)、在 Windows 上執行，並將它放在生產資源群組 (**ContosoRG**)。 他們會建立新的應用程式服務和方案。

    ![Web 應用程式](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. 佈建好 Web 應用程式之後，他們會重複此程序以建立 WCF 服務的 Web 應用程式 (**SHWCF-EUS2**)

    ![Web 應用程式](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. 完成之後，他們會瀏覽應用程式的位址，以檢查應用程式是否已成功建立。

## <a name="step-4-configure-connection-strings"></a>步驟 4：設定連接字串

Contoso 必須確定 Web 應用程式和資料庫皆可進行通訊。 若要這樣做，須在程式碼和 Web 應用程式中設定連接字串。

1. 在 WCF 服務的 Web 應用程式中 (**SHWCF-EUS2**) > [設定]  >  [應用程式設定]，新增名為 **DefaultConnection** 的連接字串。
2. 此連接字串取自 **SmartHotel-Registration** 資料庫，並且應以正確的認證進行更新。

    ![連接字串](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Contoso 會使用 Visual Studio 從 **SmartHotel360-internal-booking-apps** 資料夾開啟方案檔。 在 WCF 服務 SmartHotel.Registration.Wcf 的 web.config 檔案中，**connectionStrings** 區段應更新為此連接字串。

     ![連接字串](media/contoso-migration-refactor-web-app-sql/string2.png)

4. 在 SmartHotel.Registration.Web 的 web.config 檔案中，**client** 區段應變更為指向 WCF 服務的新位置。 這是裝載服務端點的 WCF Web 應用程式 URL。

    ![連接字串](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>步驟 5：發行 Web 應用程式

這是最後一個步驟，Contoso 會將 Web 應用程式發佈至 Azure。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 SmartHotel.REgistration.Wcf 專案 > [發佈]。

    ![發佈](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. 開始發行。

    ![發佈](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. 由於他們已建立 Web 應用程式，因此可以選取現有的 App Service。

    ![發佈](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. 在選取 WCF 應用程式之後，Visual Studio 會對其進行部署。

    ![發佈](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. 然後，重複此程序以發行 Web 應用程式 - SmartHotel.Registration.Web。

    ![發佈](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


此時，應用程式已成功遷移至 Azure。

## <a name="clean-up-after-migration"></a>移轉之後進行清除

移轉之後，Contoso 必須完成以下的清除步驟：  

- 從 vCenter 清查中移除內部部署 VM。
- 從本機備份作業中移除 VM。
- 更新內部文件以顯示 SmartHotel 應用程式的新位置。 資料庫會顯示為在 Azure SQL 資料庫中執行，而前端會顯示為在兩個 Web 應用程式中執行。
- 檢閱與已解除委任 VM 互動的任何資源，並更新任何相關的設定或文件，以反映新的組態。


## <a name="review-the-deployment"></a>檢閱部署

對於 Azure 中的移轉後資源，Contoso 必須能執行一切功能並保護其新的基礎結構。

### <a name="security"></a>安全性

- Contoso 必須確保其新的 **SmartHotel-Registration** 資料庫很安全。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特別是，他們應將 Web 應用程式更新為搭配使用 SSL 與憑證。

### <a name="backups"></a>備份

- Contoso 需要檢閱 Azure SQL Database 的備份需求。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 他們應考慮實作容錯移轉群組，為該資料庫提供區域性容錯移轉。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- 考量到復原能力，Contoso 須考慮在主要的美國東部 2 和美國中部區域部署 Web 應用程式。 他們可以設定流量管理員，以確保在發生區域性的運行中斷時可進行容錯移轉。

### <a name="licensing-and-cost-optimization"></a>授權和成本最佳化

- 部署好所有資源之後，Contoso 應根據[基礎結構規劃](contoso-migration-infrastructure.md#set-up-tagging)來指派 Azure 標記。
- 所有授權費用都會併入 Contoso 使用的 PaaS 服務中。 這將會從 EA 中扣除。
1. Contoso 會啟用 Microsoft 子公司 Cloudyn 授權的 Azure 成本管理。 它是一種多雲端成本管理解決方案，可協助您利用和管理 Azure 和其他雲端資源。  [深入了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 

## <a name="conclusion"></a>結論

在本文中，Contoso 已透過將應用程式前端 VM 遷移至兩個 Web 應用程式，來重構 SmartHotel 應用程式。 他們已將應用程式資料庫遷移至 Azure SQL 資料庫。






