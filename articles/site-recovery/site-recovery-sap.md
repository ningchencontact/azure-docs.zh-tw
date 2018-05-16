---
title: 使用 Azure Site Recovery 保護多層式 SAP NetWeaver 應用程式部署 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 保護 SAP NetWeaver 應用程式部署。
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: e2107177663163259d1f731717c4910bc986fc1f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署

大部分的大型與中型 SAP 部署都有某種形式的災害復原解決方案。 隨著移至應用程式 (如 SAP) 的核心商務程序愈來愈多，強固且可測試的災害復原解決方案愈形重要。 Azure Site Recovery 已經過測試並與 SAP 應用程式整合。 Site Recovery 超越大部分內部部署災害復原解決方案的功能，且具有比競爭解決方案低的總體擁有成本 (TCO)，。

使用 Site Recovery，您可以：
* 藉由將元件複寫到 Azure，**啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能**。
* 藉由將元件複寫到另一個 Azure 資料中心，**啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能**。
* 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來**簡化雲端移轉**。
* 藉由建立隨選生產複本來測試 SAP 應用程式，**簡化 SAP 專案升級、測試和原型設計**。

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 保護 SAP NetWeaver 應用程式部署。 本文涵蓋在 Azure 上保護三層式 SAP NetWeaver 部署的最佳做法，也就是使用 Site Recovery 來複寫至另一個 Azure 資料中心。 其說明支援的案例和組態，以及如何執行測試容錯移轉 (災害復原演練) 和實際的容錯移轉。

## <a name="prerequisites"></a>先決條件
在開始之前，請確定您了解如何執行下列工作：

* [將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [設計復原網路](site-recovery-azure-to-azure-networking-guidance.md)
* [執行測試容錯移轉至 Azure](azure-to-azure-walkthrough-test-failover.md)
* [執行容錯移轉到 Azure](site-recovery-failover.md)
* [複寫網域控制站](site-recovery-active-directory.md)
* [複寫 SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支援的案例
您可以使用 Site Recovery，在下列案例中實作災害復原解決方案：
* 在一個 Azure 資料中心執行並複寫至另一個 Azure 資料中心 (Azure 對 Azure 災害復原) 的 SAP 系統。 如需詳細資訊，請參閱 [Azure 對 Azure 複寫架構](https://aka.ms/asr-a2a-architecture)。
* 在內部部署 VMware (或實體) 伺服器上執行並複寫到 Azure 資料中心內災害復原站台 (VMware 對 Azure 災害復原) 的 SAP 系統。 此案例需要一些額外的元件。 如需詳細資訊，請參閱 [VMware 對 Azure 複寫架構](https://aka.ms/asr-v2a-architecture)。
* 在內部部署 Hyper-V 上執行並複寫到 Azure 資料中心內災害復原站台 (Hyper-V 對 Azure 災害復原) 的 SAP 系統。 此案例需要一些額外的元件。 如需詳細資訊，請參閱 [Hyper-V 對 Azure 複寫架構](https://aka.ms/asr-h2a-architecture)。

在本文中，我們會使用 Azure 對 Azure 災害復原案例來示範 Site Recovery 的 SAP 災害復原功能。 由於 Site Recovery 複寫不限於特定應用程式，因此所述程序應也可以套用至其他案例。

### <a name="required-foundation-services"></a>必要的基礎服務
我們將在本文討論的案例中，部署下列基礎服務：
* Azure ExpressRoute 或 Azure VPN 閘道
* 至少一個在 Azure 中執行的 Active Directory 網域控制站和 DNS 伺服器

我們建議您先建立此基礎結構，再部署 Site Recovery。

## <a name="typical-sap-application-deployment"></a>典型 SAP 應用程式部署
大型 SAP 客戶通常部署 6 到 20 個個別的 SAP 應用程式。 這些應用程式大部分是以 SAP NetWeaver ABAP 或 Java 引擎為基礎。 許多特定且較小型的非 NetWeaver SAP 獨立引擎 (通常是一些非 SAP 應用程式) 可支援這些核心 NetWeaver 應用程式。  

務必清查所有您環境中執行的 SAP 應用程式。 然後決定部署模式 (兩層或三層式)、版本、修補程式、大小、變換率，以及磁碟持續性需求。

![典型 SAP 部署模式的圖表](./media/site-recovery-sap/sap-typical-deployment.png)

使用原生 DBMS 工具 (例如 SQL Server AlwaysOn、Oracle Data Guard 或 SAP HANA 系統複寫) 來保護 SAP 資料庫的持續層。 如同 SAP 資料庫層級，用戶端層不會受到 Site Recovery 的保護。 請務必考量到會影響此圖層的因素。 這些因素包括 DNS 傳播延遲、安全性及災害復原資料中心的遠端存取。

Site Recovery 是應用程式層的建議解決方案 (包括 SAP SCS 和 ASCS)。 其他應用程式 (例如非 NetWeaver SAP 應用程式和非 SAP 應用程式) 是形成整體 SAP 部署環境的一部分。 您應使用 Site Recovery 保護這些應用程式。

## <a name="replicate-virtual-machines"></a>複寫虛擬機器
若要開始將所有 SAP 應用程式虛擬機器都複寫至 Azure 災害復原資料中心，請遵循[將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指引。

如果您使用靜態 IP 位址，您可以指定您想要虛擬機器使用的 IP 位址。 若要設定 IP 位址，請移至 [計算和網路設定] > [網路介面卡]。

![示範如何在 Site Recovery 網路介面卡窗格設定私人 IP 位址的螢幕擷取畫面](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>建立復原計畫
復原方案支援在容錯移轉期間對多層式應用程式中的各層進行排序。 排序有助於維持應用程式的一致性。 當您為多層式 Web 應用程式建立復原計畫時，請完成[使用 Site Recovery 建立復原計畫](site-recovery-create-recovery-plans.md)中所述的步驟。

### <a name="add-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
為了讓應用程式可正常運作，您可能需要在容錯移轉後或測試容錯移轉期間，於 Azure 虛擬機器上執行某些作業。 您可以自動化某些容錯移轉後的作業。 例如，您可以透過將對應的指令碼新增至復原計畫，以更新 DNS 項目和變更繫結與連線。

### <a name="dns-update"></a>DNS 更新
如果是已設定動態 DNS 更新的 DNS，則虛擬機器通常會在啟動之後使用新的 IP 位址更新 DNS。 如果您想要新增一個明確的步驟來以虛擬機器的新 IP 位址更新 DNS，則請新增[指令碼來更新 DNS 中的 IP 位址](https://aka.ms/asr-dns-update)，作為復原計畫群組上的容錯移轉後置動作。  

## <a name="example-azure-to-azure-deployment"></a>Azure 對 Azure 部署範例
下圖顯示 Site Recovery 的 Azure 對 Azure 災害復原案例：

![Azure 對 Azure 複寫案例的圖表](./media/site-recovery-sap/sap-replication-scenario.png)

* 主要資料中心位於新加坡 (Azure 東南亞)。 災害復原資料中心位於香港 (Azure 東亞)。 在此案例中，由兩部在新加坡以同步模式執行 SQL Server AlwaysOn 的 VM，來提供本機高可用性。
* 檔案共用 SAP ASCS 可針對 SAP 單一失敗點提供高可用性。 檔案共用 ASCS 不需要叢集共用的磁碟。 不需要 SIOS 之類的應用程式。
* DBMS 層的災害復原保護是利用非同步複寫達成。
* 此案例會示範「對稱的災害復原」。 此字詞是說明災害復原解決方案就是生產環境中的複本。 災害復原 SQL Server 解決方案具有本機高可用性。 對稱的災害復原對資料庫層級而言不是必要的。 許多客戶會利用雲端部署的彈性，來快速建置災害復原事件之後的本機高可用性端點。
* 本圖說明 Site Recovery 所複寫的 SAP NetWeaver ASCS 和應用程式伺服器層。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

1.  在 Azure 入口網站中，選取您的復原服務保存庫。
2.  選取為 SAP 應用程式建立的復原計畫。
3.  選取 [測試容錯移轉]。
4.  若要啟動測試容錯移轉程序，請選取復原點和 Azure 虛擬網路。
5.  當次要環境啟動時，即可執行您驗證。
6.  完成驗證後，若要清除此容錯移轉環境，請選取 [清除測試容錯移轉]。

如需詳細資訊，請參閱[在 Site Recovery 中測試容錯移轉至 Azure](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>執行容錯移轉

1.  在 Azure 入口網站中，選取您的復原服務保存庫。
2.  選取為 SAP 應用程式建立的復原計畫。
3.  選取 [容錯移轉]。
4.  若要啟動容錯移轉程序，請選取復原點。

如需詳細資訊，請參閱[在 Site Recovery 中容錯移轉](site-recovery-failover.md)。

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何使用 Site Recovery 為 SAP NetWeaver 部署建置災害復原解決方案，請參閱可下載的白皮書＜[SAP NetWeaver：使用 Azure Site Recovery 建置災害復原方案](http://aka.ms/asr-sap)＞。 本白皮書會討論各種 SAP 架構的建議，列出支援 Azure 上的 SAP 的應用程式和 VM 類型，並說明災害復原解決方案的測試計劃選項。
* 深入了解如何使用 Site Recovery [複寫其他工作負載](site-recovery-workload.md)。
