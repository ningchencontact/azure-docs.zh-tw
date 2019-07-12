---
title: 設定 Azure 虛擬機器 (Vm) 上 RHEL 上的 IBM Db2 HADR |Microsoft Docs
description: 建立 Azure 虛擬機器 (Vm) RHEL 上的 IBM Db2 LUW 高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812125"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>在 Red Hat Enterprise Linux Server 上的 Azure Vm 上的 IBM Db2 LUW 的高可用性

適用於 Linux、 UNIX 和 Windows (LUW) 中的 IBM Db2[高可用性和災害復原 (HADR) 組態](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)一個執行的主要資料庫執行個體的節點和至少一個執行的次要資料庫執行個體的節點所組成。 主要資料庫執行個體的變更會複寫到次要資料庫執行個體同步或非同步的方式，根據您的設定。 

本文說明如何部署和設定 Azure 虛擬機器 (Vm)、 安裝叢集架構，並安裝 IBM Db2 LUW HADR 組態。 

本文不討論如何安裝和設定 IBM Db2 LUW HADR 」 或 「 SAP 軟體安裝。 為了協助您完成這些工作，我們會提供 SAP 和 IBM 安裝手冊的參考。 本文著重於 Azure 環境特有的組件。 

支援的 IBM Db2 版本為 10.5 和更新版本，如記載於 SAP 附註[1928533]。

在開始安裝之前，請參閱下列 SAP 附註和文件：

| SAP 附註 | 描述 |
| --- | --- |
| [1928533] | 在 Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型 |
| [2015553] | 在 Azure 上的 SAP:支援的必要條件 |
| [2178632] | 主要監視度量適用於 SAP on Azure |
| [2191498] | Linux 上搭配 Azure 的 SAP：增強型監視 |
| [2243692] | 在 Azure (IaaS) VM 上的 Linux:SAP 授權問題 |
| [2002167] | Red Hat Enterprise Linux 7.x：安裝與升級 |
| [2694118] | Red Hat Enterprise Linux HA 附加元件在 Azure 上 |
| [1999351] | 對適用於 SAP 的增強型 Azure 監視功能進行疑難排解 |
| [2233094] | DB6：SAP 應用程式在 Azure 上使用 IBM Db2 for Linux、 UNIX 和 Windows-其他資訊 |
| [1612105] | DB6：Db2 與 HADR 的常見問題集 |


| 文件 | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes):適用於 Linux 都具有所有必要 SAP 附註 |
| [Azure 虛擬機器規劃和實作適用於 SAP on Linux][planning-guide]指南 |
| [適用於 SAP on Linux 的 azure 虛擬機器部署][deployment-guide]（本文） |
| [Azure 虛擬機器資料庫管理 system(DBMS) 部署適用於 SAP on Linux][dbms-guide]指南 |
| [在 Azure 的規劃與部署檢查清單上的 SAP 工作負載][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7 的高可用性附加元件的概觀][rhel-ha-addon] |
| [高可用性附加元件管理][rhel-ha-admin] |
| [高可用性附加元件參考][rhel-ha-ref] |
| [RHEL 高可用性叢集的支援原則：以 Microsoft Azure 虛擬機器作為叢集成員][rhel-azr-supp]
| [在 Microsoft Azure 上安裝和設定 Red Hat Enterprise Linux 7.4 (和更新版本) 高可用性叢集][rhel-azr-inst]
| [針對 SAP 工作負載的 IBM Db2 Azure 虛擬機器 DBMS 部署][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [支援 RHEL 高可用性叢集的 IBM Db2 for Linux，Unix，and Windows 叢集中的管理原則][rhel-db2-supp]



## <a name="overview"></a>總覽
為了達到高可用性，至少兩個 Azure 虛擬機器上，它們會部署在安裝與 HADR 的 IBM Db2 LUW [Azure 可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中或跨[Azure 可用性區域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

下圖顯示兩部資料庫伺服器的 Azure Vm 的設定。 這兩個資料庫伺服器的 Azure Vm 有自己的儲存體附加和啟動並執行。 在 HADR，一個資料庫執行個體中的其中一個 Azure Vm 會有主要執行個體的角色。 所有用戶端會連線到主要執行個體。 在 Db2 交易記錄會保存在本機資料庫交易中的所有變更。 因為交易記錄檔記錄會保存在本機，記錄會傳輸透過 TCP/IP 的資料庫執行個體上的第二個的資料庫伺服器、 待命伺服器或待命執行個體。 待命執行個體更新本機資料庫向前復原資料庫傳送的交易記錄檔記錄。 如此一來，待命伺服器都會保持與主要伺服器同步。

HADR 是只複寫功能。 它有任何失敗偵測和自動接管或容錯移轉功能可用。 接管或傳輸到待命伺服器必須手動起始的資料庫管理員。 若要達到自動接管以及失敗偵測，您可以使用 Linux Pacemaker 叢集功能。 Pacemaker 會監視這兩個資料庫的伺服器執行個體。 當主要資料庫的伺服器執行個體損毀，Pacemaker 啟始*自動*HADR 接管待命伺服器。 Pacemaker 也可確保虛擬 IP 位址，都會指派給新的主要伺服器。

![IBM Db2 高可用性概觀](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

若要將 SAP 應用程式伺服器連接到主要資料庫，您必須是虛擬的主機名稱和虛擬 IP 位址。 發生容錯移轉時，SAP 應用程式伺服器會連接到新的主要資料庫執行個體。 在 Azure 環境中， [Azure 負載平衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)才能具有所需的 HADR 的 IBM Db2 的方式使用的虛擬 IP 位址。 

為了協助您全面了解如何使用 HADR 和 Pacemaker 的 IBM Db2 LUW 融入的高可用性的 SAP 系統設定下, 圖顯示 IBM Db2 資料庫為基礎的 SAP 系統的高可用性安裝的概觀。 本文涵蓋僅 IBM Db2，但它提供有關如何設定 SAP 系統的其他元件的其他文件的參考。

![IBM DB2 高可用性的完整的環境概觀](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步驟的高階概觀
若要部署的 IBM Db2 組態，您需要遵循下列步驟：

  + 規劃您的環境。
  + 部署 Vm。
  + 更新 RHEL Linux，並設定檔案系統。
  + 安裝和設定 Pacemaker。
  + 安裝程式[glusterfs 叢集][glusterfs] or [Azure NetApp Files][anf-rhel]
  + 安裝[不同的叢集上的 ASCS/ERS][ascs-ha-rhel]。
  + 使用分散式/高可用性選項 (SWPM) 安裝 IBM Db2 資料庫。
  + 安裝和建立次要資料庫節點，然後執行個體，並設定 HADR。
  + 請確認正在 HADR。
  + Pacemaker 將組態套用至控制項 IBM Db2。
  + 設定 Azure 負載平衡器。
  + 主要的安裝和對話方塊應用程式伺服器。
  + 請檢查並調整 SAP 應用程式伺服器的組態。
  + 執行容錯移轉和接管的測試。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>規劃 Azure 基礎結構裝載具有 HADR 的 IBM Db2 LUW

執行部署之前，請完成規劃的程序。 規劃建立部署的 Db2 與在 Azure 中的 HADR 組態的基礎。 下表列出必須在規劃 IMB Db2 LUW （資料庫 SAP 環境的一部分） 的索引鍵項目：

| 主題 | 簡短說明 |
| --- | --- |
| 定義 Azure 資源群組 | 您可在此部署 VM、 VNet、 Azure Load Balancer 和其他資源的資源群組。 可以是現有或新。 |
| 虛擬網路 / 子網路定義 | 正在部署所在的 Vm 以 IBM Db2 和 Azure Load Balancer。 可以是現有或新建立。 |
| 虛擬機器裝載 IBM Db2 LUW | VM 大小、 儲存體、 網路、 IP 位址。 |
| 虛擬的主機名稱和 IBM Db2 資料庫的虛擬 IP| 虛擬 IP 或主機名稱，用於 SAP 應用程式伺服器的連接。 **db-virt-hostname**， **db virt ip**。 |
| Azure 的隔離 | 防止方法，以避免分割核心分裂情況。 |
| Azure Load Balancer | 使用方式的 Basic 或 Standard （建議選項），探查連接埠的 Db2 資料庫 （建議 62500）**探查連接埠**。 |
| 名稱解析| 名稱解析的運作方式在環境中。 強烈建議您 DNS 服務。 可以使用本機主機檔案。 |
    
如需有關在 Azure 中的 Linux Pacemaker 的詳細資訊，請參閱[於 Azure 中的 Red Hat Enterprise Linux 上設定 pacemaker][rhel-pcs-azr]。

## <a name="deployment-on-red-hat-enterprise-linux"></a>在 Red Hat Enterprise Linux 上的部署

IBM Db2 LUW 的資源代理程式會包含在 Red Hat Enterprise Linux Server HA 附加元件。 這份文件中所述的安裝程式，您應該使用 Red Hat Enterprise Linux for SAP。 Azure Marketplace 包含 Red Hat Enterprise Linux 7.4 適用於 SAP 或更高版本可供您部署新的 Azure 虛擬機器映像。 請注意，當您選擇在 Azure VM 市集中的 VM 映像，透過 Azure Marketplace 的 Red hat 提供各種支援或服務模型。

### <a name="hosts-dns-updates"></a>主機：DNS 更新
所有的主機名稱，包括虛擬的主機名稱的清單，並更新您的 DNS 伺服器，以啟用適當的 IP 位址的主機名稱解析。 如果 DNS 伺服器不存在或您不能更新，並建立 DNS 項目，您需要使用在此案例中參與的個別 vm 的本機主機檔案。 如果您使用主機檔案項目，請確定項目會套用至 SAP 系統環境中的所有 Vm。 不過，我們建議您先使用您的 DNS，在理想情況下，擴充至 Azure


### <a name="manual-deployment"></a>手動部署

請確定所選的 OS IBM/sap 支援的 IBM Db2 LUW。 Azure Vm 和 Db2 版本支援的 OS 版本的清單位於 SAP 附註[1928533]。 個別的 Db2 版本的作業系統版本的清單位於 SAP 產品可用性對照表。 我們強烈建議最少的 Red Hat Enterprise Linux 7.4 適用於 SAP 因為在此或更新版本的 Azure 相關的效能改善 Red Hat Enterprise Linux 版本。

1. 建立或選取資源群組。
1. 建立或選取虛擬網路和子網路。
1. 建立 Azure 可用性設定組，或部署在可用性區域。
    + 可用性設定組中，設定為 2 的最大的更新網域。
1. 建立虛擬機器 1。
    + 使用 Red Hat Enterprise Linux for SAP 映像，在 Azure Marketplace 中。
    + 選取您在步驟 3 中建立 Azure 可用性設定組，或選取可用性區域。
1.  建立虛擬機器 2。
    + 使用 Red Hat Enterprise Linux for SAP 映像，在 Azure Marketplace 中。
    + 選取 Azure 可用性設定組中步驟 3 中建立或選取可用性區域 （未如步驟 3 所示的相同區域）。
1. 將資料磁碟新增至 Vm，，然後檢查 文件中的檔案系統設定的建議[針對 SAP 工作負載的 IBM Db2 Azure 虛擬機器 DBMS 部署][dbms-db2]。

## <a name="create-the-pacemaker-cluster"></a>建立 Pacemaker 叢集
    
若要建立此 IBM Db2 伺服器的基本 Pacemaker 叢集，請參閱 [於 Azure 中的 Red Hat Enterprise Linux 上設定 pacemaker][rhel-pcs-azr]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安裝 IBM Db2 LUW 和 SAP 環境

開始在 IBM Db2 LUW 上 SAP 環境的安裝之前，請檢閱下列文件：

+ Azure 文件
+ SAP 文件
+ IBM 文件

在這篇文章簡介部分會提供這份文件的連結。

檢查有關安裝 NetWeaver 型應用程式在 IBM Db2 LUW 上的 SAP 安裝手冊。
您可以使用，以尋找 SAP 技術入口網站上的指南[SAP 安裝指南 Finder][sap-instfind]。

您可以減少的輔助線顯示在入口網站中設定下列篩選條件：
- 我想要：[安裝新的系統]
- 我的資料庫：「 IBM Db2 for Linux，Unix，and Windows 」
- 適用於 SAP NetWeaver 版本、 堆疊組態或作業系統的其他篩選器

#### <a name="red-hat-firewall-rules"></a>Red Hat 的防火牆規則
Red Hat Enterprise Linux 已預設啟用的防火牆。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>設定 IBM Db2 LUW 與 HADR 的安裝提示

若要設定主要的 IBM Db2 LUW 資料庫執行個體：

- 使用高可用性或分散式的選項。
- 安裝 SAP ASCS/ERS 和資料庫執行個體。
- 建立新安裝的資料庫的備份。

> [!IMPORTANT] 
> 記下 「 資料庫通訊連接埠 」 是在安裝期間設定。 它必須是相同的連接埠號碼，這兩個資料庫執行個體。
>![SAP SWPM 連接埠定義](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>適用於 Azure 的 IBM Db2 HADR 設定

   當您使用 Azure Pacemaker 柵欄代理程式時，設定下列參數：

   - HADR 對等的持續期間 （秒） (HADR_PEER_WINDOW) = 240  
   - HADR 逾時值 (HADR_TIMEOUT) = 45

我們建議您根據初始容錯移轉/接管測試先前的參數。 它是強制您在使用這些參數設定測試容錯移轉和接管的適當功能。 個別的組態可能會不同，因為參數可能需要調整。 

> [!NOTE]
> IBM Db2 與正常啟動 HADR 組態特有：次要或待命資料庫執行個體必須先啟動並執行您可以啟動主要資料庫執行個體。

   
> [!NOTE]
> 進行安裝和設定的特定 Azure 和 Pacemaker:透過 SAP Software Provisioning Manager 在安裝程序，還有一個明確的問題，相關的 IBM Db2 LUW 高可用性：
>+ 請勿選取**IBM Db2 pureScale**。
>+ 請勿選取**安裝 IBM Tivoli 系統的自動化 Multiplatforms**。
>+ 請勿選取**產生叢集設定檔**。
>![SAP SWPM-DB2 HA 選項](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


若要設定待命資料庫伺服器使用的 SAP 同質性系統複製程序，執行下列步驟：

1. 選取 **系統複製**選項 >**目標系統** > **分散式** > **資料庫執行個體**。
1. 為複製方法，並選取**同質系統**，讓您可以使用備份來還原待命伺服器執行個體上的備份。
1. 當您到達結束步驟，以還原同質性系統複製的資料庫時，請結束安裝程式。 從備份還原資料庫的主要主機。 已在主要資料庫伺服器上執行所有的後續安裝階段。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat 為 DB2 HADR 的防火牆規則
要加入防火牆規則允許至 DB2 和 DB2 for HADR 工作之間的流量：
+ 資料庫通訊的連接埠。 如果使用資料分割，也新增這些連接埠。
+ HADR 連接埠 （DB2 參數 HADR_LOCAL_SVC 值）
+ Azure 的探查連接埠
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 核取
針對示範用途，這篇文章中所述的程序，資料庫 SID 是**ID2**。

您已設定 HADR 且狀態會在主要和待命節點上為 「 對等，並已連線之後，請執行下列檢查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 組態

當您使用自動容錯移轉的 Pacemaker 發生節點失敗時，您需要據以設定您的 Db2 執行個體和 Pacemaker。 本章節描述這種組態。

下列項目會使用前置詞：

- **[A]** ：適用於所有節點
- **[1]** ：只適用於節點 1 
- **[2]** ：只適用於節點 2

**[A]** Pacemaker 組態的必要條件：
1. 關閉這兩部資料庫伺服器，使用者 db2\<sid > db2stop 使用。
1. 變更 shell 環境的 db2\<sid > 使用者 */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker 組態

**[1]** IBM Db2 HADR 專屬 Pacemaker 組態：
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** 建立 IBM Db2 資源：
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** 啟動 IBM Db2 資源：
* Put Pacemaker 離開維護模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** 確定叢集狀態正常，而且會啟動的所有資源。 它並不重要的資源執行哪一個節點。
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

線上: [az idb01 az-idb02]

資源的完整清單：

 rsc_st_azure   (stonith:fence_azure_arm):    開始使用的 az idb01： 主要/附屬組：Db2_HADR_ID2 主要 [Db2_HADR_ID2] 母片: [az idb01] 建立從屬節點: [az idb02] 資源群組： g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):     啟動 az idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb):    開始使用的 az idb01

精靈的狀態： corosync： 作用中/已停用 pacemaker： 作用中/已停用 pcsd： 作用中/已啟用
</pre>

> [!IMPORTANT]
> 您必須管理 Pacemaker 叢集使用 Pacemaker 工具的 Db2 執行個體。 如果您使用 db2 命令，例如 db2stop，Pacemaker 會偵測到資源為失敗的動作。 如果您要執行維護，您可以將節點或資源放入維護模式。 Pacemaker 會暫止監視的資源，然後您可以使用一般的 db2 系統管理命令。


### <a name="configure-azure-load-balancer"></a>設定 Azure 負載平衡器
若要設定 Azure 負載平衡器，我們建議您使用[Azure 標準 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)然後執行下列作業：

1. 建立前端 IP 集區：

   a. 在 Azure 入口網站中，開啟 Azure 負載平衡器，選取**前端 IP 集區**，然後選取**新增**。

   b. 輸入新前端 IP 集區的名稱 (例如**Db2 連接**)。

   c. 設定**指派**要**靜態**，並輸入 IP 位址**虛擬 IP**開頭定義。

   d. 選取 [確定]  。

   e. 建立新的前端 IP 集區之後，請記下集區 IP 位址。

1. 建立後端集區：

   a. 在 Azure 入口網站中，開啟 Azure 負載平衡器，選取**後端集區**，然後選取**新增**。

   b. 輸入新的後端集區的名稱 (例如**Db2 後端**)。

   c. 選取 [新增虛擬機器]  。

   d. 選取可用性設定組或虛擬機器裝載在上一個步驟中建立的 IBM Db2 資料庫。

   e. 選取的 IBM Db2 叢集中的虛擬機器。

   f. 選取 [確定]  。

1. 建立健康狀態探查：

   a. 在 Azure 入口網站中，開啟 Azure 負載平衡器，選取**健康狀態探查**，然後選取**新增**。

   b. 輸入新的健全狀況探查的名稱 (例如**Db2 hp**)。

   c. 選取  **TCP**做為通訊協定和連接埠**62500**。 保持**間隔**值設定為**5**，並保留**狀況不良閾值**值設定為**2**。

   d. 選取 [確定]  。

1. 建立負載平衡規則：

   a. 在 Azure 入口網站中，開啟 Azure 負載平衡器，選取**負載平衡規則**，然後選取**新增**。

   b. 輸入新負載平衡器規則的名稱 (例如**Db2 SID**)。

   c. 選取的前端 IP 位址、 後端集區中和您稍早建立的健康情況探查 (例如**Db2 前端**)。

   d. 保持**通訊協定**設為**TCP**，並輸入連接埠*資料庫通訊的連接埠*。

   e. 將 [閒置逾時]  增加為 30 分鐘。

   f. 務必**啟用浮動 IP**。

   g. 選取 [確定]  。

**[A]** 供探查連接埠新增防火牆規則：
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>變更用於連線的虛擬 IP 的 SAP 設定檔
若要連接到主要執行個體的 HADR 組態中，將 SAP 應用程式層必須使用您所定義和設定 Azure 負載平衡器虛擬 IP 位址。 下列變更是必要的：

/sapmnt/\<SID >/設定檔/預設值。PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>主要的安裝和對話方塊應用程式伺服器

當您安裝主要，針對 Db2 HADR 組態對話方塊應用程式伺服器，使用虛擬主機名稱，您會選擇組態。 

如果您執行安裝之前您必須建立 Db2 HADR 組態，請如所述上, 一節和 SAP Java 堆疊，如下所示的變更。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 或 Java 堆疊系統 JDBC URL 檢查

您可以使用 J2EE 組態工具來檢查或更新 JDBC URL。 由於 J2EE 組態工具是圖形化工具，您需要有 X 伺服器安裝：
 
1. 登入主要的應用程式伺服器的 J2EE 執行個體，並執行：
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. 在左框架內，選擇**安全性存放區**。
1. 在右側框架中，選擇 索引鍵的 jdbc/集區 / \<SAPSID>含 /url。
1. 將 JDBC URL 中的主機名稱變更為 虛擬主機名稱。
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. 選取 **新增**。
1. 若要儲存您的變更，請選取磁碟圖示左上方。
1. 關閉組態工具。
1. 重新啟動的 Java 執行個體。

## <a name="configure-log-archiving-for-hadr-setup"></a>設定記錄封存 HADR 安裝程式
若要設定 Db2 記錄封存 HADR 安裝程式，我們建議您設定主要和待命資料庫，能夠從所有的記錄檔封存位置的自動記錄檔擷取功能。 主要和待命資料庫必須能夠從以資料庫的其中一個執行個體可能會封存記錄檔的所有記錄檔封存位置擷取記錄檔封存檔案。 

記錄封存是只有執行主要資料庫。 如果您變更 HADR 的資料庫伺服器角色，或如果發生失敗，新的主要資料庫會負責記錄封存。 如果您已設定多個記錄檔封存位置，可能會重複封存您的記錄檔。 發生本機或遠端的更新，您可能也必須手動將從舊的主要伺服器的已封存的記錄檔複製到新的主要伺服器的作用中的記錄檔位置。

我們建議您設定一般的 NFS 共用 或 GlusterFS，從這兩個節點寫入記錄檔。 在 NFS 共用或 GlusterFS 才能提供高可用性。 

您可以使用現有的高可用性 NFS 共用或 GlusterFS 傳輸或設定檔目錄。 如需詳細資訊，請參閱：

- [Red Hat Enterprise Linux for SAP NetWeaver 上的 GlusterFS on Azure VM][glusterfs] 
- [在 Red Hat Enterprise Linux for SAP 應用程式的 Azure NetApp 檔案上的 Azure Vm 上的 SAP NetWeaver 的高可用性][anf-rhel]
- [Azure 的 NetApp 檔案](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（若要建立 NFS 共用）

## <a name="test-the-cluster-setup"></a>測試叢集設定

本節說明如何測試您的 Db2 HADR 安裝程式。 每個測試都假設 IBM Db2 主要所在*az idb01*虛擬機器。 您必須使用具有 sudo 權限或 （不建議使用） 的根使用者。

這裡所說明的所有測試案例的初始狀態: （crm_mon-r 或電腦狀態）

- **電腦狀態**是在執行階段的 Pacemaker 狀態的快照集 
- **crm_mon r** Pacemaker 狀態的連續輸出

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP 系統中的原始狀態已記錄在交易 DBACOCKPIT > 設定 > 概觀，如下圖所示：

![DBACockpit - Pre Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>測試曾經背叛的 IBM Db2


> [!IMPORTANT] 
> 開始測試之前，請確定：
> * Pacemaker 沒有任何失敗的動作 （電腦狀態）。
> * 沒有位置條件約束 （剩餘的移轉測試）
> * IBM Db2 HADR 同步處理運作正常。 檢查使用者 db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


移轉的節點正在執行主要的 Db2 資料庫執行下列命令：
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

移轉完成之後，crm 狀態輸出看起來像：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP 系統中的原始狀態已記錄在交易 DBACOCKPIT > 設定 > 概觀，如下圖所示：

![DBACockpit - Post Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

使用 「 電腦資源移動 」 移轉的資源建立位置條件約束。 位置條件約束在此情況下，無法執行 az idb01 上的 IBM Db2 執行個體。 位置條件約束不會刪除，如果資源無法容錯回復。

移除位置限制和待命節點將會啟動在 az idb01 上。
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
和叢集狀態會變更為：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit-已移除的位置限制](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


移轉資源回到*az idb01*清除位置條件約束
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **電腦資源移動\<res_name > <host>:** 建立位置條件約束，並可能會造成問題，以接管
- **電腦資源清除\<res_name >** :清除位置條件約束
- **電腦資源清除\<res_name >** :清除資源的所有錯誤

### <a name="test-a-manual-takeover"></a>測試手動接管

您可以停止上的 Pacemaker 服務來測試手動接管*az idb01*節點：
<pre><code>systemctl stop pacemaker</code></pre>

status on *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

容錯移轉之後，您可以啟動上的服務再次*az idb01*。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>終止執行 HADR 主要資料庫的節點上的 Db2 程序

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 執行個體將會失敗，且 Pacemaker 將移動主要節點和下列狀態的報表：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker 會重新啟動 Db2 主要資料庫執行個體的相同節點上，或它將會容錯移轉到執行次要資料庫執行個體的節點，則會報告錯誤。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>終止執行次要資料庫執行個體的節點上的 Db2 程序

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

節點取得 into 失敗，所述，並回報錯誤
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 執行個體重新啟動它之前，必須指派次要角色中。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>透過 db2stop 強制執行 HADR 主要資料庫執行個體的節點上停止 DB

為使用者 db2\<sid > 執行命令 db2stop 強制：
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

偵測到的失敗：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR 次要資料庫執行個體已升級成主要角色。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>執行"halt"HADR 主要資料庫執行個體的 VM 會損毀

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

在此情況下，Pacemaker 會偵測到正在執行主要資料庫執行個體的節點沒有回應。

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

下一步是檢查*Split brain*情況。 上次執行的主要資料庫執行個體的節點已關閉判定存留的節點之後，會執行資源的容錯移轉。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


發生核心異常、 失敗的節點都將 restared 柵欄代理程式。 失敗的節點恢復上線後，您必須啟動 pacemaker 叢集
<pre><code>sudo pcs cluster start</code></pre> 這會啟動成次要角色的 Db2 執行個體。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>後續步驟
- [SAP NetWeaver 的高可用性架構和案例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [於 在 Azure 中的 Red Hat Enterprise Linux 上設定 pacemaker][rhel-pcs-azr]
