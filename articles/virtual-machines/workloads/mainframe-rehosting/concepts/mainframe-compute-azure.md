---
title: 將大型主機計算移至 Azure 虛擬機器
description: Azure 計算資源比較有利至大型主機的容量，因此您可以移轉，並將 IBM z14 應用程式現代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896300"
---
# <a name="move-mainframe-compute-to-azure"></a>將大型主機計算移至 Azure

大型主機有聲譽優良的高可靠性和可用性，而且會持續為許多企業的受信任的骨幹。 他們通常會認為有幾乎無限的延展性和計算能力以及。 不過，有些企業有從前的最大的可用主機的功能。 如果這聽起來像您一樣，Azure 會提供靈活度、 存取範圍及基礎結構的節省量。

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道您的大型主機如何計算至 Azure 的功能比較。 根據 IBM z14 主機 （撰寫本文時最新的模型），這篇文章會告訴您如何在 Azure 上取得可比較的結果。

若要開始，請考慮環境並存。 下圖會比較執行至 Azure 的主控環境的應用程式的大型主機環境。

![Azure 服務與模擬環境的供應項目比較支援，並且簡化移轉](media/mainframe-compute-azure.png)

大型主機的電源通常用於線上交易處理 (OLTP) 系統來處理數以百萬計的上千名使用者的更新。 這些應用程式通常會使用軟體來交易處理、 畫面處理和表單項目。 他們可能會使用客戶資訊控制系統 (CICS)、 資訊管理系統 (IMS) 或交易介面套件 (TIP)。

如圖所示，在 Azure 上的 TPM 模擬器可處理 CICS 與 IMS 工作負載。 在 Azure 上的批次系統模擬器執行的工作控制語言 (JCL) 的角色。 大型主機資料移轉至 Azure 的資料庫，例如 Azure SQL Database 的資料庫。 Azure 服務或其他託管在 Azure 虛擬機器的軟體都可以用於系統管理。

## <a name="mainframe-compute-at-a-glance"></a>一眼的大型主機計算

在 z14 大型主機處理器會排列在最多四個*抽屜*。 A*隱藏式選單*是只需處理器和晶片的叢集。 每個隱藏式選單可以擁有 6 個作用中的中央處理器 (CP) 晶片，而且每個 CP 10 的系統控制站 (SC) 晶片。 在 Intel x86 術語中，有每個隱藏式選單、 10 個核心，每個通訊端，以及四個抽屜的六個通訊端。 此架構提供 24 通訊端與 240 個核心，最大值、 針對 z14 大致相同。

快速 z14 CP 具有 5.2 GHz 時脈速度。 一般而言，z14 被傳送以在方塊中的所有 CPs。 視需要會啟動這些項目。 客戶通常必須支付至少四個小時的每個月，儘管實際使用量的計算時間。

大型主機處理器可以設定為下列類型之一：

- 一般用途 (GP) 處理器
- 整合式資訊處理器系統 z (zIIP)
- Linux (IFL) 處理器的整合式的功能
- 系統協助處理器 (SAP)
- 整合式結合設備 (ICF) 處理器

## <a name="scaling-mainframe-compute-up-and-out"></a>調整大型主機計算增加和相應放大

IBM 大型主機提供調整達 240 個核心 （目前 z14 大小的單一系統） 的能力。 此外，IBM 大型主機可以透過稱為 「 結合的功能 (CF) 功能相應放大。 CF 可讓多個大型主機系統可以同時存取相同的資料。 使用 CF，大型主機平行 Sysplex 技術群組大型主機的處理器在叢集中。 本指南已寫入時，平行 Sysplex 功能支援 64 個處理器的 32 的分組。 最多 2,048 處理器可以組成群組，以這種方式相應放大計算容量。

CF 允許直接存取的共用資料的計算叢集。 它用於鎖定的資訊、 快取的詳細資訊和共用的資料的資源清單。 使用一或多個 CFs 平行 Sysplex 可以視為 「 共用的所有項目 」 向外延展計算叢集。 如需這些功能的詳細資訊，請參閱[IBM Z 上平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) IBM 網站上。

應用程式可以使用這些功能，來提供向外延展效能和高可用性。 如需 CICS 如何使用平行 Sysplex CF 的資訊，請下載[IBM CICS 和結合的功能：基本概念以外](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)redbook。

## <a name="azure-compute-at-a-glance"></a>一眼的 azure 計算

有些人不小心將 Intel 型伺服器不是與大型主機一樣強大。 不過，新的核心密集、 intel 系統有做為許多計算為大型主機的容量。 本節說明適用於運算和儲存體的 Azure 基礎結構做為-即服務 (IaaS) 選項。 Azure 提供平台為-即服務 (PaaS) 選項，但本文著重於提供可比較的大型主機的容量的 IaaS 選擇。

Azure 虛擬機器提供大小和類型的範圍中的計算能力。 在 Azure 中，虛擬 CPU (vCPU) 大致上相當於大型主機上的核心。

目前，Azure 虛擬機器的範圍大小提供從 1 到 128 個 Vcpu。 虛擬機器 (VM) 類型會針對特定工作負載最佳化。 例如，下列清單顯示 （撰寫本文時目前） 的 VM 類型和其建議的用法：

| 大小     | 類型和描述                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 系列 | 一般用途包含 64 個 vCPU 和高達 3.5 GHz 的時脈速度                           |
| E 系列 | 記憶體最佳化的最多 64 個 Vcpu                                                 |
| F 系列 | 計算具有最多 64 個 Vcpu 和 3..7 GHz 時脈速度最佳化                       |
| H 系列 | 適用於高效能運算 (HPC) 應用程式                          |
| L 系列 | 適用於高輸送量應用程式，例如 NoSQL 資料庫所支援的儲存體 |
| M 系列 | 最大運算和記憶體最佳化的 Vm，具有最多可有 128 個 Vcpu                        |

如需可用 Vm 的詳細資訊，請參閱[虛擬機器系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。

Z14 大型主機可以有最多 240 個核心。 不過，z14 大型主機幾乎不會使用所有核心的單一應用程式或工作負載。 相反地，大型主機隔離工作負載分成邏輯資料分割 (LPARs)，還有 LPARs 評等-MIPS （數百萬計的指示執行每秒） 或 MSU （百萬個服務單位）。 判斷比較 Azure，納入 MIPS （或 MSU） 評等上執行的大型主機工作負載所需的 VM 大小。

以下是一般的估計值：

-   每一 vCPU 的 150 MIPS

-   每個處理器的 1,000 MIPS

若要判斷正確的 VM 大小，LPAR 中指定的工作負載，第一次最佳化工作負載的 VM。 然後判斷所需的 Vcpu 數目。 保守的估計是 150 MIPS 每個 vCPU。 根據這項估計，比方說，16 個 Vcpu 的 F 系列 VM 可以輕鬆地支援來自與 2,400 MIPS LPAR 的 IBM Db2 工作負載。

## <a name="azure-compute-scale-up"></a>Azure 計算相應增加

M 系列 Vm 可以調整最多可有 128 個 Vcpu （在撰寫本文時的時間）。 M 系列 VM 使用 150 MIPS 保守估計，每個 vCPU，等於約 19,000 MIPS。 估計 MIPS 的大型主機的一般規則是 1,000 MIPS 每個處理器。 Z14 大型主機可以有最多 24 個處理器，並提供單一的大型主機系統即將實付 24,000 MIPS。

最大的單一 z14 大型主機已超過最大的 VM 在 Azure 中可用的大約 5,000 MIPS。 還務必比較部署工作負載的方式。 如果大型主機系統有應用程式和關聯式資料庫，它們通常部署在相同實體的大型主機上 — 各自位於自己 LPAR。 在 Azure 上相同的方案通常會部署應用程式和資料庫的個別的適當大小的 VM 使用一部 VM。

例如，如果 M64 vCPU 系統支援應用程式，並且 M96 vCPU 資料庫，則需要大約 150 個 Vcpu，或大約實付 24,000 MIPS 如下圖所示。

![比較工作負載部署的實付 24,000 MIPS](media/mainframe-compute-mips.png)

方法是將 LPARs 移轉到個別的 Vm。 然後 Azure 輕鬆地相應增加以部署單一大型主機系統的大部分應用程式所需的大小。

## <a name="azure-compute-scale-out"></a>Azure 計算向外延展

Azure 為基礎的優點之一是解決方案的向外延展的能力。幾乎無限的縮放比例會計算可為應用程式的容量。 Azure 支援相應放大計算能力的多個方法：

- **跨負載平衡叢集。** 在此案例中，應用程式可以使用[負載平衡器](/azure/load-balancer/load-balancer-overview)或 resource manager，以便分散在叢集中的多個 Vm 之間的工作負載。 如果計算需要的容量，額外的 Vm 會加入至叢集。

- **虛擬機器擴展集。** 在此案例，應用程式可以調整至其他[計算資源](/azure/virtual-machine-scale-sets/overview)VM 使用量為基礎。 當需求下降時，在擴展集中的 Vm 數目也可以移往下，確保有效率地使用的計算能力。

- **調整的 PaaS。** Azure PaaS 供應項目調整計算資源。 例如， [Azure Service Fabric](/azure/service-fabric/service-fabric-overview)會配置計算資源來滿足增加的要求數量。

- **Kubernetes 叢集。** 在 Azure 上的應用程式可以使用[Kubernetes 叢集](/azure/aks/concepts-clusters-workloads)指定資源的計算服務。 Azure Kubernetes Service (AKS) 是一個受控的服務，可協調 Kubernetes 節點、 集區和 Azure 上的叢集。

若要選擇相應放大計算資源的正確方法，請務必了解 Azure 與大型主機有何不同。 索引鍵是如何--或-共用計算資源的資料。 在 Azure 中，（預設） 的資料不通常由多個 Vm 共用。 如果資料共用需要向外延展的多個 Vm 的計算叢集，共用的資料必須位於支援這項功能的資源。 在 Azure 上的資料共用牽涉到儲存體下, 一節討論。

## <a name="azure-compute-optimization"></a>Azure 計算最佳化

您可以最佳化處理的 Azure 架構中的每一層。 針對每個環境中使用 Vm 和功能最適合的型的別。 下圖顯示其中一個可能的模式，部署在 Azure 以支援使用 Db2 的 CICS 應用程式中的 Vm。 在主要站台中，會為生產、生產階段前和測試 VM 部署高可用性。 次要站台供備份和災害復原之用。

每一層也可以提供適當的災害復原服務。 例如，生產環境和資料庫 VM 可能需要熱復原或暖復原，而開發和測試 VM 則支援冷復原。

![支援災害復原的高可用性部署](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>後續步驟

- [大型主機移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型主機重新裝載的 Azure 虛擬機器上](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型主機儲存體移至 Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 資源

- [平行 Sysplex 上 IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合性功能：進階功能](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [針對 Db2 pureScale 功能安裝建立必要使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 建立執行個體命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [大型電腦應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>其他移轉資源

- [Platform Modernization Alliance：在 Azure 上的 IBM Db2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
