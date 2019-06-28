---
title: 管理 Azure 的 VMware 解決方案 CloudSimple 私用雲端
description: 說明可用來管理您的 CloudSimple 私用雲端資源和活動的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332775"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私用雲端資源和活動

CloudSimple 入口網站被管理私人雲端。  請檢查狀態、 可用的資源、 私人雲端和 CloudSimple 入口網站中的其他設定的活動。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取權[CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>檢視私用雲端的清單

**私人雲端**索引標籤上**資源**頁面會列出您訂用帳戶中的所有私用雲端。 資訊會包括名稱、 數字的 vSphere 叢集，位置，目前狀態的私用雲端和資源的資訊。

![私用雲端頁面](media/manage-private-cloud.png)

選取 私用雲端的其他資訊和動作。

## <a name="private-cloud-summary"></a>私用雲端摘要

檢視所選的私用雲端的完整摘要。  摘要頁面會包含私用雲端部署的 DNS 伺服器。  您可以設定 DNS 將從內部部署 DNS 伺服器轉送到您的私用雲端的 DNS 伺服器。  如需有關 DNS 轉送的詳細資訊，請參閱[設定的 DNS 進行名稱解析從內部部署的私用雲端 vcenter](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)。

![私用雲端摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用動作

* [啟動 vSphere 用戶端](https://docs.azure.cloudsimple.com/vsphere-access/)。 此私用雲端來存取 vCenter。
* [購買節點](create-nodes.md)。 將節點新增到此私用雲端。
* [展開](expand-private-cloud.md)。 將節點新增到此私用雲端。
* **重新整理**。 更新此頁面上的資訊。
* **刪除**。 您可以隨時刪除私用雲端。 **在刪除之前，請確定您已備份所有的系統和資料。** 刪除私用雲端會刪除所有的 Vm、 vCenter 組態和資料。 按一下 **刪除**在選取的私用雲端的 摘要 區段中。 刪除下列私用雲端的所有資料會在安全且高度相容的清除程序中都清除。
* [變更 vSphere 權限](escalate-private-cloud-privileges.md)。  提升您的權限，此私用雲端。

## <a name="private-cloud-vlanssubnets"></a>私用雲端 VLAN/子網路

檢視所選的私用雲端定義 Vlan/子網路清單。  此清單包含 Vlan/子網路建立私人雲端時所建立的管理。

![私用雲端-Vlan/子網路](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用動作

* [新增子網路VLAN/](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 加入此私人雲端中的 VLAN/子集合。

選取下列動作 VLAN/子網路
* [附加防火牆資料表](https://docs.azure.cloudsimple.com/firewall/)。 將防火牆資料表連結至這個私用雲端中。
* **編輯**
* **刪除**（只有使用者定義 Vlan/子網路）

## <a name="private-cloud-activity"></a>私用雲端活動

檢視所選的私用雲端的下列資訊。  活動資訊是針對所選的私用雲端的所有活動篩選的清單。  此頁面會顯示最多 25 個最近的活動。

* 最近的警示
* 最近的事件
* 最近的工作
* 新的稽核

![私用雲端-活動](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>雲端機架

雲端機架是建置組塊的私用雲端。 每個機架提供一種容量單位。 CloudSimple 會自動設定根據您選取項目時建立或擴充私人雲端的雲端機架。  檢視雲端機架的完整清單，其中的每個指派給包含私用雲端。

![私用雲端-雲端機架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理網路

VMware 管理資源和目前未設定私用雲端的虛擬機器的清單。 資訊包括軟體版本、 完整的網域名稱 (FQDN)，以及資源的 IP 位址。

![私用雲端-vSphere 管理網路](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入了解[私用雲端](cloudsimple-private-cloud.md)