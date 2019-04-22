---
title: 規劃您的 Avere vFXT 系統 - Azure
description: 說明在部署Avere vFXT for Azure 之前的規劃事項
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 46978d19a0789bb43e861ca89661aa5b78eb4ec7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271062"
---
# <a name="plan-your-avere-vfxt-system"></a>規劃您的 Avere vFXT 系統

這篇文章說明如何規劃新的 Avere vFXT 定位，並針對您的需求的適當調整大小的 Azure 叢集。 

前往 Azure Marketplace 或建立任何 VM 之前，請考慮叢集與 Azure 中其他元素互動的方式。 規劃叢集資源在私人網路和子網路中的位置，並決定您後端儲存體的位置。 請確定您所建立的叢集節點足夠強大，可支援您的工作流程。 

繼續閱讀以深入了解。

## <a name="resource-group-and-network-infrastructure"></a>資源群組和網路基礎結構

請考慮部署 Avere vFXT for Azure 元素的位置。 下圖顯示 Avere vFXT for Azure 元件可能的排列方式：

![本圖顯示一個子網路內的叢集控制器和叢集 VM。 子網路界限周圍是 Vnet 界限。 Vnet 內是一個代表儲存體服務端點的六邊形；它會以虛線箭號連線至 Vnet 外部的 Blob 儲存體。](media/avere-vfxt-components-option.png)

規劃 Avere vFXT 系統的網路基礎結構時，請遵循下列指導方針：

* 所有元素都應該使用針對 Avere vFXT 部署建立的新訂用帳戶管理。 優點包括： 
  * 追蹤成本更簡單 - 在一個訂用帳戶中檢視和稽核所有資源、基礎結構及計算循環的成本。
  * 清除更容易 - 您可以在完成專案後移除整個訂用帳戶。
  * 方便資料分割資源的配額-其他重要的工作負載防範可能節流藉由隔離 Avere vFXT 用戶端與單一訂用帳戶中的叢集的資源。 將大量的高效能運算工作流程的用戶端時，這可避免衝突。

* 找出接近 vFXT 叢集的用戶端計算系統。 後端儲存體可能更加遙遠。  

* VFXT 叢集和叢集控制站 VM 應該位於相同的虛擬網路 (vnet)，在相同的資源群組，並使用相同的儲存體帳戶。 自動的叢集建立範本會為處理大部分的情況。

* 此叢集必須位於自己的子網路中，以避免 IP 位址與用戶端或計算資源發生衝突。 

* 叢集建立範本可以建立大部分的叢集，包括資源群組、 虛擬網路、 子網路和儲存體帳戶的所需的基礎結構資源。 如果您想要使用已經存在的資源，請確定它們符合此表中的需求。 

  | 資源 | 使用現有的嗎？ | 需求 |
  |----------|-----------|----------|
  | 資源群組 | 是，如果是空的 | 必須是空的| 
  | 儲存體帳戶 | 如果連接的現有 Blob 容器，在叢集建立之後 <br/>  否，如果在叢集建立期間建立新的 Blob 容器 | 必須是空的現有 Blob 容器 <br/> &nbsp; |
  | 虛擬網路 | 是 | 如果建立新的 Azure Blob 容器必須包含儲存體服務端點 | 
  | 子網路 | 是 |   |

## <a name="ip-address-requirements"></a>IP 位址需求 

請確定您叢集子網路的 IP 位址範圍夠大，足以支援叢集。 

Avere vFXT 叢集會使用下列 IP 位址：

* 一個叢集管理 IP 位址。 此位址可能會在叢集中的節點之間移動，但永遠可以使用，讓您可以連線到 Avere 控制台設定工具。
* 對於每個叢集節點：
  * 至少有一個面向用戶端的 IP 位址  (所有面向用戶端的位址都由叢集的 *vserver* 管理，如有需要，這些位址可以在節點之間移動)。
  * 一個用於叢集通訊的 IP 位址
  * 一個執行個體 IP 位址 (指派給 VM)

如果您使用 Azure Blob 儲存體，它也可能需要來自叢集 Vnet 的 IP 位址：  

* Azure Blob 儲存體帳戶必須至少有五個 IP 位址。 如果您在與叢集相同的 Vnet 中找到 Blob 儲存體，請記住這項需求。
* 如果您使用叢集虛擬網路外的 Azure Blob 儲存體，您應該在 Vnet 內建立一個儲存體服務端點。 此端點不使用 IP 位址。

您可以選擇在叢集的不同資源群組中，找出網路資源和 Blob 儲存體 (如果使用的話)。

## <a name="vfxt-node-size"></a>vFXT 節點大小

當作叢集節點的 VM 可決定您快取的要求輸送量和儲存體容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每個 vFXT 節點都將相同。 也就是說，如果您建立一個三節點的叢集，您將會有三個相同類型和大小的 VM。 

| 執行個體類型 | vCPU | 記憶體  | 本機 SSD 儲存體  | 最大資料磁碟 | 取消快取的磁碟輸送量 | NIC (計數) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每個節點的磁碟快取都可以設定，而且範圍可以從 1000 GB 到 8000 GB。 每個節點的 4 TB 是建議的快取大小 Standard_E32s_v3 節點。

如需有關這些 Vm 的詳細資訊，閱讀 Microsoft Azure 文件：

* [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帳戶配額

請確定您的訂用帳戶具有執行 Avere vFXT 叢集，以及所使用之任何計算或用戶端系統的容量。 如需詳細資訊，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>後端資料儲存體

當資料不在快取中時，Avere vFXT 叢集應將資料儲存在哪？ 決定工作集將長期儲存在新的 Blob 容器中，還是儲存在現有雲端或硬體儲存體系統。 

如果您想要將 Azure Blob 儲存體用於後端，您應該在建立 vFXT 叢集時建立新的容器。 此選項會建立並設定新的容器，使其可以在叢集就緒時使用。 

請參閱[建立 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)，以了解詳細資料。

> [!NOTE]
> 只有空的 Blob 儲存體容器可以當作 Avere vFXT 系統的核心篩選使用。 vFXT 必須能夠管理其物件存放區，而不需要保留現有的資料。 
>
> 請閱讀[將資料移動到 vFXT 叢集](avere-vfxt-data-ingest.md)以了解如何使用用戶端電腦和 Avere vFXT 快取，將資料有效率地複製到叢集的新容器中。

如果您想要使用現有的內部部署儲存體系統，您必須在建立該儲存體系統之後，將其新增至 vFXT 叢集。 如需有關如何將現有的儲存體系統新增至 Avere vFXT 叢集的詳細指示，請閱讀[設定儲存體](avere-vfxt-add-storage.md)。

## <a name="cluster-access"></a>叢集存取 

Avere vFXT for Azure 位於私人子網路，叢集並沒有公用 IP 位址。 您必須擁有存取私人子網路的方法，才能管理叢集和連線到用戶端。 

存取選項包括：

* 跳板主機 - 將公用 IP 位址指派給私人網路中不同的 VM，並使用此位址建立 SSL 通道來連線到叢集節點。 

  > [!TIP]
  > 如果您在叢集控制器上設定公用 IP 位址，則此控制器可作為跳板主機。 如需詳細資訊，請參閱[作為跳板主機的叢集控制器](#cluster-controller-as-jump-host)。

* 虛擬私人網路 (VPN) - 針對您的私人網路設定點對站或站對站 VPN。

* Azure ExpressRoute - 透過 ExpressRoute 合作夥伴設定私人連線。 

如需有關這些選項的詳細資訊，請參閱[關於網際網路通訊的 Azure 虛擬網路文件](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>作為跳板主機的叢集控制器

如果您在叢集控制器上設定公用 IP 位址，您可以將其作為跳板主機，從私人子網路外連絡 Avere vFXT 叢集。 不過，由於控制器有修改叢集節點的存取權限，因此會產生輕微的安全性風險。  

若要改善具有公用 IP 位址的控制站的安全性，部署指令碼會自動建立網路安全性群組，將輸入的存取限制為只有連接埠 22。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

建立叢集時，您可以選擇是否要在叢集控制器上建立公用 IP 位址。 

* 如果您建立新的 Vnet 或新的子網路，則系統將會指派公用 IP 位址給叢集控制器。
* 如果您選取現有的 Vnet 和子網路，則叢集控制器就只會有私人 IP 位址。 

## <a name="vm-access-roles"></a>VM 存取角色 

Azure 會使用[角色型存取控制](../role-based-access-control/index.yml)(RBAC) 來授權叢集來執行特定工作的 Vm。 例如，叢集控制器需要授權，才能建立和設定叢集節點 Vm。 叢集節點必須能夠指派或重新指派到其他叢集節點的 IP 位址。

兩個內建的 Azure 角色用於 Avere vFXT 虛擬機器： 

* 叢集控制站會使用內建角色[Avere 參與者](../role-based-access-control/built-in-roles.md#avere-contributor)。 
* 叢集節點使用的內建角色[Avere 運算子](../role-based-access-control/built-in-roles.md#avere-operator)

如果您需要自訂 Avere vFXT 元件存取角色，您必須定義您自己的角色，並將其指派給 Vm 時建立。 您無法在 Azure Marketplace 中使用的部署範本。 中所述，在 Azure 入口網站中開啟票證，請參閱 Microsoft 客戶服務與支援中心[取得協助與您的系統](avere-vfxt-open-ticket.md)。 

## <a name="next-step-understand-the-deployment-process"></a>下一步：了解部署程序

[部署概觀](avere-vfxt-deploy-overview.md)提供建立 Avere vFXT for Azure 系統，並準備好提供資料所需所有步驟的概觀。  