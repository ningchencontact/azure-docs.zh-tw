---
title: 包含檔案
description: 包含檔案
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/16/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: c8b25858556538835d6a84bf0d6699f9906f1438
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322642"
---
### <a name="general-requirements"></a>一般需求

* VNet 必須與您用來建立集區的 Batch 帳戶位於相同的訂用帳戶和區域中。

* 使用 VNet 的集區最多可以有 4096 個節點。

* 針對集區指定的子網路必須有足夠的未指派 IP 位址，可容納目標設為集區的 VM 數目；也就是集區之 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 屬性的總和。 如果子網路沒有足夠的未指派 IP 位址，集區會局部配置計算節點，並發生調整大小錯誤。 

* 為 VNet 提供服務的任何自訂 DNS 服務，都必須能解析您的 Azure 儲存體端點。 具體而言，`<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 形式的 URL 應該可解析。 

其他的 VNet 需求不盡相同，這取決於 Batch 集區是位於虛擬機器組態還是雲端服務組態中。 對於部署至 VNet 的新集區，建議使用虛擬機器組態。

### <a name="pools-in-the-virtual-machine-configuration"></a>虛擬機器組態中的集區

**支援的 Vnet** - 僅限以 Azure Resource Manager 為基礎的 Vnet

**子網路識別碼** - 使用 Batch API 指定子網路時，請使用子網路的*資源識別碼*。 子網路識別碼的格式為：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**權限** - 請確認您對 VNet 的訂用帳戶或資源群組的安全性原則或鎖定是否限制了使用者管理 VNet 的權限。

**其他網路資源** - Batch 會在包含 VNet 的資源群組中自動配置其他網路資源。 針對每個50專用節點 (或每20個低優先順序節點), Batch 會配置:1個網路安全性群組 (NSG)、1個公用 IP 位址和1個負載平衡器。 這些資源會被訂用帳戶的[資源配額](../articles/azure-subscription-service-limits.md)所限制。 對於大型集區，您可能必須要求增加其中一或多項資源的配額。

#### <a name="network-security-groups"></a>網路安全性群組

子網路必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作，且必須允許輸出通訊，才能與 Azure 儲存體或其他資源進行通訊。 對於虛擬機器組態中的集區，Batch 會在連結至 VM 的網路介面 (NIC) 層級上新增 NSG。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

* 連接埠 29876 和 29877 上來自 Batch 服務角色 IP 位址的輸入 TCP 流量。 
* 連接埠 22 (Linux 節點) 或連接埠 3389 (Windows 節點) 上的輸入 TCP 流量，以允許遠端存取。 針對 Linux 上的特定類型的多重實例工作 (例如 MPI), 您也必須允許子網中包含 Batch 計算節點之 Ip 的 SSH 埠22流量。
* 任何連接埠上傳至虛擬網路的輸出流量。
* 任何連接埠上傳至網際網路的輸出流量。

> [!IMPORTANT]
> 如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 拒絕對所指定子網路中計算節點的通訊，則 Batch 服務會將計算節點的狀態設為 [無法使用]  。

您不需要在子網路層級指定 NSG，因為 Batch 會設定其本身的 NSG。 不過，如果指定的子網路有相關聯的網路安全性群組 (NSG) 和 (或) 防火牆，請設定輸入和輸出安全性規則，如下列表格所示。 只有當您需要允許從外部來源對集區 Vm 進行遠端存取時, 才必須在埠 3389 (Windows) 或 22 (Linux) 上設定輸入流量。 集區 VM 不需要此設定即可使用。 請注意, 如果使用特定種類的多重實例工作 (例如 MPI), 您將需要在 Linux 的埠22上啟用虛擬網路子網流量。

<bpt id="p1">**</bpt>Inbound security rules<ept id="p1">**</ept>

| 來源 IP 位址 | 來源服務標籤 | 來源連接埠 | 目的地 | 目的地連接埠 | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[服務標記](../articles/virtual-network/security-overview.md#service-tags) | * | Any | 29876-29877 | TCP | 允許 |
| 使用者來源 Ip, 用於遠端存取 Linux 多重實例工作的計算節點和/或計算節點子網 (如有需要)。 | N/A | * | Any | 3389 (Windows)、22 (Linux) | TCP | 允許 |

**輸出安全性規則**

| Source | 來源連接埠 | 目的地 | 目的地服務標記 | 目的地連接埠 | Protocol | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [服務標記](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(位於與您的 Batch 帳戶和 VNet 相同的區域中) | 443 | TCP | 允許 |

### <a name="pools-in-the-cloud-services-configuration"></a>雲端服務組態中的集區

**支援的 VNet** - 僅限傳統 VNet

**子網路識別碼** - 使用 Batch API 指定子網路時，請使用子網路的*資源識別碼*。 子網路識別碼的格式為：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**權限** - 針對指定的 VNet，`Microsoft Azure Batch` 服務主體必須具有 `Classic Virtual Machine Contributor` 角色型存取控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>網路安全性群組

子網路必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作，且必須允許輸出通訊，才能與 Azure 儲存體或其他資源進行通訊。

您不需要指定 NSG，因為 Batch 只會設定從 Batch IP 位址到集區節點的輸入通訊。 不過，如果指定的子網路有相關聯的 NSG 和 (或) 防火牆，請設定輸入和輸出安全性規則，如下列表格所示。 如果 NSG 拒絕對所指定子網路中計算節點的通訊，則 Batch 服務會將計算節點的狀態設為 [無法使用]  。

如果您需要允許對集區節點的 RDP 存取, 請在適用于 Windows 的埠3389上設定輸入流量。 集區節點不需要此設定即可使用。

<bpt id="p1">**</bpt>Inbound security rules<ept id="p1">**</ept>

| 來源 IP 位址 | 來源連接埠 | 目的地 | 目的地連接埠 | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />雖然這實際上需要「全部允許」，但 Batch 服務會在每個節點的層級上套用 ACL 規則，而篩選掉所有非 Batch 服務的 IP 位址。 | * | Any | 10100、20100、30100 | TCP | 允許 |
| 選擇性, 允許 RDP 存取計算節點。 | * | Any | 3389 | TCP | 允許 |

**輸出安全性規則**

| Source | 來源連接埠 | 目的地 | 目的地連接埠 | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | 允許 |
