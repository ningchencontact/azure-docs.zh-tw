---
title: 使用 SQL Server 和 Azure Site Recovery 來設定 SQL Server 的災害復原 | Microsoft Docs
description: 本文說明如何使用 SQL Server 和 Azure Site Recovery 來設定 SQL Server 的災害復原。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491782"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>設定 SQL Server 的災害復原

本文說明如何使用一套 SQL Server 商務持續性和災害復原 (BCDR) 技術，加上 [Azure Site Recovery](site-recovery-overview.md)，以保護應用程式的 SQL Server 後端。

在開始之前，請確定您了解 SQL Server 災害復原功能，包括容錯移轉叢集、 Always On 可用性群組、 資料庫鏡像、 記錄傳送、 作用中地理複寫和自動容錯移轉群組。

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>使用 Site Recovery 的 SQL Server BCDR 技術整合的災害復原建議

復原 SQL server BCDR 技術的選擇應該根據您的 RTO 和 RPO 需求，依照下表。 該選擇之後，Site Recovery 可以與容錯移轉作業，以協調將整個應用程式的復原技術的整合。

**部署類型** | **BCDR 技術** | **預期的 RTO sql** | **預期的 RPO sql** |
--- | --- | --- | ---
在 Azure IaaS VM 上或在內部部署的 SQL Server| **[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | 相當於設定為主要的次要複本所花費的時間 | 複寫是非同步的次要複本，因此不會遺失一些資料。
在 Azure IaaS VM 上或在內部部署的 SQL Server| **[容錯移轉叢集 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | 相當於節點之間的容錯移轉所花費的時間 | 它會使用共用存放裝置，因此相同的儲存體執行個體檢視可用於容錯移轉。
在 Azure IaaS VM 上或在內部部署的 SQL Server| **[資料庫鏡像 （高效能模式）](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | 相當於強制服務，鏡像伺服器做為暖待命伺服器所花費的時間。 | 複寫不是同步進行。 鏡像資料庫可能會落後主體資料庫。 間距通常很小，howvever，就會變大，如果主體或鏡像伺服器的系統負載過重。<br></br>記錄傳送可以補充資料庫鏡像，而且是理想的替代方式，以及非同步資料庫鏡像
做為 Azure 上的 PaaS SQL<br></br>（彈性集區，SQL database 伺服器） | **作用中異地複寫** | 30 秒一次觸發<br></br>容錯移轉至其中一個次要資料庫啟動時，所有其他次要複本會自動連結至新的主要複本。 | RPO 為 5 秒<br></br>作用中異地複寫會利用 Alwayson 技術的 SQL Server，以非同步方式將主要資料庫上認可的交易複寫至次要資料庫使用快照集隔離。 <br></br>次要資料保證永遠不會有部分的交易。
做為 PaaS 以在 Azure 上的主動式異地複寫設定的 SQL<br></br>（SQL Database 受控執行個體，彈性集區，SQL database 伺服器） | **自動容錯移轉群組** | RTO 為 1 小時 | RPO 為 5 秒<br></br>自動容錯移轉群組在主動式異地複寫之上提供群組語意，但使用相同的非同步複寫機制。
在 Azure IaaS VM 上或在內部部署的 SQL Server| **使用 Azure Site Recovery 的複寫** | 通常少於 15 分鐘。 [深入了解](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)若要深入了解 Azure Site Recovery 所提供的 RTO SLA。 | 1 小時內的應用程式一致性和損毀一致性的 5 分鐘。 

> [!NOTE]
> 一些重要的考量 SQL 工作負載，使用 Azure Site Recovery 來保護時：
> * Azure Site Recovery 是應用程式無從驗證，因此，任何版本的 SQL server 部署在支援的作業系統上都可以使用 Azure Site Recovery 會保護。 [深入了解](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * 您可以使用 Site Recovery 進行 Azure、 HYPER-V、 VMware 或實體基礎結構在任何部署。 請遵循[指引](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2)上如何使用 Azure Site Recovery 的 SQL Server 叢集保護的文件的結尾。
> * 請確定資料變更速率 （每秒寫入位元組） 的機器上觀察不到位於[Site Recovery 限制](vmware-physical-azure-support-matrix.md#churn-limits)。 對於 windows 機器，您可以在 [效能] 索引標籤上 [工作管理員] 中檢視這。 觀察寫入每個磁碟的速度。
> * Azure Site Recovery 支援複寫容錯移轉叢集執行個體上儲存空間直接存取。 [深入了解](azure-to-azure-how-to-enable-replication-s2d-vms.md)。
 

## <a name="disaster-recovery-of-application"></a>應用程式的災害復原

**Azure Site Recovery 會協調測試容錯移轉和容錯移轉整個應用程式的復原計劃的協助。** 

有一些必要條件以確保根據您需要完全自訂的復原計劃。 任何 SQL Server 部署通常需要 Active Directory。 它也會需要您的應用程式層的連線。

### <a name="step-1-set-up-active-directory"></a>步驟 1：設定 Active Directory

在次要復原網站上設定 Active Directory，讓 SQL Server 正常運作。

* **小型企業** — 如果只有少數應用程式，且內部部署網站只有一個網域控制站，當您想要容錯移轉整個網站時，建議您使用 Site Recovery 複寫，將網域控制站複寫至次要資料中心或 Azure。
* **中大型企業** — 如果您有大量的應用程式和一個 Active Directory 樹系，而您想要依應用程式或工作負載容錯移轉，建議您在次要資料中心或 Azure 中設定其他網域控制站。 如果您使用 Always On 可用性群組復原至遠端網站，建議您在次要網站或 Azure 上設定其他不同的網域控制站，供已復原的 SQL Server 執行個體使用。

本文中的指示假設在次要位置中有網域控制站。 [閱讀更多](site-recovery-active-directory.md) 有關使用 Site Recovery 保護 Active Directory。

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>步驟 2：確保與其他應用程式層和 web 層的連線

確定之後，資料庫層啟動並執行目標 Azure 區域中，您會有與應用程式和 web 層連線。 應該事先採取必要步驟，才能驗證與測試容錯移轉的連線。

了解如何設計應用程式的連線有幾個範例的考量：
* [設計雲端災害復原應用程式](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [彈性集區災害復原策略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>步驟 3：與 Always On 整合，作用中地理複寫 」 或 「 應用程式容錯移轉的自動容錯移轉群組

Always On 的 BCDR 技術，作用中地理複寫和自動容錯移轉群組會有在目標 Azure 區域中執行的 SQL server 的次要複本。 因此，您的應用程式容錯移轉的第一個步驟是讓此複本成為主要 （假設您已經有次要網域控制站）。 此步驟可能不需要，如果您選擇進行自動容錯移轉。 只有資料庫容錯移轉完成之後，您應該容錯移轉您的 web 或應用程式層。

> [!NOTE] 
> 如果您有保護 SQL 機器使用 Azure Site Recovery，您只需要建立復原群組，這些機器，並將其容錯移轉復原方案中。

[建立復原計劃](site-recovery-create-recovery-plans.md)與應用程式和 web 層虛擬機器。 請遵循下列步驟來新增容錯移轉的資料庫層：

1. 將指令碼匯入您的 Azure 自動化帳戶。 這包括用於將 [Resource Manager 虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)和[傳統虛擬機器](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)中的 SQL 可用性群組容錯移轉的指令碼。

    [![部署至 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. 新增 ASR-SQL-FailoverAG 作為復原方案第一個群組的前置動作。

1. 依照指令碼中的指示建立自動化變數，以提供可用性群組的名稱。

### <a name="step-4-conduct-a-test-failover"></a>步驟 4：進行測試容錯移轉

某些 BCDR 技術，例如 SQL Alwayson 原生不支援測試容錯移轉。 因此，我們建議下列方式**與這類技術整合時，才**:

1. 在 Azure 中裝載可用性群組複本的虛擬機器上，設定 [Azure 備份](../backup/backup-azure-arm-vms.md)。

1. 觸發復原計劃的測試容錯移轉之前，從上一步所使用的備份來復原虛擬機器。

    ![從 Azure 備份還原](./media/site-recovery-sql/restore-from-backup.png)

1. 在從備份還原的虛擬機器中[強制仲裁](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)。

1. 將接聽程式的 IP 更新為測試容錯移轉網路中可用的 IP 的。

    ![更新接聽程式 IP](./media/site-recovery-sql/update-listener-ip.png)

1. 使接聽程式上線。

    ![使接聽程式上線](./media/site-recovery-sql/bring-listener-online.png)

1. 使用一個 IP 和 SQL 虛擬機器建立負載平衡器；此 IP 是對應至每個可用性群組接聽程式的前端 IP 集區下的 IP，而是虛擬機器是在後端集區中新增的 SQL 虛擬機器。

     ![建立負載平衡器 - 前端 IP 集區](./media/site-recovery-sql/create-load-balancer1.png)

    ![建立負載平衡器 - 後端集區](./media/site-recovery-sql/create-load-balancer2.png)

1. 新增您的應用程式層，web 層接著後續復原群組中此復原計劃中容錯移轉。 
1. 執行測試容錯移轉復原方案，以測試應用程式的端對端容錯移轉。

## <a name="steps-to-do-a-failover"></a>進行容錯移轉的步驟

一旦您已在步驟 3 中的復原方案中加入指令碼，並驗證它藉由在步驟 4 中的特定方法的測試容錯移轉，您可以執行步驟 3 中建立復原計劃的容錯移轉。

請注意，應用程式和 web 層的容錯移轉步驟應該是相同的測試容錯移轉和容錯移轉復原計劃。

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>如何保護 SQL Server 叢集 (standard edition/SQL Server 2008 R2)

對於執行 SQL Server Standard Edition 或 SQL Server 2008 R2 的叢集，建議您使用 Site Recovery 複寫保護 SQL Server。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 至 Azure 和內部部署至 Azure

Site Recovery 不提供來賓叢集支援，將複寫至 Azure 的區域時。 SQL Server 也不會為 Standard Edition 提供低成本的災害復原解決方案。 在此案例中，我們建議您將 SQL Server 叢集保護至獨立式 SQL Server 的主要位置，並在次要資料庫中復原。

1. 在主要 Azure 區域，或在內部部署站台，請設定額外的獨立 SQL Server 執行個體。
1. 設定此執行個體做為您要保護之資料庫的鏡像。 在高安全性模式下設定鏡像。
1. 主要站台上設定 Site Recovery ([Azure](azure-to-azure-tutorial-enable-replication.md)， [HYPER-V](site-recovery-hyper-v-site-to-azure.md)或是[VMware Vm/實體伺服器)](site-recovery-vmware-to-azure-classic.md)。
1. 使用 Site Recovery 複寫來複寫至次要網站的 新的 SQL Server 執行個體。 因為它是高安全性鏡像副本，它會與主要叢集同步處理，但它會複寫使用 Site Recovery 複寫。


![標準叢集](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>容錯回復考量

就 SQL Server 標準叢集而言，若要在未規劃的容錯移轉之後進行容錯回復，必須從鏡像執行個體進行 SQL 備份並還原至原始叢集，並重新建立鏡像。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>沒有 SQL 取得的授權方式時使用 Azure Site Recovery 保護？
軟體保證 – 災害復原權益涵蓋適用於 SQL Server 的 Azure Site Recovery 複寫，並適合所有 Azure Site Recovery 案例 (內部部署至 Azure 的災害復原或跨地區 Azure IaaS 的災害復原)。 [閱讀更多資訊](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>將 Azure Site Recovery 支援我的 SQL 版本？
Azure Site Recovery 是應用程式無從驗證。 因此，Azure Site Recovery 可以保護任何版本的 SQL server 部署在支援的作業系統上。 [深入了解](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>後續步驟
* [深入了解](site-recovery-components.md) Site Recovery 架構。
* 在 Azure 中的 SQL server，深入了解[高可用性解決方案](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)次要 Azure 區域中復原的。
* 針對在 Azure 中的 SQL Database，深入了解[業務續航力](../sql-database/sql-database-business-continuity.md)並[高可用性](../sql-database/sql-database-high-availability.md)次要 Azure 區域中的復原選項。
* SQL server 機器在內部[深入](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)需復原 Azure 虛擬機器中的高可用性選項。
