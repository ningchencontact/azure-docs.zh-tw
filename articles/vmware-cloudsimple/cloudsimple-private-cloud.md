---
title: 在 VMware 的解決方案，由 CloudSimple-Azure 中的私人雲端
description: 深入了解 CloudSimple 私用雲端和概念。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577041"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 私人雲端概觀

CloudSimple 轉換，並擴充到公用雲端的 VMware 工作負載，以分鐘為單位。 CloudSimple 服務，您可以使用部署 VMware 原生 Azure bare metal 基礎結構。 您的部署所在的 Azure 位置，並與 Azure 雲端的其餘部分完全整合。

* CloudSimple 解決方案提供完整的 VMware 操作持續性。 此解決方案可讓您的公用雲端優點：
  * 彈性
  * 創新
  * 效率
* 使用 CloudSimple，您會受益於降低擁有權總成本的雲端耗用量模型。 此外，它也會提供隨選佈建、 全額而定-做-您-成長，以及容量最佳化。
* CloudSimple 完全適用於：
  * 現有的工具
  * 技術
  * 處理序
* 此相容性可讓您的小組來管理 Azure 雲端上的工作負載，而不會中斷您的原則：
  * 網路
  * 安全性  
  * 資料保護  
  * 稽核
* CloudSimple 管理基礎結構和所有必要網路功能和管理的服務。 CloudSimple 服務可讓您的小組專注於：
  * 商業價值
  * 應用程式佈建
  * 業務持續性
  * 支援
  * 強制執行原則

## <a name="private-cloud-environment-overview"></a>私用雲端環境概觀

私用雲端是隔離的 VMware 堆疊，例如這些環境：

* ESXi 主機
* vCenter
* vSAN
* NSX

私用雲端是由 vCenter 伺服器管理在自己管理定義域中。

在堆疊上執行：

* 專用節點
* 隔離裸機硬體節點

使用者會取用透過原生 VMware 工具，包括堆疊：

* vCenter
* NSX 管理員

您可以部署在 Azure 位置的專用的節點。 然後您可以使用 Azure 和 CloudSimple 管理它們。 私用雲端是由一或多個 vSphere 叢集所組成，每個叢集包含 3 到 16 個節點。

您可以建立私用雲端，使用購買節點：

* 隨用隨付節點
* 保留的專用節點

您可以在內部部署環境與 Azure 使用下列連線的網路連線私用雲端：

* 安全
* 私用 VPN
* Azure ExpressRoute

私用雲端環境被設計來排除有單一失敗點：

* ESXi 叢集已設定 vSphere 的高可用性，並調整大小必須至少一個備用的節點，提供恢復功能。
* vSAN 提供備援的主要儲存體。 vSan 需要至少三個節點，以針對單一的失敗提供保護。 您可以設定為較大的叢集提供更高的恢復功能的 vSAN。
* 您可以使用 RAID 10 存放裝置原則，以防止儲存體失敗設定 vCenter、 PSC 和 NSX Manager Vm。 然後，在它們受保護的節點及網路故障的 vSphere HA。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私用雲端案例

* **資料中心淘汰或移轉**

  * 當您達到您現有的資料中心的限制，或重新整理硬體時，請取得額外的容量。
  * 在雲端中，新增所需的容量，並排除管理硬體更新的麻煩。
  * 降低風險與成本的雲端移轉，相較於耗時的轉換或建構。
  * 使用熟悉的 VMware 工具和技能來加速雲端移轉。 在雲端中，使用 Azure 服務來現代化您的應用程式，您的步調。

* **展開 依需求**

  * 展開到雲端，以符合非預期的需求，例如新的開發環境或季節性的容量暴增。
  * 視需要建立新的容量，並讓它保持，只要您需要它。
  * 降低初期投資、 加快速度的佈建，並降低複雜度具有相同的架構和原則跨內部部署和雲端。

* **嚴重損壞修復和 Azure 雲端中的虛擬桌面**

  * 建立遠端存取資料、 應用程式，以及以 Azure 雲端中的桌面。 透過高頻寬連線，您需要上傳 / 下載資料速度來從事件復原。 低延遲網路讓您快速回應時間預期從傳統型應用程式的使用者。

  * 您所有的原則和網路使用熟悉的 VMware 工具與 CloudSimple 入口網站在雲端中複寫。 投入時間和風險的建立和管理 DR 和 VDI 的實作，可降低此複寫。

* **高效能應用程式和資料庫**

  * 執行您最嚴苛的工作負載，提供 CloudSimple 超交集架構。
  * 執行 Oracle、 Microsoft SQL server、 中介軟體系統，以及高效能 NOSQL 資料庫。

  * 為您自己的資料中心，利用高速 25 Gbps 的網路連線雲端的經驗。 高速連線可讓您執行跨內部部署，在 Azure 上的 VMware 的混合式應用程式和 Azure 私用的工作負載，不會影響效能。

* **真正的混合式**

  * 整合 DevOps 之間的 VMware 與 Azure 服務。
  * VMware 管理 Azure 服務和解決方案，可以套用到所有工作負載進行最佳化。
  * 存取公用雲端服務，而不需要依序展開您的資料中心，或重新架構您的應用程式。
  * 集中管理身分識別，存取控制原則，記錄和監視在 Azure 上的 VMware 應用程式。

## <a name="limits"></a>限制

下表顯示的節點限制對私人雲端的資源。

| 資源 | 限制 |
|----------|-------|
| 若要建立私人雲端的節點數目下限 | 3 |
| 在私人雲端上的叢集中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 在新叢集上的節點數目下限 | 3 |

## <a name="next-steps"></a>後續步驟

* 了解如何[建立私人雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)