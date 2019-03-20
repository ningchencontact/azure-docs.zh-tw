---
title: 使用 Azure NetApp 檔案的 SUSE Linux Enterprise Server 上的 SAP NetWeaver 的 azure 虛擬機器高可用性 |Microsoft Docs
description: SUSE Linux Enterprise Server for SAP 應用程式的 Azure NetApp 檔案上的 SAP NetWeaver 的高可用性指南
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 862e99b1ab44c097429dcf015b6f8c8ae023e6bc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225496"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>適用於 SUSE Linux Enterprise Server for SAP 應用程式的 Azure NetApp 檔案上的 Azure Vm 上的 SAP NetWeaver 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

這篇文章說明如何部署虛擬機器設定虛擬機器、 安裝叢集架構，並安裝高可用性的 SAP NetWeaver 7.50 系統，使用[Azure NetApp 檔案](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/)。
在範例組態、 安裝命令等、 ASCS 執行個體號碼 00、 ERS 執行個體號碼 01、 主要的應用程式執行個體 (PAS) 是 02 且應用程式執行個體 (AAS) 是 03。 SAP 系統識別碼 QAS 會使用。 

這篇文章說明如何使用 Azure NetApp 檔案達到高可用性的 SAP NetWeaver 應用程式。 資料庫層級未涵蓋在本文中詳述。

請先閱讀下列 SAP Note 和文件：

* [Azure 的 NetApp 檔案文件][anf-azure-doc] 
* SAP Note [1928533]，其中包含：  
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2205917] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的建議 OS 設定
* SAP Note [1944799] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 指導方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1984787] 包含 SUSE LINUX Enterprise Server 12 的一般資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [在 Linux 上為 SAP 進行 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳做法指南][suse-ha-guide] 此指南包含設定內部部署 Netweaver HA 和 SAP HANA 系統複寫的所有必要資訊。 請使用這些指南作為一般基準。 它們提供更詳細的資訊。
* [SUSE 高可用性擴充 12 SP3 版本資訊][suse-ha-12sp3-relnotes]
* [Microsoft Azure 上使用 Azure NetApp 檔案 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>概觀

SAP Netweaver 的中央服務的高 availability(HA) 需要共用存放裝置。
為了達到此目標 SUSE Linux 上目前則需要建立個別的高可用性 NFS 叢集。 

現在就可以使用來達到 SAP Netweaver HA 部署於 Azure NetApp 檔案的共用存放裝置。 使用 Azure NetApp 檔案共用的存放裝置就不需要額外[NFS 叢集](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。 SAP Netweaver 中央 services(ASCS/SCS) 的 HA 仍然需要 pacemaker。


![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP Hana 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上[負載平衡器](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview)才可使用的虛擬 IP 位址。 下列清單顯示 (A)SCS 和 ERS 負載平衡器的組態。

### <a name="ascs"></a>(A)SCS

* 前端組態
  * 10.1.1.20 的 IP 位址
* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面
* 探查連接埠
  * 連接埠 620<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 前端組態
  * 10.1.1.21 的 IP 位址
* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面
* 探查連接埠
  * 連接埠 621<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>設定 Azure NetApp 檔案基礎結構 

SAP NetWeaver 需要傳輸和設定檔目錄的共用儲存體。  繼續進行 Azure NetApp 檔案基礎結構的安裝程式之前，先熟悉[Azure NetApp 檔案文件][anf-azure-doc]。 如果您選取的 Azure 區域提供 Azure NetApp 檔案的檢查。 下列連結會顯示依 Azure 區域的 Azure NetApp 檔案的可用性：[Azure 的 NetApp 檔案 Azure 區域可用性][anf-avail-matrix]。

「 Azure NetApp 檔案 」 功能是在數個 Azure 區域為公開預覽狀態。 在部署 Azure NetApp 檔案時，註冊 Azure NetApp 檔案預覽中，遵循[註冊 Azure NetApp 檔案指示][anf-register]。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 檔案資源  

步驟假設您有已部署[Azure 虛擬網路](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)。 請記住 Azure NetApp 檔案資源和 Vm，可掛接的 Azure NetApp 檔案資源必須部署在相同的 Azure 虛擬網路中。  

1. 如果您還沒有已完成，要求[註冊 Azure NetApp 預覽版](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 在選取的 Azure 區域，依照建立 NetApp 帳戶[建立 NetApp 帳戶指示](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)。  
3. 設定 Azure NetApp 檔案容量集區，遵循[有關如何設定 Azure NetApp 檔案容量集區](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)。  
在本文中介紹的 SAP Netweaver 架構會使用單一 Azure NetApp 檔案容量集區，Premium SKU。 我們建議 Azure NetApp 檔案進階 SKU 在 Azure 上的 SAP Netweaver 應用程式工作負載。  

4. 委派 Azure NetApp 檔案的子網路中所述[指示委派至 Azure NetApp 檔案的子網路](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

5. 部署 Azure NetApp 檔案磁碟區，遵循[指示 Azure NetApp 檔案建立磁碟區](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes)。 部署中指定的 Azure NetApp 檔案的磁碟區[子網路](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets)。 請記住 Azure NetApp 檔案資源和 Azure Vm 必須位於相同的 Azure 虛擬網路。 例如 sapmnt<b>QAS</b>，usrsap<b>QAS</b>等是磁碟區名稱和 sapmnt<b>qas</b>，usrsap<b>qas</b>等 filepaths 對於 AzureNetApp 檔案的磁碟區。  

   1. 磁碟區 sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. 磁碟區 usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. 磁碟區 usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. 磁碟區 usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. 磁碟區 trans (nfs://10.1.0.4/trans)
   6. 磁碟區 usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pa)
   7. 磁碟區 usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
在此範例中，我們可以使用適用於所有的 SAP Netweaver 檔案系統的 Azure NetApp 檔案示範如何使用 Azure NetApp 檔案。 您也可以做為部署不需要透過 NFS 掛接的 SAP 檔案系統[Azure 磁碟儲存體](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd)。 在此範例中<b>的-e</b>必須是對 Azure NetApp 檔案和<b>f g</b> (也就是 /usr/sap/<b>QAS</b>/D<b>02</b>，/usr/sap/<b>QAS</b>/D<b>03</b>) 可以部署為 Azure 磁碟儲存體。 

### <a name="important-considerations"></a>重要考量︰

在考慮 Azure NetApp 檔案上的 SAP Netweaver SUSE 高可用性架構時，請注意下列幾項重要考量：

- 最小容量集區會是 4 TiB。 容量集區大小必須是 4 TiB 的倍數。
- 最小的磁碟區是 100 GiB
- Azure NetApp 檔案並將所有的虛擬機器將會掛接 Azure NetApp 檔案磁碟區必須位於相同的 Azure 虛擬網路。 [虛擬網路對等互連](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)尚未支援 Azure NetApp 檔案。
- 選取的虛擬網路必須有子網路，委派給 Azure NetApp 檔案。
- Azure 的 NetApp 檔案服務目前支援僅 NFSv3 
- Azure 的 NetApp 檔案服務可提供[匯出原則](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)： 您可以控制允許的用戶端，存取類型 （讀取與寫入、 唯讀等等。）。 
- Azure 的 NetApp 檔案功能尚無法感知區域。 目前 Azure NetApp 檔案功能不被部署的 Azure 區域中的所有可用性區域中。 請留意的某些 Azure 區域中潛在的延遲影響。 

   > [!NOTE]
   > 請注意，Azure NetApp 檔案並不支援尚未對等互連的虛擬網路。 部署 Vm 和 Azure NetApp 檔案中的磁碟區相同的虛擬網路。

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Linux Vm

首先，您要建立 Azure NetApp 檔案磁碟區。 部署 Vm。 之後，您需建立負載平衡器，然後使用後端集區中的虛擬機器。

1. 建立資源群組
1. 建立虛擬網路
1. 為 ASCS 建立可用性設定組  
   設定更新網域上限
1. 建立虛擬機器 1  
   至少使用 SLES4SAP 12 SP3，在此範例中使用 SLES4SAP 12 SP3 的映像  
   選取可用性設定組稍早為 ASCS 建立  
1. 建立虛擬機器 2  
   至少使用 SLES4SAP 12 SP3，在此範例中使用 SLES4SAP 12 SP3 的映像  
   選取可用性設定組稍早為 ASCS 建立  
1. 建立可用性設定組 （PAS、 AAS） 的 SAP 應用程式執行個體    
   設定更新網域上限
1. 建立虛擬機器 3  
   至少使用 SLES4SAP 12 SP3，在此範例中使用 SLES4SAP 12 SP3 的映像  
   選取可用性設定組稍早建立的 PA/AAS   
1. 建立虛擬機器 4  
   至少使用 SLES4SAP 12 SP3，在此範例中使用 SLES4SAP 12 SP3 的映像  
   選取可用性設定組稍早建立的 PA/AAS  

## <a name="setting-up-ascs"></a>設定 (A)SCS

在此範例中，已部署資源以手動方式透過[Azure 入口網站](https://portal.azure.com/#home)。

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Azure 負載平衡器

首先，您要建立 Azure NetApp 檔案磁碟區。 部署 Vm。 之後，您需建立負載平衡器，然後使用後端集區中的虛擬機器。

1. 建立負載平衡器 (內部)  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址 10.1.1.20
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如**前端。QAS。ASCS**)
         1. 將 [指派] 設定為靜態，並輸入 IP 位址 (例如**10.1.1.20**)
         1. Click OK
      1. 針對 ASCS ERS 的 IP 位址 10.1.1.21
         * 重複上述步驟，在"a"建立 ERS 的 IP 位址 (例如**10.1.1.21**和**前端。QAS。ERS**)
   1. 建立後端集區
      1. 建立 ASCS 的後端集區
         1. 開啟負載平衡器，選取後端集區，然後按一下 [新增]
         1. 輸入新的後端集區的名稱 (例如**後端。QAS**)
         1. 按一下 [新增虛擬機器]。
         1. 選取您稍早為 ASCS 建立可用性設定組 
         1. 選取 (A)SCS 叢集的虛擬機器
         1. Click OK
   1. 建立健康狀態探查
      1. 針對 ASCS 是連接埠 620**00**
         1. 開啟負載平衡器，選取健康情況探查，然後按一下 [新增]
         1. 輸入新的健全狀況探查的名稱 (例如**健全狀況。QAS。ASCS**)
         1. 選取 [TCP] 作為通訊協定、連接埠 620**00**，保留 [間隔] 5 和 [狀況不良閾值] 2
         1. Click OK
      1. 連接埠 621**01**針對 ASCS ERS
            * 重複上述步驟，在"c"，以建立 ERS 的健康情況探查 (例如 621**01**和**健全狀況。QAS。ERS**)
   1. 負載平衡規則
      1. 針對 ASCS 是 32**00** TCP
         1. 開啟負載平衡器、 選取負載平衡規則然後按一下 [新增]
         1. 輸入新的負載平衡器規則的名稱 (例如**lb。QAS。ASCS.3200**)
         1. 針對 ASCS、 後端集區，以及您稍早建立的健康情況探查選取前端 IP 位址 (例如**前端。QAS。ASCS**)
         1. 保留通訊協定 [TCP]，輸入連接埠 **3200**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
      1. ASCS 的其他連接埠
         * 重複上述步驟，在"d"連接埠 36**00**、 39**00**、 81**00**、 5**00**13、 5**00**14、 5**00**16 和 TCP ascs
      1. ASCS ERS 的其他連接埠
         * 重複上述步驟，在"d"連接埠 33**01**、 5**01**13、 5**01**14、 5**01**16 和 TCP ASCS ers

> [!IMPORTANT]
> 不會啟用 TCP 放置 Azure 負載平衡器後方的 Azure Vm 上的時間戳記。 啟用 TCP 加上時間戳記將會造成失敗的健康狀態探查。 設定參數**net.ipv4.tcp_timestamps**要**0**。 如需詳細資訊，請參閱[負載平衡器健康情況探查](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>建立 Pacemaker 叢集

依照[在 Azure 中於 SUSE Linux Enterprise Server 上設定 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的步驟，建立此 (A)SCS 伺服器的基本 Pacemaker 叢集。

### <a name="installation"></a>安裝

下列項目會加上下列其中一個前置詞：**[A]** - 適用於所有節點、**[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 安裝 SUSE 連接器

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > 請勿在叢集節點的主機名稱中使用短破折號。 否則叢集將無法運作。 這是已知的限制，而 SUSE 正在努力研發修正程式。 修正程式將會作為 sap-suse-cloud-connector 套件的修補程式來發行。

   請確定您已安裝新版的 SAP SUSE 叢集連接器。 舊版連接器稱為 sap_suse_cluster_connector，新版連接器稱為 **sap-suse-cluster-connector**。

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** 更新 SAP 資源代理程式  
   
   資源代理程式套件必須有修補程式才能使用新的組態，本文會有該修補程式的說明。 您可以查看您是否已使用下列命令安裝了修補程式

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   輸出應該會類似

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   如果 grep 命令找不到 IS_ERS 參數，您必須安裝 [SUSE 下載頁面](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)上所列出的修補程式

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>準備進行 SAP NetWeaver 安裝

1. **[A]** 建立共用目錄

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** 設定 autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   使用下列命令建立檔案

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Azure NetApp 檔案服務目前支援僅 NFSv3。 不省略 nfsvers = 3 個參數。
   
   重新啟動 autofs 來裝載新的共用
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** 設定分頁檔

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新啟動代理程式以啟動變更

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>安裝 SAP NetWeaver ASCS/ERS

1. **[1]** 為 ASCS 執行個體建立虛擬 IP 資源和健康情況探查

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** 安裝 SAP NetWeaver ASCS  

   使用虛擬主機名稱，例如對應至 ASCS，負載平衡器前端組態的 IP 位址的第一個節點上的 root 身分安裝 SAP NetWeaver ASCS <b>anftstsapvh</b>， <b>10.1.1.20</b>和比方說用於負載平衡器探查的執行個體編號<b>00</b>。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數 SAPINST_USE_HOSTNAME 安裝 SAP，使用虛擬主機名稱。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   如果安裝作業無法在 /usr/sap/ 中建立子資料夾**QAS**/ASCS**00**，請嘗試設定的擁有者和群組 ASCS**00**資料夾，然後重試。 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** 為 ERS 執行個體建立虛擬 IP 資源和健康情況探查

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** 安裝 SAP NetWeaver ERS

   使用虛擬主機名稱對應至 ERS 負載平衡器前端組態的 IP 位址，例如，在第二個節點上的 root 身分安裝 SAP NetWeaver ERS <b>anftstsapers</b>， <b>10.1.1.21</b>和比方說用於負載平衡器探查的執行個體編號<b>01</b>。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。 您可以使用參數 SAPINST_USE_HOSTNAME 安裝 SAP，使用虛擬主機名稱。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > 請使用 SWPM SP 20 PL 05 或更高版本。 較低版本無法正確設定權限，因而會讓安裝失敗。

   如果安裝作業無法在 /usr/sap/ 中建立子資料夾**QAS**/ERS**01**，請嘗試設定的擁有者和群組 ERS**01**資料夾，然後重試。

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** 調整 ASCS/SCS 和 ERS 執行個體設定檔
 
   * ASCS/SCS 設定檔

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS 設定檔

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** 設定保持運作

   SAP NetWeaver 應用程式伺服器和 ASCS/SCS 之間的通訊是透過軟體負載平衡器來路由傳送。 在逾時時間 (可設定) 過後，負載平衡器就會將非作用中的連線中斷。 為防止這個情況，您需要在 SAP NetWeaver ASCS/SCS 設定檔中設定參數，並變更 Linux 系統設定。 如需詳細資訊，請閱讀 [SAP Note 1410736][1410736]。

   ASCS/SCS 設定檔參數 enque/encni/set_so_keepalive 已在最後一個步驟中新增。

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** 在安裝過後設定 SAP 使用者

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** 在 sapservice 檔案中新增 ASCS 和 ERS SAP 服務

   在第二個節點中新增 ASCS 服務項目，並將 ERS 服務項目複製到第一個節點。

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** 建立 SAP 叢集資源

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   請確定叢集狀態正常，且所有資源皆已啟動。 資源在哪一個節點上執行並不重要。

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver 應用程式伺服器準備 

某些資料庫需要在應用程式伺服器上執行資料庫執行個體安裝。 準備應用程式伺服器虛擬機器，以便在這些情況下使用它們。

以下步驟假設您將應用程式伺服器安裝在與 ASCS/SCS 和 HANA 伺服器不同的伺服器上。 否則，您就不必進行以下某些步驟 (例如設定主機名稱解析)。

下列項目會加上其中一個 **[A]** -PA 和 AAS，適用於 **[P]** -僅適用於 PAS 或 **[S]** -僅適用於 AAS。


1. **[A]** 設定作業系統

   縮減已變更的快取大小。 如需詳細資訊，請參閱[使用大量 RAM 的 SLES 11/12 伺服器出現低寫入效能](https://www.suse.com/support/kb/doc/?id=7010287) (英文)。

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

   ```bash
   sudo vi /etc/hosts
   ```

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** 建立 sapmnt 目錄

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** 建立 PAS 目錄

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** 建立 AAS 目錄

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** PAS 上設定 autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   使用下列命令建立新檔案

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   重新啟動 autofs 來裝載新的共用

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** AAS 上設定 autofs

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   使用下列命令建立新檔案

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   重新啟動 autofs 來裝載新的共用

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** 設定分頁檔

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新啟動代理程式以啟動變更

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>安裝資料庫

在此範例中，SAP NetWeaver 安裝在 SAP Hana 上。 您可以針對此安裝使用每個支援的資料庫。 如需如何在 Azure 中安裝 SAP Hana 的詳細資訊，請參閱 [Azure 虛擬機器 (VM) 上的 SAP Hana 高可用性][sap-hana-ha]。 如需支援的資料庫清單，請參閱 [SAP Note 1928533][1928533]。

* 執行 SAP 資料庫執行個體安裝

   安裝 SAP NetWeaver 資料庫執行個體 root 身分使用虛擬主機名稱對應至資料庫負載平衡器前端組態的 IP 位址。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 應用程式伺服器安裝

請遵循下列步驟來安裝 SAP 應用程式伺服器。

1. **[A]** 準備應用程式伺服器，請依照下列章節中的步驟[SAP NetWeaver 應用程式伺服器準備](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)上述準備應用程式伺服器。

2. **[A]** 安裝 SAP NetWeaver 應用程式伺服器安裝的主要或其他 SAP NetWeaver 應用程式伺服器。

   您可以使用 sapinst 參數 SAPINST_REMOTE_ACCESS_USER 來允許非 root 使用者連線到 sapinst。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** 更新 SAP HANA 安全存放區

   將 SAP HANA 安全存放區更新為指向 SAP HANA 系統複寫設定的虛擬名稱。

   執行下列命令來列出項目
   <pre><code>
   hdbuserstore List
   </code></pre>

   這樣應該會列出所有項目，且看起來應該類似
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   輸出會顯示預設項目的 IP 位址指向虛擬機器，而不是指向負載平衡器的 IP 位址。 這個項目必須變更才能指向負載平衡器的虛擬機器主機名稱。 請務必使用相同的連接埠 (**30313**上述輸出中) 和資料庫名稱 (**QAS**上述輸出) ！

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>測試叢集設定

下列測試是測試案例中的複本[最佳做法指南的 SUSE][suse-ha-guide]。 為方便起見，我們將案例複製過來。 請同時閱讀最佳做法指南，並執行所有可能已新增的其他測試。

1. 測試 HAGetFailoverConfig、 HACheckConfig 和 HACheckFailoverConfig

   以 \<sapsid>adm 身份在目前執行 ASCS 執行個體的節點上執行下列命令。 如果命令失敗，並且出現「失敗：記憶體不足」，則可能是因為您的主機名稱中有短破折號。 這是已知問題，SUSE 會透過 sap-suse-cluster-connector 套件提供修正程式。

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. 手動移轉 ASCS 執行個體

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   以 root 身份執行下列命令，來遷移 ASCS 執行個體。

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. 測試 HAFailoverToNode

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   執行下列命令為<sapsid>adm 移轉 ASCS 執行個體。

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. 模擬節點損毀 

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   如果您使用 SBD，Pacemaker 應不會在已終止的節點上自動啟動。 節點在重新啟動後的狀態應該會像下面這樣。

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   使用下列命令在已清除的節點上啟動 Pacemaker，並清除 SBD 訊息及失敗的資源。

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. 測試以手動方式重新啟動 ASCS 執行個體

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   例如，透過編輯交易 su01 中的使用者來建立佇列鎖定。 執行下列命令，為 < sapsid\>adm ASCS 執行個體執行所在的節點上。 這些命令會停止 ASCS 執行個體，並重新啟動它。 在這項測試中，佇列鎖定應該會遺失。

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS 執行個體應會立即在 Pacemaker 中停用

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   在相同節點上再次啟動 ASCS 執行個體。

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   交易 su01 的佇列鎖定應該會遺失，而且後端應已重設。 測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. 終止訊息伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   以 root 身份執行下列命令，找出訊息伺服器的處理程序，並將其終止。

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   如果您只終止訊息伺服器一次，sapstart 會將伺服器重新啟動。 如果您終止伺服器的次數足夠，則 Pacemaker 最終會將 ASCS 執行個體移到另一個節點。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. 終止佇列伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   以 root 身份在執行 ASCS 執行個體的節點上執行下列命令，以終止佇列伺服器。

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS 執行個體應會立即容錯移轉到另一個節點。 在 ASCS 執行個體啟動之後，ERS 執行個體應該也會進行容錯移轉。 以 root 身份執行下列命令，以在測試之後清除 ASCS 和 ERS 執行個體的資源狀態。

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. 終止佇列複寫伺服器處理程序

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   以 root 身份在執行 ERS 執行個體的節點上執行下列命令，以終止佇列複寫伺服器處理程序。

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   如果您只執行此命令一次，sapstart 會重新啟動處理程序。 如果您執行命令的次數足夠，則 sapstart 將不會重新啟動處理程序，而資源會處於停止狀態。 以 root 身份執行下列命令，以在測試之後清除 ERS 執行個體的資源狀態。

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. 終止佇列 sapstartsrv 程序

   開始測試之前的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   以 root 身份在執行 ASCS 的節點上執行下列命令。

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   應一律由 Pacemaker 資源代理程式來重新啟動 sapstartsrv 處理程序。 測試完成之後的資源狀態：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>後續步驟

* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性和災害復原的 SAP 的計畫 
* HANA on Azure （大型執行個體），請參閱[SAP HANA （大型執行個體） 上的高可用性和災害復原 Azure](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性並為 Azure VM 上的 SAP HANA 規劃災害復原，請參閱 [Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性][sap-hana-ha]
