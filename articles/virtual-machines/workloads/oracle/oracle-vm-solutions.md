---
title: Azure 虛擬機器上的 oracle 解決方案 |Microsoft Docs
description: 深入了解支援的設定和限制的 Microsoft Azure 上的 Oracle 虛擬機器映像。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 70e87a38373688c1b364a079cd07934309662e3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707427"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 映像並將其部署在 Microsoft Azure

本文章涵蓋 Azure Marketplace 中的 oracle 發行的虛擬機器映像為基礎的 Oracle 解決方案的相關資訊。 如果您想要在 Oracle 雲端基礎結構使用的跨雲端應用程式解決方案，請參閱[Oracle 應用程式解決方案，整合 Microsoft Azure 與 Oracle 雲端基礎結構](oracle-oci-overview.md)。

若要取得目前可用的映像清單，請執行下列命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019 年從下列映像則可供使用項目：

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

系統會將這些映像視為「自備授權」，因此您只會支付執行 VM 產生的計算成本、儲存成本和網路成本。  其假設您已取得使用 Oracle 軟體的適當授權，且與 Oracle 之間已擁有支援合約。 Oracle 已保證從內部部署至 Azure 的授權行動性。 如需有關授權行動性的詳細資訊，請參閱已發佈的 [Oracle 和 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (英文) 附註。 

個人也可以選擇將其在 Azure 中從頭建立的自訂映像，或從內部部署環境上傳的自訂映像作為解決方案的基礎。

## <a name="support-for-jd-edwards"></a>JD Edwards 的支援
根據 Oracle 支援附註[Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)，JD Edwards EnterpriseOne 版本 9.2 和更新版本上支援**任何公用雲端供應項目**符合其特定`Minimum Technical Requirements`(MTR)。  您必須建立符合其 OS 和軟體應用程式相容性之 MTR 規格的自訂映像。 

## <a name="oracle-database-vm-images"></a>Oracle 資料庫 VM 映像
Oracle 支援在以 Oracle Linux 為基礎的虛擬機器映像上，在 Azure 中執行 Oracle DB 12.1 Standard 和 Enterprise 版本。  若要讓 Azure 上的 Oracle DB 生產工作負載提供最佳效能，請務必適當地調整 VM 映像的大小，並使用進階儲存體支援的受控磁碟。 如需有關如何使用 Oracle 已發佈之 VM 映像在 Azure 中快速地讓 Oracle DB 啟動並執行的指示，[請嘗試 Oracle DB 快速入門逐步解說](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>連接的磁碟組態選項

連接的磁碟依賴 Azure Blob 儲存體服務。 理論上每個標準磁碟最多都能達約每秒 500 輸入/輸出作業 (IOPS)。 我們的進階磁碟供應項目十分適用於高效能資料庫工作附載，且可達到每部磁碟最多 5000 IOPS。 如果符合您效能需求，您可以使用單一磁碟。 不過，您可以改善有效的 IOPS 效能，如果您使用多個連接的磁碟、 資料庫資料分散，然後使用 Oracle Automatic Storage Management (ASM)。 如需有關 Oracle ASM 的特定詳細資訊，請參閱 [Oracle Automatic Storage 概觀](https://www.oracle.com/technetwork/database/index-100339.html) (英文)。 如需如何安裝和設定 Linux Azure VM 上的 Oracle ASM 的範例，請參閱 <<c0> [ 安裝和設定 Oracle Automatic Storage Management](configure-oracle-asm.md)教學課程。

### <a name="shared-storage-configuration-options"></a>共用存放裝置組態選項

Azure 的 NetApp 檔案被設計成符合在雲端中，執行高效能工作負載，例如資料庫中的核心需求，並提供;
- Azure 的原生共用執行 Oracle 工作負載可能是透過原生 NFS 用戶端 VM 或 Oracle dNFS NFS 儲存體服務
- 反映出真實世界範圍的 IOPS 需求的可調整的效能層級
- 低延遲
- 高可用性、 高持久性和規模，通常需要任務關鍵性企業工作負載 （例如 SAP 和 Oracle） 的管理能力
- 快速且有效率的備份和復原，來達成的最積極的 RTO 和 RPO 的 SLA

這些功能可能會因為 Azure NetApp 檔案以 Azure 資料中心環境中 – 做為 Azure 原生服務內執行的 NetApp® ONTAP® 全快閃系統為基礎。 結果是一種理想的資料庫儲存體技術，可以佈建及耗用就像其他 Azure 儲存體選項。 請參閱[Azure NetApp 檔案文件](https://docs.microsoft.com/azure/azure-netapp-files/)如需有關如何部署和存取 Azure NetApp 檔案 NFS 磁碟區。 請參閱[在 Azure 部署最佳作法指南使用 Azure NetApp 檔案上的 Oracle](https://www.netapp.com/us/media/tr-4780.pdf)的最佳作法建議操作 Azure NetApp 檔案上的 Oracle 資料庫。


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC 是用來在內部部署的多節點叢集組態中，減少發生單一節點錯誤的機率。 它依賴以下兩個內部部署技術，而這兩個技術並非大規模公用雲端環境的原生技術︰網路多點傳送和共用磁碟。 如果您的資料庫解決方案需要 Oracle RAC，在 Azure 中，您需要第三方 = 廠商軟體，才能啟用這些技術。 如需有關 Oracle RAC 的詳細資訊，請參閱 < [FlashGrid SkyCluster 頁面](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和災害復原考量
當在 Azure 中使用 Oracle 資料庫，您會負責實作高可用性和災害復原解決方案，以避免任何停機時間。 

高可用性和災害復原 Oracle Database Enterprise Edition （不需倚賴 Oracle RAC） 可以達成在 Azure 上使用[Data Guard、 Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)，或[Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)，與兩個不同的虛擬機器上的兩個資料庫。 這兩個虛擬機器應該位於相同的[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)中，以確保它們可以透過永續私人 IP 位址互相存取。  此外，建議您將虛擬機器放置於相同的可用性設定組，讓 Azure 將其放置於不同的容錯網域和升級網域。 如果您想要擁有異地備援，設定兩個不同區域之間進行複寫，並使用 VPN 閘道連接兩個執行個體的兩個資料庫。

本教學課程[在 Azure 上實作 Oracle Data Guard](configure-oracle-dataguard.md)會引導您完成基本安裝程序在 Azure 上。  

使用 Oracle Data Guard，可以藉由某個虛擬機器中的主要資料庫、另一個虛擬機器中的次要 (待命) 資料庫以及它們之間的單向複寫設定，達到高可用性。 這樣讀取作業存取的會是資料庫的複本。 使用 Oracle GoldenGate，您則可以設定兩個資料庫之間的雙向複寫。 若要了解如何使用這些工具為資料庫設定高可用性解決方案，請參閱 Oracle 網站上的 [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文件 (英文)。 如需資料庫複本的讀取-寫入存取權，您可以使用 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

本教學課程[在 Azure 上實作 Oracle GoldenGate](configure-oracle-golden-gate.md)會引導您完成基本安裝程序在 Azure 上。

除了擁有在 Azure 中建構 HA 和 DR 解決方案，您應該有可用於還原資料庫的備份策略。 本教學課程[備份及復原 Oracle 資料庫](oracle-backup-recovery.md)會引導您完成建立一致備份的基本程序。

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虛擬機器映像

* **只有在 Enterprise Edition 上才支援叢集。** 只有當您使用的是 WebLogic Server Enterprise Edition 時，才會獲授權使用 WebLogic 叢集。 請勿在使用 WebLogic Server Standard Edition 時使用叢集。
* **不支援 UDP 多點傳送。** Azure 支援 UDP 單點傳播，但不支援多點傳送或廣播。 WebLogic Server 可以依賴 Azure 的 UDP 單點傳播功能。 依賴 UDP 單點傳播獲得最佳結果，建議 WebLogic 叢集大小會保留靜態的或保留與最多 10 個受管理的伺服器。
* **WebLogic Server 預期針對 T3 存取的公用和私人連接埠是相同的 (例如，使用 Enterprise JavaBeans 時)。** 請考慮包含兩個或多個 Vm，名稱為虛擬網路中的 WebLogic Server 叢集執行的服務層 (EJB) 應用程式的多層式案例*SLWLS*。 用戶端層位於不同的子網路，在相同虛擬網路中，執行簡單的 Java 程式，嘗試呼叫服務層中的 EJB。 因為需要負載平衡服務層，就必須 WebLogic Server 叢集中的虛擬機器建立公用負載平衡的端點。 如果您指定的私人連接埠與公用連接埠不同 (例如，7006:7008)，則會發生下列錯誤：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   這是因為對於任何遠端 T3 存取，WebLogic Server 預期負載平衡器連接埠和 WebLogic 受控伺服器連接埠是相同的。 在上述情況中，用戶端正在存取連接埠 7006 (負載平衡器連接埠)，而受控伺服器正在接聽 7008 (私人連接埠)。 這項限制只適用於 T3 存取，不適用於 HTTP。

   若要避免此問題，請使用下列其中一種因應措施：

  * 針對 T3 存取專用的負載平衡端點使用相同的私人和公用連接埠號碼。
  * 啟動 WebLogic Server 時包含下列 JVM 參數：

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

如需相關資訊，請參閱位於 <https://support.oracle.com> 的 KB 文章 **860340.1**。

* **動態叢集和負載平衡限制。** 假設您想要在 WebLogic Server 中使用動態叢集，並透過 Azure 中的單一、公用負載平衡端點將它公開。 做法是只要您針對每個受管理的伺服器 （從範圍動態指派） 中使用固定通訊埠編號，而且不會啟動更受管理的伺服器多於可用的系統管理員正在追蹤之機器。 也就是沒有一個受管理的伺服器，每個虛擬機器）。 如果您的設定會導致啟動比虛擬機器的多個 WebLogic server （亦即，其中多個 WebLogic Server 執行個體共用相同的虛擬機器），則不可能是一個以上的 WebLogic 伺服器執行個體繫結至指定的連接埠號碼。 該虛擬機器上的其他失敗。

   若您設定管理伺服器自動將唯一的連接埠號碼指派給其受控伺服器，則無法進行負載平衡，因為 Azure 不支援從單一公用連接埠對應至多個私人連接埠，而此設定中需要這樣做。
* **多個虛擬機器上的 WebLogic Server 的執行個體。** 根據您的部署需求，您可以考慮在相同的虛擬機器上執行多個 WebLogic Server 執行個體如果虛擬機器夠大。 例如，在中型大小虛擬機器上 (包含 2 個核心)，您可以選擇執行兩個 WebLogic Server 的執行個體。 不過，我們仍建議您避免單一失敗點引入您的架構，會發生這個狀況，如果您使用只有一個執行多個 WebLogic Server 執行個體的虛擬機器。 使用至少兩部虛擬機器，可能是較好的方法，以及每個虛擬機器可以執行多個 WebLogic Server 執行個體。 每個 WebLogic Server 執行個體可能還會在相同叢集的一部分。 不過，它目前不是能夠使用 Azure 進行相同的虛擬機器內此類 WebLogic Server 部署所公開的負載平衡端點，因為 Azure 負載平衡器需要負載平衡的伺服器分散至唯一虛擬機器。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虛擬機器映像
* **JDK 6 和 7 最新更新。** 雖然建議使用 Java 的最新公開支援版本 (目前為 Java 8)，Azure 也提供 JDK 6 和 7 映像。 這是針對尚未準備升級至 JDK 8 的舊版應用程式。 雖然舊版 JDK 映像的更新可能不再提供一般大眾使用，憑藉 Microsoft 與 Oracle 的合作夥伴關係，Azure 提供的 JDK 6 和 7 映像會包含最新的非公用更新，該更新通常是由 Oracle 僅提供給 Oracle 支援客戶的選取群組。 新版本的 JDK 映像與 JDK 6 和 7 的更新版本會在一段時間內供取用。

   JDK 6 和 7 映像，和虛擬機器及從它們衍生的映像中可用的 JDK 僅能在 Azure 中使用。
* **64 位元 JDK。** Oracle WebLogic Server 虛擬機器映像和 Azure 所提供的 Oracle JDK 虛擬機器映像包含 Windows Server 和 JDK 的 64 位元版本。

## <a name="next-steps"></a>後續步驟
您現在有目前在 Microsoft Azure 中的虛擬機器映像為基礎的 Oracle 解決方案的概觀。 下一步是將第一個 Oracle 資料庫在 Azure 上的部署。

> [!div class="nextstepaction"]
> [在 Azure 上建立 Oracle 資料庫](oracle-database-quick-create.md)
