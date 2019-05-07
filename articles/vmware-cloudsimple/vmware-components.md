---
title: Azure 的 VMware 解決方案由 CloudSimple-私用雲端之 VMware 元件
description: 描述如何在私人雲端上安裝 VMware 元件
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160181"
---
# <a name="private-cloud-vmware-components"></a>私人雲端之 VMware 元件

私人雲端是隔離的 VMware 堆疊 （ESXi 主機、 vCenter、 vSAN 和 NSX） 由 vCenter 伺服器管理網域中受管理的環境。  CloudSimple 服務可讓您部署 VMware 原生 Azure bare metal 基礎結構中的 Azure 位置。  私用雲端會與其餘的 Azure 雲端整合。  私用雲端的部署具有下列的 VMware 堆疊元件：

* **VMware ESXi-** 在 Azure 上的 Hypervisor 專用節點
* **VMware vCenter-** 私用雲端 vSphere 環境的集中管理的應用裝置
* **VMware 的 vSAN-** 超交集基礎結構解決方案
* **VMware NSX 資料中心-** 網路虛擬化與安全性軟體  

## <a name="vmware-component-versions"></a>VMware 元件版本

私用雲端的 VMware 堆疊會部署下列軟體版本。

| 元件 | 版本 | 授權的版本 |
|-----------|---------|------------------|
| ESXi | 6.7U1 | 企業加上 |
| vCenter | 6.7U1 | vCenter 標準 |
| vSAN | 6.7 | Enterprise |
| NSX 資料中心 | 2.3 | 進階 |

## <a name="esxi"></a>ESXi

當您建立私用雲端時，會購買 CloudSimple 節點上安裝 VMware ESXi。  ESXi 提供 hypervisor 的部署工作負載的虛擬機器 (Vm)。  節點對您的私用雲端提供超交集基礎結構 （計算和儲存體）。  節點是於私人雲端在 vSphere 叢集的一部分。  每個節點有四個實體網路介面連接到為網路。  用來建立兩個實體網路介面**vSphere 分散式切換 (VDS)** vCenter 和兩個用來建立**NSX 管理分散式的交換器 (N-VDS)**。  網路介面設定為高可用性的主動-主動模式。

深入了解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server 應用裝置

vCenter server 應用裝置 (VCSA) 提供驗證、 管理和協調流程函式由 CloudSimple VMware 的解決方案。 當您建立私人雲端時，會部署 VCSA 與內嵌平台服務控制站 (PSC)。  當您部署您的私人雲端時，會建立在 vSphere 叢集上部署 VCSA。  每個私人雲端都有它自己 VCSA。  私用雲端的擴充會將節點加入至私人雲端上 VCSA。

### <a name="vcenter-single-sign-on"></a>vCenter 單一登入

內嵌的平台服務控制站上 VCSA 相關聯**單一登入網域 vCenter**。  網域名稱**cloudsimple.local**。  預設使用者**CloudOwner@cloudsimple.com**為您建立可存取 vCenter。  您可以加入您在內部部署/Azure active directory [vCenter 的身分識別來源](https://docs.azure.cloudsimple.com/set-vcenter-identity/)。

## <a name="vsan-storage"></a>vSAN 的儲存體

私人雲端建立完整設定的全快閃 vSAN 儲存體中，本機叢集。  相同 SKU 的最小的三個節點，才能使用 vSAN 資料存放區建立 vSphere 叢集。  根據預設，會將重複資料刪除和壓縮啟用 vSAN 資料存放區上。  在 vSphere 叢集的每個節點上建立兩個磁碟群組。 每個磁碟群組包含一個快取磁碟和三個磁碟的容量。

預設 vSAN 儲存體原則是在 vSphere 叢集上建立和套用至 vSAN 資料存放區。  此原則會決定 VM 儲存體物件方式進行佈建和配置內的資料存放區，以確保必要的服務等級。  儲存體原則會定義**容許 (FTT) 失敗**並**失敗容錯方法**。  您可以建立新的存放裝置原則，並將它們套用至 Vm。 若要維持 SLA，25%的備用容量必須維護 vSAN 資料存放區上。  

### <a name="default-vsan-storage-policy"></a>預設 vSAN 存放裝置原則

下表顯示 vSAN 儲存體原則參數的預設值。

| 在 vSphere 叢集的節點數目 | FTT | 失敗容錯方法 |
|------------------------------------|-----|--------------------------|
| 3 和 4 個節點 | 1 | RAID 1 （鏡像）-建立 2 個複本 |
| 5 到 16 個節點 | 2 | RAID 1 （鏡像）-建立 3 個複本 |

## <a name="nsx-data-center"></a>NSX 資料中心

NSX 資料中心提供私人雲端網路虛擬化、 micro 分割和網路安全性功能。  您可以設定 NSX NSX 透過私人雲端上的資料中心所支援的所有服務。  當您建立私用雲端時，會安裝並設定下列 NSX 元件。

* NSXT 管理員
* 傳輸的區域
* 主應用程式和 Edge 上行設定檔
* Edge Transport、 Ext1，和 Ext2 的邏輯交換器
* 針對 ESXi 傳輸節點的 IP 集區
* 邊緣傳輸節點的 IP 集區
* 邊緣節點
* DRS 反親和性規則的控制站和邊緣 Vm
* 第 0 層路由器
* 啟用 Tier0 路由器上的 BGP

## <a name="vsphere-cluster"></a>vSphere 叢集

ESXi 主機被設定為以確保私用雲端的高可用性叢集。  當您建立私用雲端時，第一個叢集部署 vSphere 的管理元件。  資源集區建立管理元件，並管理的所有 Vm 會都部署在此資源集區中。 無法刪除第一個叢集，以壓縮私用雲端。  vSphere 叢集提供高可用性 Vm 使用**vSphere HA**。  要容許的失敗根據叢集中可用的節點數目。  您可以使用公式```Number of nodes = 2N+1```其中```N```是要容許的失敗數目。

### <a name="vsphere-cluster-limits"></a>vSphere 叢集限制

| 資源 | 限制 |
|----------|-------|
| 若要建立私人雲端的節點數目下限 （第一個 vSphere 叢集） | 3 |
| 叢集在私人雲端上的 vSphere 中的節點數目上限 | 16 |
| 私人雲端中的節點數目上限 | 64 |
| 最大數目 vSphere 叢集在私人雲端 | 21 |
| 新的 vSphere 叢集上的節點數目下限 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

偶爾也會需要 VMware 基礎結構的組態進行變更。 目前，這些間隔可能會發生每隔 1 到 2 月，但是頻率應該拒絕一段時間。 這種類型的維護通常可以完成而不會中斷正常 CloudSimple 服務耗用量。 在 VMware 維護間隔內，下列服務繼續運作而造成的影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有的網路和儲存體
* Azure 的所有流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責生命週期管理的 VMware （ESXi、 vCenter、 PSC 和 NSX） 軟體的私人雲端。

軟體更新包括：

* **修補程式**。 安全性修補程式或 VMware 所發行的 bug 修正。
* **更新**。 次要版本的 VMware 堆疊元件的變更。
* **升級**。 主要版本的 VMware 堆疊元件的變更。

CloudSimple 可用從 VMware 時，測試重大安全性修補程式。 每個 SLA，CloudSimple 推出的安全性修補程式到私用雲端環境一週內。

CloudSimple 提供 VMware 軟體元件的每季的維護更新。 VMware 軟體的新主要版本可用時，CloudSimple 的運作方式與客戶合作，協調適當的維護期間升級。  

## <a name="next-steps"></a>後續步驟

* [CloudSimple 維護和更新](cloudsimple-maintenance-updates.md)