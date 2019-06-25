---
title: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量 | Microsoft Docs
description: 適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 975289f338e638ed0209d4f6cf2a163ced996e42
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202950"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

本指南是如何實作及 Microsoft Azure 上部署 SAP 軟體的相關文件的一部分。 閱讀本指南之前，請先閱讀[規劃和實作指南][planning-guide]。 本文件涵蓋使用 Azure 基礎結構即服務 (IaaS) 功能的 Microsoft Azure 虛擬機器 (Vm) 上的 SAP 相關 DBMS 系統的一般部署層面。

本文補充 SAP 安裝文件和 SAP 附註 」 進行安裝和部署 SAP 軟體的主要資源上指定的平台。

本文件說明在 Azure VM 中執行 SAP 相關 DBMS 系統的考量。 有數個對於本章中特定 DBMS 系統的參考資料。 相反地，特定 DBMS 系統會在本文中，處理這份文件之後。

## <a name="definitions"></a>定義
在本文件中，使用這些詞彙：

* **IaaS**:基礎結構即服務。
* **PaaS**:平台即服務。
* **SaaS**:軟體即服務。
* **SAP 元件**:例如 ERP Central Component (ECC)、 Business Warehouse (BW)、 Solution Manager 或企業版入口網站 (EP) 個別 SAP 應用程式。 可以根據 SAP 元件，傳統的 ABAP 或 Java 技術或非 NetWeaver 架構的應用程式，例如商務物件。
* **SAP 環境**:一或多個 SAP 元件以邏輯方式分組執行開發、 品質保證、 訓練、 災害復原或生產等商務功能。
* **SAP 環境**:此詞彙是指客戶 IP 環境中的整個 SAP 資產。 SAP 版圖包括所有生產和非生產環境。
* **SAP 系統**:組合的 DBMS 層與應用程式層，例如 SAP ERP 開發系統、 SAP Business Warehouse 測試系統或 SAP CRM 生產系統。 在 Azure 部署中，不支援分割這兩個層在內部部署環境與 Azure 之間。 如此一來，SAP 系統是在內部部署或是其在 Azure 中部署。 您可以部署在 Azure 或內部部署的 SAP 架構的不同系統。 比方說，您可以部署 SAP CRM 開發和測試系統，在 Azure 中但內部部署部署 SAP CRM 生產系統。
* **跨單位**:說明的案例中，Vm 部署到 Azure 訂用帳戶具有站對站、 多站台，或 Azure ExpressRoute 連線能力之間的內部部署資料中心和 Azure。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 

    連線的原因是為了將內部部署網域、內部部署 Active Directory 和內部部署 DNS 擴充到 Azure。 內部部署的架構會擴充到訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 在內部部署網域的網域使用者可以存取的伺服器，並在這些 Vm，例如 DBMS 服務上執行服務。 您可以在內部部署的 VM 與 Azure 中部署的 VM 之間進行通訊與名稱解析。 此案例中是用來部署 Azure 上的 SAP 資產最常見的案例。 如需詳細資訊，請參閱[規劃與設計 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> 跨單位部署 SAP 系統，其中執行 SAP 系統的 Azure 虛擬機器就會在內部部署網域的成員，並支援生產環境 SAP 系統。 跨單位組態可將部分或完整 SAP 環境部署到 Azure。 即使在 Azure 中執行完整 SAP 環境需要這些 Vm 是內部部署網域的一部分和 Active Directory LDAP。 
>
> 在舊版的文件中，提到混合式 IT 案例。 詞彙*混合式*已進行 root 破解是內部部署與 Azure 之間的跨單位連線。 在此情況下，混合式也表示 Azure 中的 Vm 會在內部部署 Active Directory 的一部分。
>
>

有些 Microsoft 文件描述跨單位案例稍有不同，特別是針對 DBMS 高可用性組態。 在 SAP 相關的文件，跨單位案例全然取決於為站對站或私人[ExpressRoute](https://azure.microsoft.com/services/expressroute/)連線和內部部署與 Azure 的 SAP 架構。

## <a name="resources"></a>資源
有可用的其他文件上的 SAP 工作負載在 Azure 上。 開始使用[在 Azure 上的 SAP 工作負載：開始使用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)，然後選擇您感興趣的區域。

下列 SAP 附註被關於在 Azure 上的 SAP 對於本文件涵蓋的區域。

| 附註編號 | 標題 |
| --- | --- |
| [1928533] |在 Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] |Microsoft Azure 上的 SAP：支援的必要條件 |
| [1999351] |對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2178632] |Microsoft Azure 上適用於 SAP 的主要監視度量 |
| [1409604] |Windows 上的虛擬化：增強型監視 |
| [2191498] |Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 應用程式︰支援的產品和版本 |
| [2233094] |DB6：使用 IBM DB2 for Linux、 UNIX 和 Windows Azure 上的 SAP 應用程式：其他資訊 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 授權問題 |
| [1984787] |SUSE LINUX Enterprise Server 12：安裝注意事項 |
| [2002167] |Red Hat Enterprise Linux 7.x：安裝與升級 |
| [2069760] |Oracle Linux 7.x SAP 安裝和升級 |
| [1597355] |適用於 Linux 的交換空間建議 |
| [2171857] |Oracle Database 12c:在 Linux 上的檔案系統支援 |
| [1114181] |Oracle Database 11g:在 Linux 上的檔案系統支援 |


如需所有 SAP 附註適用於 Linux 的資訊，請參閱[SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

您必須使用 Microsoft Azure 架構以及如何部署和操作 Microsoft Azure 虛擬機器的知識。 如需詳細資訊，請參閱 < [Azure 文件](https://docs.microsoft.com/azure/)。

一般情況下，Windows、 Linux 和 DBMS 的安裝和設定是本質上與任何虛擬機器或裸機機器安裝在內部部署環境相同。 有一些架構和系統管理實作決策都不同，當您使用 Azure IaaS。 本文件說明的特定架構和系統管理差異，當您使用 Azure IaaS 的準備。


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 部署 VM 的儲存結構
若要遵循本章中，閱讀和了解所顯示的資訊[本章][ deployment-guide-3]的[部署指南][deployment-guide]。 您需要了解，並了解不同，VM 系列和標準和進階儲存體閱讀本章之前之間的差異。 

若要深入了解 Azure 儲存體，適用於 Azure Vm，請參閱：

- [簡介 Azure Windows Vm 的受控磁碟](../../windows/managed-disks-overview.md)。
- [簡介 Azure Linux Vm 的受控磁碟](../../linux/managed-disks-overview.md)。

在基本組態中，我們通常建議部署結構，其中作業系統、 DBMS 和最終的 SAP 二進位檔會與資料庫檔案分隔開來。 我們建議在 Azure 虛擬機器中執行的 SAP 系統的基底 VHD 或安裝作業系統、 資料庫管理系統可執行檔和 SAP 可執行檔的磁碟。 

DBMS 資料和記錄檔會儲存在標準儲存體 」 還是 「 進階儲存體。 它們是儲存在不同的磁碟，並以邏輯磁碟形式連接到原始的 Azure 作業系統映像 VM。 針對 Linux 部署，會記錄不同的建議，特別是針對 SAP HANA。

當您計劃您的磁碟配置時，尋找這些項目之間的最佳平衡︰

* 資料檔案數量。
* 包含檔案的磁碟數目。
* 單一磁碟的 IOPS 配額。
* 每個磁碟的資料輸送量。
* 每個 VM 大小可能的額外資料磁碟數目。
* VM 可提供的整體儲存體輸送量。
* 不同的 Azure 儲存體類型可以提供的延遲。
* VM Sla。

Azure 會強制執行每個資料磁碟的 IOPS 配額。 這些配額是在標準儲存體和進階儲存體上裝載的磁碟不同。 I/O 延遲在兩個儲存體類型之間也有所不同。 進階儲存體提供較佳的 I/O 延遲。 

不同 VM 類型都有限的數目的可附加資料磁碟。 另一個限制是只有特定 VM 類型，可以使用進階儲存體。 一般而言，您決定使用根據 CPU 和記憶體需求的特定 VM 類型。 您也可以考慮的 IOPS、 延遲及磁碟輸送量需求，通常利用磁碟數目或進階儲存體磁碟類型來調整。 受到每個磁碟的 IOPS 和輸送量的數目可能會決定磁碟大小，特別是使用進階儲存體。

> [!NOTE]
> 針對 DBMS 部署，我們建議使用進階儲存體的任何資料，交易記錄，或取消復原檔案。 無論您是否想要部署生產環境或非生產系統。

> [!NOTE]
> 若要受益於 Azure 的唯一[的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，所有連接的磁碟必須是進階儲存體類型，其中包含基底 VHD。

> [!NOTE]
> 裝載主要資料庫檔案，例如資料和記錄檔位於 Azure 資料中心的相鄰的共置之第三方資料中心的儲存硬體上的 SAP 資料庫的不支援。 針對 SAP 工作負載，以原生的 Azure 服務的唯一儲存體支援的 SAP 資料庫資料和交易記錄檔。

IOPS、 延遲和輸送量需求所定義的資料庫檔案和記錄檔和取消復原檔案和您所使用的 Azure 儲存體的型別位置。 若要有足夠的 IOPS，您可能被迫使用多個磁碟，或使用較大的進階儲存體磁碟。 如果您使用多個磁碟，請在包含資料檔或記錄檔和取消復原檔案的磁碟建置軟體等量磁碟區。 在這種情況下，IOPS 和磁碟輸送量 Sla，基礎進階儲存體磁碟或標準儲存體磁碟的最大可達到 IOPS 是累加產生的等量磁碟區組。

已經陳述的是，如果您的 IOPS 需求超過單一 VHD 可以提供，平衡所需的資料庫檔案，跨多個 Vhd 的 IOPS 數目。 將 IOPS 負載分散到磁碟的最簡單方式是透過不同的磁碟建置軟體等量磁碟區。 然後放置從中切割出軟體等量 Lun 上的 SAP DBMS 的資料檔案的數目。 在等量磁碟區的磁碟數目取決 IOPs 需求、 磁碟輸送量需求，以及磁碟區需求。


---
> ![Windows][Logo_Windows] Windows
>
> 我們建議您跨多個 Azure Vhd 建立等量磁碟區，使用 Windows 儲存空間。 至少使用 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 若要建置軟體 RAID，在 Linux 上支援只有 MDADM 和邏輯磁碟區管理員 (LVM)。 如需詳細資訊，請參閱
>
> - [Linux 上設定軟體 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)使用 MDADM
> - 使用 LVM [在 Azure 中的 Linux VM 上設定 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> Azure 儲存體會保留三個 Vhd 映像，因為它沒有意義時您等量磁碟區設定備援。 您只需要設定等量，以便將 I/o 分散到不同的 Vhd。
>

### <a name="managed-or-nonmanaged-disks"></a>受控或非受控磁碟
Azure 儲存體帳戶是系統管理的建構，也限制的緣由。 限制是根據標準儲存體帳戶 」 和 「 進階儲存體帳戶而不同。 如需功能和限制的資訊，請參閱[Azure 儲存體延展性和效能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

標準儲存體，請記得在每個儲存體帳戶的 IOPS 上有所限制。 請參閱包含的資料列**總要求率**一文中[Azure 儲存體延展性和效能目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。 另外還有每個 Azure 訂用帳戶的儲存體帳戶數目初始限制。 以避免達到這些儲存體帳戶的限制 餘額大的 SAP 架構，跨不同的儲存體帳戶的 Vhd。 當您的意思了幾個數百個具有超過一千個 Vhd 的虛擬機器時，這會是冗長乏味的工作。

不建議使用標準儲存體與 SAP 工作負載搭配使用的 DBMS 部署，因為僅限於此簡短的參考和標準儲存體的建議[文章](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

若要避免計劃和部署到不同的 Azure 儲存體帳戶的 Vhd 的系統管理工作，Microsoft 導入了[Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)2017。 受控的磁碟可供標準儲存體和進階儲存體。 相較於非受控磁碟的受控磁碟的主要優點如下：

- 受控磁碟，Azure 不同的 Vhd 會分配給不同的儲存體帳戶會自動在部署階段。 如此一來，儲存體帳戶限制的資料磁碟區，I/O 輸送量和 IOPS 不叫用。
- 使用受控的磁碟，Azure 儲存體接受的 Azure 可用性設定組的概念。 如果 VM 是 Azure 可用性設定組的一部分，則基底 VHD 和 VM 的連接的磁碟已部署至不同的容錯和更新網域中。


> [!IMPORTANT]
> 提供 Azure 受控磁碟的優點，我們建議您使用 Azure 受控磁碟的 DBMS 部署的 SAP 部署一般情況下。
>

若要從 unmanaged 轉換成受控磁碟，請參閱：

- [Windows 虛擬機器從非受控磁碟轉換為受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [將 Linux 虛擬機器從非受控磁碟轉換為受控磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和資料磁碟的快取
當您將磁碟掛接到 Vm 時，您可以選擇是否要快取間的 VM 和位於 Azure 儲存體磁碟的 I/O 流量。 標準和進階儲存體使用兩種不同技術，這種類型的快取。

下列建議假設標準 DBMS 的 I/O 特性：

- 就大部分的讀取工作負載對資料庫資料檔案。 這些讀取是很重要的 DBMS 系統的效能。
- 針對資料檔案的寫入會在檢查點或常數的資料流為基礎的高載。 平均一天，有較少寫入，而不讀取。 相對於容量從資料檔案的讀取，這些寫入為非同步，並不要阻擋任何使用者交易。
- 幾乎不會從交易記錄或重做檔案進行任何讀取。 例外狀況是大型 I/o，當您執行交易記錄備份。
- 主要負載與針對交易或重做記錄檔是寫入。 根據工作負載的本質，您可以有 I/o 為 4 KB，或在其他情況下，1 MB 或更多的 I/O 大小越小。
- 所有 writes 必須都保存在磁碟中可靠的方式。

標準儲存體，可能的快取類型包括：

* None
* 閱讀
* 讀取/寫入

若要取得一致且具決定性的效能，將快取設定標準儲存體上的所有磁碟包含 DBMS 相關資料檔、 記錄和取消復原檔案和資料表空間**NONE**。 基底 VHD 的快取可以保留預設值。

針對進階儲存體中，有下列的快取選項：

* None
* 閱讀
* 讀取/寫入
* 無 + 寫入加速器只適用於 Azure M 系列 Vm
* 讀取 + 寫入加速器只適用於 Azure M 系列 Vm

針對進階儲存體，我們建議您使用**資料檔案讀取快取**將 sap 資料庫，然後選擇**快取的記錄檔磁碟**。

M 系列部署中，我們建議您為您的 DBMS 部署使用 Azure 寫入加速器。 如需詳細資訊、 限制和部署的 Azure 寫入加速器，請參閱[啟用寫入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>非持續性的 azure 磁碟
部署 VM 之後，azure Vm 會提供非持續性磁碟。 在 VM 重新開機的情況下被抹除這些磁碟機上的所有內容。它會假設資料檔案和記錄檔和重做資料庫檔應該在任何情況下位於這些非保存的磁碟機上。 可能會有例外狀況，對於某些資料庫，這些非保存的磁碟機可能是適用於 tempdb 和暫存資料表空間。 避免使用適用於 A 系列 Vm 的這些磁碟機，因為該 VM 系列的輸送量會受限於這些非保存的磁碟機。 

如需詳細資訊，請參閱 <<c0> [ 了解 Azure 中 Windows Vm 上的暫存磁碟機](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> 在 Azure VM 中的磁碟機 D 是非保存的磁碟機，而由 Azure 計算節點上的一些本機磁碟。 因為它是保存在 D 磁碟機上的內容所做的任何變更都會遺失，當 VM 重新開機。 變更包含已儲存的檔案、 已建立的目錄和已安裝的應用程式。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm 自動掛接磁碟機，在非保存是由 Azure 計算節點上的本機磁碟的磁碟機的 /mnt/resource。 因為它是非保存的重新啟動 VM 時，對 /mnt /mnt/resource 的內容做任何變更都會遺失。 變更包含已儲存的檔案、 已建立的目錄和已安裝的應用程式。
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 儲存體復原
Microsoft Azure 儲存體儲存的基底 VHD，OS 和連接的磁碟或 blob，使用至少三個不同的儲存體節點上。 這種類型的儲存體稱為 「 本地備援儲存體 (LRS) 」。 LRS 是在 Azure 中的儲存體的所有類型的預設值。

有其他複本建立方法。 如需詳細資訊，請參閱 [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
>Premium storage 是建議的 DBMS Vm 和儲存資料庫和記錄檔和取消復原檔案的磁碟儲存體類型。 進階儲存體僅適用於備援方法為 LRS。 如此一來，您需要設定資料庫的方法，以啟用資料庫的資料複寫到另一個 Azure 區域或可用性區域。 資料庫的方法包括 SQL Server Alwayson、 Oracle Data Guard 和 HANA 系統複寫。


> [!NOTE]
> 針對 DBMS 部署，不建議標準儲存體的異地備援儲存體 (GRS) 使用。 GRS 會嚴重影響效能，以及不接受跨不同的 Vhd 連結至 VM 的寫入順序。 不會執行的寫入順序到不同的 Vhd 可能會導致不一致的資料庫在複寫目標端。 發生這種情況如果資料庫和記錄檔和取消復原的檔案會分散到多個 Vhd，大致上相同的情況下，來源 VM 端上。



## <a name="vm-node-resiliency"></a>VM 節點復原
Azure 提供數個不同的 Sla，適用於 Vm。 如需詳細資訊，請參閱最新版本[虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 所以 DBMS 層可用性通常是重要 SAP 系統中，您需要了解可用性設定組，可用性區域，以及維護事件。 如需有關這些概念的詳細資訊，請參閱 <<c0> [ 管理 Azure 中 Windows 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)並[管理 Azure 中 Linux 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

針對實際執行 DBMS 案例與 SAP 工作負載最低的建議是：

- 在不同的可用性設定組位於相同的 Azure 區域中部署兩個 Vm。
- 在相同的 Azure 虛擬網路中執行這些兩個的 Vm，而且必須連結從相同的子網路的 Nic。
- 使用資料庫方法讓第二部 VM 保持熱待命。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA System Replication。

您也可以部署第三個 VM，另一個 Azure 區域中，並使用相同的資料庫方法來提供另一個 Azure 區域中的非同步複本。

如需如何設定 Azure 可用性設定組的詳細資訊，請參閱[本教學課程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。



## <a name="azure-network-considerations"></a>Azure 的網路考量
在大規模的 SAP 部署中，使用的藍圖[Azure 虛擬資料中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)。 您可以將它用於您的虛擬網路組態和權限和角色指派至您的組織的不同部分。

這些最佳作法是客戶部署數百個的結果：

- SAP 應用程式部署到虛擬網路不需要存取網際網路。
- 在應用程式層相同的虛擬網路中，執行資料庫 Vm。
- 虛擬網路內的 Vm 具有靜態私人 IP 位址的配置。 如需詳細資訊，請參閱 < [IP 位址類型及配置方法，在 Azure 中的](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- 路由的限制，則 DBMS Vm 來回*不*搭配安裝在本機的 DBMS Vm 上的防火牆設定。 相反地，流量路由以定義[網路安全性群組 (Nsg)](https://docs.microsoft.com/azure/virtual-network/security-overview)。
- 若要分隔及隔離在 DBMS VM 的流量，將指派給 VM 的不同的 Nic。 每個 NIC 取得不同的 IP 位址，以及每個 NIC 會指派給不同的虛擬網路子網路。 每一個子網路具有不同的 NSG 規則。 隔離或分隔的網路流量是路由的量值。 它不用來設定的網路輸送量配額。

> [!NOTE]
> 指派靜態 IP 位址，透過 Azure，表示將它們指派給個別的虛擬 Nic。 請勿將客體 OS 內的靜態 IP 位址指派給虛擬 nic。 某些 Azure 服務，例如 Azure 備份依賴在最主要的虛擬 NIC 設定 DHCP，不適用於靜態 IP 位址。 如需詳細資訊，請參閱 <<c0> [ 疑難排解 Azure 虛擬機器備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要將多個靜態 IP 位址指派至 VM，請指派多個虛擬 Nic 的 vm。
>


> [!IMPORTANT]
> 設定[網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)中 SAP 應用程式和 DBMS 層的 SAP NetWeaver-之間的通訊路徑，Hybris 或 S/4HANA 為基礎的 SAP 系統不支援。 這項限制是基於功能和效能的考量。 SAP 應用程式層與 DBMS 層之間的通訊路徑必須是一個直接的帳戶。 這項限制不包含[應用程式安全性群組 (ASG) 和 NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)如果 ASG 和 NSG 規則允許直接通訊路徑。 
>
> 其他情況下，不支援網路虛擬設備的位置是：
>
> * 代表 Linux Pacemaker 的 Azure Vm 之間的通訊路徑叢集節點和 SBD 裝置中所述[適用於 SUSE Linux Enterprise Server for SAP 應用程式上的 Azure Vm 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)。
> * Azure Vm 和 Windows Server 向外延展檔案伺服器 (SOFS) 之間的通訊路徑設定中所述的最多[Windows 容錯移轉叢集上叢集 SAP ASCS/SCS 執行個體，在 Azure 中使用檔案共用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 網路虛擬設備在通訊路徑中的可以輕鬆地 double 的兩個通訊合作夥伴之間的網路延遲。 它們也可以限制在 SAP 應用程式層與 DBMS 層之間的重要路徑中的輸送量。 在某些客戶情況下，網路虛擬設備可能會導致失敗的 Pacemaker Linux 叢集。 這些是 Linux Pacemaker 叢集節點間通訊通訊的地方 SBD 裝置透過網路虛擬設備的情況。
>

> [!IMPORTANT]
> 另一個所設計的*不*支援會隔離到不同 Azure 虛擬網路的 SAP 應用程式層與 DBMS 層[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)彼此。 我們建議使用不同的 Azure 虛擬網路中使用而不是在 Azure 虛擬網路內的子網路隔離的 SAP 應用程式層和 DBMS 層。 
>
> 如果您決定不要遵循建議，並改為隔離到不同的虛擬網路的兩個層，必須是兩個虛擬網路[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 請注意，網路兩個之間的流量[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虛擬網路受到傳輸成本。 SAP 應用程式層與 DBMS 層之間交換包含多個 tb 的大型資料磁碟區。 如果 SAP 應用程式層和 DBMS 層隔離出來兩個對等互連的 Azure 虛擬網路之間，您可以累積大量的成本。

使用您的生產環境內的 Azure 可用性的 DBMS 部署的兩個 Vm 設定。 也使用的 SAP 應用程式層和兩個 DBMS Vm 的管理和作業流量的個別路由。 請見下圖：

![兩個子網路中兩部 VM 的圖表](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure Load Balancer 將流量重新導向
使用 SQL Server Always On 或 HANA 系統複寫中使用的私人虛擬 IP 位址需要 Azure load balancer 的組態。 負載平衡器會使用探查連接埠，判斷使用中 DBMS 節點，並將流量路由傳送至該使用中的資料庫節點以獨佔方式。 

如果沒有資料庫節點的容錯移轉，則不需要重新設定的 SAP 應用程式。 相反地，針對私人虛擬 IP 位址重新連接的最常見的 SAP 應用程式架構。 同時，負載平衡器回應節點的容錯移轉重新針對私人虛擬 IP 位址的流量導向至第二個節點。

Azure 提供兩個不同[負載平衡器 Sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)： 基本 SKU 及標準 SKU。 除非您想要部署在 Azure 可用性區域，基本 load balancer SKU 將會正常執行。

為 DBMS Vm 與 SAP 應用程式層，一律會路由傳送透過負載平衡器的所有時間之間的流量？ 答案取決於您設定負載平衡器的方式。 

在這個階段中，在 DBMS VM 的連入流量一律會透過負載平衡器路由傳送。 連出流量路由在 DBMS VM 從應用程式層 VM 的負載平衡器組態而定。 

負載平衡器會提供 DirectServerReturn 選項。 已設定該選項，在 DBMS VM 從 SAP 應用程式層到重新導向到的流量是否*不*透過負載平衡器路由傳送。 相反地，它將直接移至應用程式層。 如果 DirectServerReturn 未設定，會將 SAP 應用程式層的傳回流量路由透過負載平衡器。

我們建議您設定 DirectServerReturn 搭配位於 SAP 應用程式層與 DBMS 層之間的負載平衡器。 此設定可減少兩個圖層之間的網路延遲。

如需如何設定此組態與 SQL Server Always On 的範例，請參閱 <<c0> [ 在 Azure 中設定 Always On 可用性群組的 ILB 接聽程式](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)。

如果您使用已發佈的 GitHub JSON 範本做為參考您在 Azure 中的 SAP 基礎結構部署，請研究這[SAP 3 層系統範本](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此範本中，您也可以看到負載平衡器的正確設定。

### <a name="azure-accelerated-networking"></a>Azure 加速網路
若要進一步減少 Azure Vm 之間的網路延遲，我們建議您選擇[Azure 加速網路](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 當您部署的 SAP 工作負載，特別是針對 SAP 應用程式層和 SAP DBMS 層的 Azure Vm 時，請使用它。

> [!NOTE]
> 並非所有的 VM 類型都支援加速網路。 前一篇文章會列出支援加速網路的 VM 類型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 若要了解如何部署具有加速網路的 Windows Vm，請參閱[建立使用加速網路的 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 如需有關 Linux 散發套件的詳細資訊，請參閱[建立有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> 如果是 SUSE、Red Hat 和 Oracle Linux，最近版本都支援加速網路。 SLES 12 SP2 或 RHEL 7.2 等較舊的版本不支援 Azure 加速網路。
>


## <a name="deployment-of-host-monitoring"></a>部署主機監視功能
針對生產用途的 Azure 虛擬機器中的 SAP 應用程式，SAP 需要能夠取得主機監視資料，從執行 Azure 虛擬機器的實體主機。 需要有特定的 SAP HostAgent 修補程式等級，才能在 SAPOSCOL 和 SAP HostAgent 中啟用此功能。 確切的修補程式等級記載於 SAP 附註 [1409604]。

如需有關的主應用程式將資料傳遞給 SAPOSCOL 和 SAP Host Agent 的元件及這些元件的生命週期管理的詳細資訊，請參閱[部署指南][deployment-guide]。


## <a name="next-steps"></a>後續步驟
如需有關特定 DBMS 的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 SQL Server Azure 虛擬機器 DBMS 部署](dbms_guide_sqlserver.md)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](dbms_guide_oracle.md)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](dbms_guide_ibm.md)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和 Content Server 部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP HANA 作業指南](hana-vm-operations.md)
- [Azure 虛擬機器的 SAP Hana 高可用性](sap-hana-availability-overview.md)
- [Azure 虛擬機器上的 SAP HANA 的備份指南](sap-hana-backup-guide.md)

