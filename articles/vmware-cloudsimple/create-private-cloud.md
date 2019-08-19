---
title: 透過 CloudSimple 私用雲端建立 Azure VMware 解決方案
description: 描述如何建立 CloudSimple 私用雲端, 以營運彈性和持續性將 VMware 工作負載擴充至雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812260"
---
# <a name="create-a-cloudsimple-private-cloud"></a>建立 CloudSimple 私用雲端

建立私用雲端可協助您解決網路基礎結構的各種常見需求:

* **成長**。 如果您已達到現有基礎結構的硬體重新整理點, 私用雲端可讓您擴充, 而不需要新的硬體投資。

* **快速擴充**。 如果發生任何暫時性或未規劃的容量需求, 私用雲端可讓您建立額外的容量, 而不會有延遲。

* **加強保護**。 有三個或多個節點的私用雲端時, 您會獲得自動的冗余和高可用性保護。

* **長期基礎結構需求**。 如果您的資料中心已達到產能, 或您想要重新組織以降低成本, 私用雲端可讓您淘汰資料中心, 並遷移至雲端式解決方案, 同時與您的企業營運保持相容。

當您建立私人雲端時, 您會取得單一 vSphere 叢集, 以及在該叢集中建立的所有管理 Vm。

## <a name="before-you-begin"></a>開始之前

您必須先布建節點, 才能建立私人雲端。  如需布建節點的詳細資訊, 請參閱 < 為[VMware 解決方案提供節點 CloudSimple-Azure](create-nodes.md)文章。

為私人雲端的 vSphere/vSAN 子網配置 CIDR 範圍。 私用雲端會建立為 vCenter server 所管理的隔離 VMware 堆疊 (ESXi 主機、vCenter、vSAN 和 NSX) 環境。 管理元件會部署在為 vSphere/vSAN 子網 CIDR 選取的網路中。 在部署期間, 網路 CIDR 範圍會分成不同的子網。  VSphere/vSAN 子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通訊的網路重迭。  與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。  如需有關 vSphere/vSAN 子網的詳細資訊, 請參閱[vlan 和子網總覽](cloudsimple-vlans-subnets.md)。

* 最小 vSphere/vSAN 子網 CIDR 範圍前置詞:/24 
* 最大 vSphere/vSAN 子網 CIDR 範圍前置詞:/21

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取[CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>建立新的私人雲端

1. 在 [**資源**] 頁面上, 按一下 [**新增私人雲端**]。

    ![建立私用雲端-如何開始](media/create-pc-button.png)

2. 選取要裝載私人雲端資源的位置。

3. 選擇為私人雲端布建的 [CS28] 或 [CS36] 節點類型 [you'ev]。 第二個選項包含最大計算和記憶體容量。

4. 選取私人雲端的節點數目。 您最多可以選取 you'ev 已布[建](create-nodes.md)的可用節點數目。

    ![建立私用雲端-基本設定](media/create-private-cloud-basic-info.png)

5. 按一下 **下一步Advanced 選項**。

6. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 請確定 CIDR 範圍不會與您的任何內部部署或其他 Azure 子網 (虛擬網路) 或閘道子網重迭。  請勿使用 Azure 虛擬網路上定義的任何 CIDR 範圍。
    
    **CIDR 範圍選項:** /24、/23、/22 或/21。 A/24 CIDR 範圍最多支援9個節點,/23 CIDR 範圍最多支援41節點, 而/22 和/21 CIDR 範圍最多可支援64個節點 (私用雲端中的節點數目上限)。

    > [!CAUTION]
    > VSphere/vSAN CIDR 範圍中的 IP 位址會保留供私人雲端基礎結構使用。  請勿在任何虛擬機器上使用此範圍內的 IP 位址。

7. 按一下 **下一步審查和建立**。

8. 檢查設定。 如果您需要變更任何設定, 請按一下 [**上一步**]。

9. 按一下 [建立]。

當您按一下 [建立] 之後, 就會開始私人雲端布建。  您可以從 CloudSimple 入口網站上的 [[工作]](https://docs.azure.cloudsimple.com/activity/#tasks) 頁面監視進度。  布建可能需要30分鐘到兩小時的時間。  布建完成後, 您就會收到一封電子郵件。

## <a name="next-steps"></a>後續步驟

* [擴充私人雲端](expand-private-cloud.md)