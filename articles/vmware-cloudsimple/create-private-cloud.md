---
title: Azure VMware Solution by CloudSimple-建立 CloudSimple 私用雲端
description: 描述如何建立 CloudSimple 私用雲端, 以營運彈性和持續性將 VMware 工作負載擴充至雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aacdb57c312946a9ec2b17a8d41aa9150efc277d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640979"
---
# <a name="create-a-cloudsimple-private-cloud"></a>建立 CloudSimple 私用雲端

私用雲端是一種隔離的 VMware 堆疊, 可支援 ESXi 主機、vCenter、vSAN 和 NSX。 私人雲端是透過 CloudSimple 入口網站來管理。 他們在自己的管理網域中有自己的 vCenter server。 堆疊會在專用節點和隔離的裸機硬體節點上執行。

建立私用雲端可協助您解決網路基礎結構的各種常見需求:

* **成長**。 如果您已達到現有基礎結構的硬體重新整理點, 私用雲端可讓您擴充, 而不需要新的硬體投資。

* **快速擴充**。 如果發生任何暫時性或未規劃的容量需求, 私用雲端可讓您建立額外的容量, 而不會有延遲。

* **加強保護**。 有三個或多個節點的私用雲端時, 您會獲得自動的冗余和高可用性保護。

* **長期基礎結構需求**。 如果您的資料中心已達到產能, 或您想要重新組織以降低成本, 私用雲端可讓您淘汰資料中心, 並遷移至雲端式解決方案, 同時與您的企業營運保持相容。

當您建立私人雲端時, 您會取得單一 vSphere 叢集, 以及在該叢集中建立的所有管理 Vm。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取[CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>建立新的私人雲端

1. 選取 [所有服務]。
2. 搜尋**CloudSimple Services**。
3. 選取您要在其上建立私人雲端的 CloudSimple 服務。
4. 在 **[總覽**] 中, 按一下 [**建立私人雲端**] 以開啟 CloudSimple 入口網站的新瀏覽器索引標籤。 若出現提示, 請使用您的 Azure 登入認證登入。

    ![從 Azure 建立私人雲端](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 入口網站中, 提供私人雲端的 [名稱]。
6. 選取私人雲端的 [**位置**]。
7. 選取 [**節點類型**], 與您在 Azure 上購買的內容一致。  您可以選擇 [ [CS28] 或 [CS36] 選項](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)。 第二個選項包含最大計算和記憶體容量。
8. 指定**節點計數**。  至少需要三個節點, 才能建立私人雲端。

    ![建立私用雲端-基本資訊](media/create-private-cloud-basic-info.png)

9. 按一下 **下一步Advanced 選項**。
10. 輸入 vSphere/vSAN 子網的 CIDR 範圍。 請確定 CIDR 範圍不會與您的任何內部部署或其他 Azure 子網 (虛擬網路) 或閘道子網重迭。

    **CIDR 範圍選項:** /24、/23、/22 或/21。 A/24 CIDR 範圍最多支援9個節點,/23 CIDR 範圍最多支援41節點, 而/22 和/21 CIDR 範圍最多可支援64個節點 (私用雲端中的節點數目上限)。

    > [!IMPORTANT]
    > VSphere/vSAN CIDR 範圍中的 IP 位址會保留供私人雲端基礎結構使用。  請勿在任何虛擬機器上使用此範圍內的 IP 位址。

11. 按一下 **下一步審查和建立**。
12. 檢查設定。 如果您需要變更任何設定, 請按一下 [**上一步**]。
13. 按一下 [建立]。

私用雲端布建程式隨即啟動。 布建私人雲端最多可能需要兩個小時的時間。

如需擴充現有私人雲端的指示, 請參閱[擴充私人雲端](expand-private-cloud.md)。
