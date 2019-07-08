---
title: 使用 Azure NetApp 檔案的 Red Hat Enterprise Linux 上的 SAP NetWeaver 的 azure 虛擬機器高可用性 |Microsoft Docs
description: SAP NetWeaver on Red Hat Enterprise Linux 的 Azure 虛擬機器高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503574"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Red Hat Enterprise Linux for SAP 應用程式的 Azure NetApp 檔案上的 SAP NetWeaver 的 azure 虛擬機器高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

這篇文章說明如何部署虛擬機器設定虛擬機器、 安裝叢集架構，並安裝高可用性的 SAP NetWeaver 7.50 系統，使用[Azure NetApp 檔案](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)。
在範例組態中，安裝命令等。ASCS 執行個體是號碼 00、 ERS 執行個體號碼 01、 主要的應用程式執行個體 (PAS) 是 02，和應用程式執行個體 (AAS) 是 03。 SAP 系統識別碼 QAS 會使用。 

資料庫層級未涵蓋在本文中詳述。  

請先閱讀下列 SAP Note 和文件：

* [Azure 的 NetApp 檔案文件][anf-azure-doc] 
* SAP 说明 [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本

* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2002167] 建議適用於 Red Hat Enterprise Linux 的作業系統設定
* SAP Note [2009879] 提供適用於 Red Hat Enterprise Linux 的 SAP HANA 方針
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [Azure 虛擬機器規劃和實作適用於 SAP on Linux][planning-guide]
* [適用於 SAP on Linux 的 azure 虛擬機器部署][deployment-guide]
* [適用於 SAP on Linux 的 azure 虛擬機器 DBMS 部署][dbms-guide]
* [Pacemaker 叢集中的 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 一般 RHEL 文件
  * [高可用性附加元件概觀](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性附加元件管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性附加元件參考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [在 RHEL 7.5 中使用獨立資源為 SAP Netweaver 設定 ASCS/ERS](https://access.redhat.com/articles/3569681)
  * [使用在 RHEL 上的 Pacemaker 的獨立加入佇列伺服器 2 (ENSA2) 設定 SAP S/4HANA ASCS/ERS ](https://access.redhat.com/articles/3974941)
* Azure 專用 RHEL 文件：
  * [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員](https://access.redhat.com/articles/3131341)
  * [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集](https://access.redhat.com/articles/3252491)
* [Microsoft Azure 上使用 Azure NetApp 檔案 NetApp SAP 應用程式][anf-sap-applications-azure]

## <a name="overview"></a>概觀

SAP Netweaver 的中央服務的高 availability(HA) 需要共用存放裝置。
為了達到此目標 Red Hat Linux 上目前則需要建立個別的 GlusterFS 高可用性叢集。 

現在就可以使用來達到 SAP Netweaver HA 部署於 Azure NetApp 檔案的共用存放裝置。 使用 Azure NetApp 檔案共用的存放裝置就不需要額外[GlusterFS 叢集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)。 SAP Netweaver 中央 services(ASCS/SCS) 的 HA 仍然需要 pacemaker。

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP Hana 資料庫會使用虛擬主機名稱和虛擬 IP 位址。 在 Azure 上必須有負載平衡器才能使用虛擬 IP 位址。 下列清單顯示 (A) SCS 和 ERS 負載平衡器具有個別前端 Ip 組態。

> [!IMPORTANT]
> 多 SID 叢集的 SAP ASCS/ERS 為 Red Hat Linux，因為在 Azure Vm 中的客體作業系統**不支援**。 多 SID 叢集描述安裝多個 SAP ASCS/ERS 執行個體具有不同的 Sid，在一個 Pacemaker 叢集。

### <a name="ascs"></a>(A)SCS

* 前端組態
  * 192.168.14.9 的 IP 位址
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
  * 192.168.14.10 的 IP 位址
* 後端組態
  * 連線到應該屬於 (A)SCS/ERS 叢集一部分之所有虛擬機器的主要網路介面
* 探查連接埠
  * 連接埠 621<strong>&lt;nr&gt;</strong>
* 負載平衡規則
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>設定 Azure NetApp 檔案基礎結構 

SAP NetWeaver 需要傳輸和設定檔目錄的共用儲存體。  繼續進行 Azure NetApp 檔案基礎結構的安裝程式之前，先熟悉[Azure NetApp 檔案文件][anf-azure-doc]。 如果您選取的 Azure 區域提供 Azure NetApp 檔案的檢查。 下列連結會顯示依 Azure 區域的 Azure NetApp 檔案的可用性：[Azure 的 NetApp 檔案 Azure 區域可用性][anf-avail-matrix]。

Azure 的 NetApp 檔案位於數個[Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)。 在部署 Azure NetApp 檔案時，要求上的架到 Azure NetApp 檔案，遵循[註冊 Azure NetApp 檔案指示][anf-register]。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 檔案資源  

步驟假設您有已部署[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 在相同的 Azure 虛擬網路，或對等互連的 Azure 虛擬網路中，必須部署 Azure NetApp 檔案資源和 Vm，可掛接的 Azure NetApp 檔案資源。  

1. 如果您還沒有已完成，要求[上的架到 Azure NetApp 檔案](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  
2. 在選取的 Azure 區域，依照建立 NetApp 帳戶[建立 NetApp 帳戶指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)。  
3. 設定 Azure NetApp 檔案容量集區，遵循[有關如何設定 Azure NetApp 檔案容量集區](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)。  
在本文中介紹的 SAP Netweaver 架構會使用單一 Azure NetApp 檔案容量集區，Premium SKU。 我們建議 Azure NetApp 檔案進階 SKU 在 Azure 上的 SAP Netweaver 應用程式工作負載。  
4. 委派 Azure NetApp 檔案的子網路中所述[指示委派至 Azure NetApp 檔案的子網路](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

5. 部署 Azure NetApp 檔案磁碟區，遵循[指示 Azure NetApp 檔案建立磁碟區](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)。 部署中指定的 Azure NetApp 檔案的磁碟區[子網路](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)。 請記住 Azure NetApp 檔案資源和 Azure Vm 必須位於相同的 Azure 虛擬網路或對等互連的 Azure 虛擬網路中。 在此範例中我們會使用兩個 Azure NetApp 檔案磁碟區： sap<b>QAS</b>和 transSAP。 掛接到對應的掛接點的檔案路徑是 /usrsap<b>qas</b>/sapmnt<b>QAS</b>，/usrsap<b>qas</b>/usrsap<b>QAS</b>sys等。  

   1. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. 磁碟區 transSAP (nfs://192.168.24.4/transSAP)
   6. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pa)
   7. 磁碟區的 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
在此範例中，我們可以使用適用於所有的 SAP Netweaver 檔案系統的 Azure NetApp 檔案示範如何使用 Azure NetApp 檔案。 您也可以做為部署不需要透過 NFS 掛接的 SAP 檔案系統[Azure 磁碟儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。 在此範例中<b>的-e</b>必須是對 Azure NetApp 檔案和<b>f g</b> (也就是 /usr/sap/<b>QAS</b>/D<b>02</b>，/usr/sap/<b>QAS</b>/D<b>03</b>) 可以部署為 Azure 磁碟儲存體。 

### <a name="important-considerations"></a>重要考量︰

在考慮 Azure NetApp 檔案上的 SAP Netweaver SUSE 高可用性架構時，請注意下列幾項重要考量：

- 最小容量集區會是 4 TiB。 容量集區大小必須是 4 TiB 的倍數。
- 最小的磁碟區是 100 GiB
- Azure 的 NetApp 檔案和所有虛擬機器，其中會裝載 Azure NetApp 檔案磁碟區，必須是相同的 Azure 虛擬網路中或在[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)相同區域中。 現在支援透過 VNET 對等互連相同區域中的 azure NetApp 檔案存取。 尚未支援 azure NetApp 存取透過全域對等互連。
- 選取的虛擬網路必須有子網路，委派給 Azure NetApp 檔案。
- Azure 的 NetApp 檔案服務目前支援僅 NFSv3 
- Azure 的 NetApp 檔案服務可提供[匯出原則](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)： 您可以控制允許的用戶端，存取類型 （讀取與寫入、 唯讀等等。）。 
- Azure 的 NetApp 檔案功能尚無法感知區域。 目前 Azure NetApp 檔案功能不被部署的 Azure 區域中的所有可用性區域中。 請留意的某些 Azure 區域中潛在的延遲影響。 

## <a name="setting-up-ascs"></a>設定 (A)SCS

在此範例中，已部署資源以手動方式透過[Azure 入口網站](https://portal.azure.com/#home)。

### <a name="deploy-linux-manually-via-azure-portal"></a>透過 Azure 入口網站手動部署 Linux

首先，您要建立 Azure NetApp 檔案磁碟區。 部署 Vm。 之後，您需建立負載平衡器，然後使用後端集區中的虛擬機器。

1. 建立負載平衡器 (內部)  
   1. 建立前端 IP 位址
      1. ASCS 的 IP 位址 192.168.14.9
         1. 開啟負載平衡器，選取前端 IP 集區，然後按一下 [新增]
         1. 輸入新前端 IP 集區的名稱 (例如**前端。QAS。ASCS**)
         1. 將 [指派] 設定為靜態，並輸入 IP 位址 (例如**192.168.14.9**)
         1. Click OK
      1. 針對 ASCS ERS 的 IP 位址 192.168.14.10
         * 重複上述步驟，在"a"建立 ERS 的 IP 位址 (例如**192.168.14.10**和**前端。QAS。ERS**)
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
         1. 開啟負載平衡器、 選取負載平衡規則，然後按一下 [新增]
         1. 輸入新的負載平衡器規則的名稱 (例如**lb。QAS。ASCS.3200**)
         1. 針對 ASCS、 後端集區，以及您稍早建立的健康情況探查選取前端 IP 位址 (例如**前端。QAS。ASCS**)
         1. 保留通訊協定 [TCP]  ，輸入連接埠 **3200**
         1. 將閒置逾時增加為 30 分鐘
         1. **務必啟用浮動 IP**
         1. Click OK
      1. ASCS 的其他連接埠
         * 重複上述步驟，在"d"連接埠 36**00**、 39**00**、 81**00**、 5**00**13、 5**00**14、 5**00**16 和 TCP ascs
      1. ASCS ERS 的其他連接埠
         * 重複上述步驟，在"d"的連接埠 32**01**、 33**01**、 5**01**13、 5**01**14、 5**01**16 和 TCP針對 ASCS ERS


> [!IMPORTANT]
> 不會啟用 TCP 放置 Azure 負載平衡器後方的 Azure Vm 上的時間戳記。 啟用 TCP 加上時間戳記將會造成失敗的健康狀態探查。 設定參數**net.ipv4.tcp_timestamps**要**0**。 如需詳細資訊，請參閱[負載平衡器健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>建立 Pacemaker 叢集

依照[在 Azure 中於 Red Hat Enterprise Linux 上設定 Pacemaker](high-availability-guide-rhel-pacemaker.md) 中的步驟，建立此 (A)SCS 伺服器的基本 Pacemaker 叢集。

### <a name="prepare-for-sap-netweaver-installation"></a>準備進行 SAP NetWeaver 安裝

下列項目會加上下列其中一個前置詞： **[A]** - 適用於所有節點、 **[1]** - 僅適用於節點 1 或 **[2]** - 僅適用於節點 2。

1. **[A]** 設定主機名稱解析

   您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱

    ```
    sudo vi /etc/hosts
    ```

   將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** Azure NetApp 檔案磁碟區中的建立 SAP 目錄。  
   在其中一個 Vm 上掛接暫時的 Azure NetApp 檔案磁碟區，並建立 SAP 目錄 （檔案路徑）。  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir-p/sapmnt/QAS sudo mkdir-p /usr/sap/trans sudo mkdir-p /usr/sap/QAS/SYS sudo mkdir-p /usr/sap/QAS/ASCS00 sudo mkdir-p /usr/sap/QAS/ERS01
   
   sudo chattr + 我 sapmnt/QAS sudo chattr + 我 /usr/sap/trans sudo chattr + 我 /usr/sap/QAS/SYS sudo chattr + 我 /usr/sap/QAS/ASCS00 sudo chattr + 我 /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>載入外掛程式： langpacks，產品識別碼、 搜尋-已停用存放庫
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata 為過去 2 週的舊。 安裝 yum cron 嗎？ 或執行： yum makecache 快速
   # <a name="installed-packages"></a>已安裝的套件
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch: x86_64
   # <a name="version------395"></a>版本：3.9.5
   # <a name="release------124el7"></a>版本：124.el7
   # <a name="size---------100-k"></a>大小：100k
   # <a name="repo---------installed"></a>存放庫： 安裝
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>從存放庫： rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>摘要：SAP 叢集資源代理程式和連接器指令碼
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>授權：GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>描述：SAP 資源代理程式和連接器使用的指令碼介面
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>：若要允許管理叢集中的 SAP 執行個體的 pacemaker
   #          <a name="-environment"></a>： 環境。
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>將下列幾行加入至 fstab，儲存並結束
    192.168.24.5:/sapQAS/sapmntQAS sapmnt/QAS nfs rw 難，rsize 則 = 65536，wsize = 65536，vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw 難，rsize 則 = 65536，wsize = 65536，vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw 難，rsize 則 =65536，wsize = 65536，vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>設定 y 屬性 ResourceDisk.EnableSwap
   # <a name="create-and-use-swapfile-on-resource-disk"></a>建立和使用資源磁碟上的分頁檔。
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>設定與屬性 ResourceDisk.SwapSizeMB 交換檔的大小
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>資源磁碟的可用空間是根據虛擬機器大小而異。 請確定您未設定為太大的值。 您可以檢查命令 swapon 使用交換空間
   # <a name="size-of-the-swapfile"></a>分頁檔的大小。
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent 重新啟動
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo 電腦節點待命 anftstsapcl2
   
   sudo 電腦資源建立 fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo 電腦資源建立 vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl2-standby"></a>節點 anftstsapcl2： 待命
   # <a name="online--anftstsapcl1-"></a>線上: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>資源的完整清單：
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):    開始使用的 anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>資源群組： g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  開始使用的 anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     開始使用的 anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>允許存取 SWPM。 此規則不是永久性的。 如果您重新啟動電腦，您必須再次執行命令。
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo 電腦節點 unstandby anftstsapcl2 sudo 電腦節點待命 anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    -群組 g QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    -群組 g QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    -群組 g QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl1-standby"></a>節點 anftstsapcl1： 待命
   # <a name="online--anftstsapcl2-"></a>線上: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>資源的完整清單：
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    開始使用的 anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>資源群組： g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  開始使用的 anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    啟動 anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     開始使用的 anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>資源群組： g QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):  開始使用的 anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):     開始使用的 anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>允許存取 SWPM。 此規則不是永久性的。 如果您重新啟動電腦，您必須再次執行命令。
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>將重新啟動命令變更為 start 命令
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = 本機 $(_EN) pf=$(_PF)
   Start_Program_01 = 本機 $(_EN) pf=$(_PF)
   
   # <a name="add-the-keep-alive-parameter"></a>新增的保持運作的參數
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>將重新啟動命令變更為 start 命令
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>ERS 設定檔中移除自動啟動
   # <a name="autostart--1"></a>自動啟動 = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>變更 Linux 系統組態
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>在節點上安裝 ASCS 的位置，標記為註解下面這一行
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>在節點上安裝 ERS 的位置，標記為註解下面這一行
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo 電腦資源建立 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    -群組 g QAS_AERS
      
    sudo 電腦的條件約束共置新增 g-QAS_AERS g QAS_ASCS-5000 sudo 電腦的條件約束位置 rsc_sap_QAS_ASCS00 規則分數 = 2000 runs_ers_QAS eq 1 sudo 電腦的條件約束順序 g QAS_ASCS 則 g QAS_AERS kind = 選擇性對稱 = false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo 電腦資源建立 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    中繼資源綁定 = 5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    -群組 g QAS_AERS
      
    sudo 電腦的條件約束共置新增 g-QAS_AERS g QAS_ASCS-5000 sudo 電腦的條件約束順序 g QAS_ASCS 則 g QAS_AERS 種類 = 選擇性對稱 = false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pcs status
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>線上: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>資源的完整清單：
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    開始使用的 anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>資源群組： g QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  開始使用的 anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     開始使用的 anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>資源群組： g QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):  開始使用的 anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):     開始使用的 anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>ASCS 的探查連接埠
   sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 62000/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 62000/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3200/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3200/tcp sudo防火牆 cmd-區域 = 公用--新增連接埠 = 3600/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3600/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3900/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3900/tcp sudo防火牆 cmd-區域 = 公用--新增連接埠 = 8100/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 8100/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50013/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50013/tcp sudo防火牆 cmd-區域 = 公用--新增連接埠 = 50014/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50014/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50016/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50016/tcp
   # <a name="probe-port-of-ers"></a>ERS 的探查連接埠
   sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 62101/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 62101/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3301/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 3301/tcp sudo防火牆 cmd-區域 = 公用--新增連接埠 = 50113/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50113/tcp sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50114/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50114/tcp sudo防火牆 cmd-區域 = 公用--新增連接埠 = 50116/tcp--永久 sudo 防火牆-cmd-區域 = 公用--新增連接埠 = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>SAP NetWeaver ASCS 負載平衡器前端組態的 IP 位址
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>適用於 SAP NetWeaver ASCS ERS 負載平衡器前端組態的 IP 位址
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir-p/sapmnt/QAS sudo mkdir-p /usr/sap/trans

   sudo chattr + 我 sapmnt/QAS sudo chattr + 我 /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>將下列幾行加入至 fstab，儲存並結束
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>將下行加入至 fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>掛接
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>將下行加入至 fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>掛接
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>設定 y 屬性 ResourceDisk.EnableSwap
   # <a name="create-and-use-swapfile-on-resource-disk"></a>建立和使用資源磁碟上的分頁檔。
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>設定與屬性 ResourceDisk.SwapSizeMB 交換檔的大小
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>資源磁碟的可用空間是根據虛擬機器大小而異。 請確定您未設定為太大的值。 您可以檢查命令 swapon 使用交換空間
   # <a name="size-of-the-swapfile"></a>分頁檔的大小。
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo service waagent 重新啟動
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore 清單
   ```

   This should list all entries and should look similar to
   ```
   資料檔案： /home/qasadm/.hdb/anftstsapa01/SSFS_HDB。DAT 金鑰檔案： /home/qasadm/.hdb/anftstsapa01/SSFS_HDB。索引鍵
   
   索引鍵的預設環境：192.168.14.4:30313 使用者：SAPABAP1 資料庫：QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su-qasadm hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>移除 ers 移轉過程中發生失敗的動作
   [root@anftstsapcl1 ~] # 電腦資源清除 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl2 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl2 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   線上: [anftstsapcl1 anftstsapcl2]
   
   資源的完整清單：
   
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   
   失敗的動作：
   * rsc_sap_QAS_ERS01_monitor_11000 anftstsapcl1 上的 「 未執行 」 (7): 呼叫 = 45，狀態 = 完成 exitreason = '，
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] # 電腦資源清除 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] # 電腦資源清除 rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~] # 電腦資源清除 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl2 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl2 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # 電腦資源清除 rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~] # 電腦資源清除 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # 電腦資源清除 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    啟動 anftstsapcl1 資源群組： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 啟動 anftstsapcl1 資源群組： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  啟動 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    開始使用的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     啟動 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 開始使用的 anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
