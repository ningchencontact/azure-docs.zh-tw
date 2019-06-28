---
title: 展開 Azure VMware CloudSimple 私用雲端的解決方案
description: 說明如何擴充以增加容量，在現有或新叢集中現有的 CloudSimple 私用雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332527"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>展開 CloudSimple 私用雲端

CloudSimple 提供動態展開 私用雲端的彈性。 您可以開始使用較小的組態，然後再展開 視需要更高的容量。 或者，您可以建立私用雲端，以根據目前的需求，然後再展開 耗用量增加時。

私用雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有 3 到 16 個節點。  當您展開私人雲端，您將節點新增至現有的叢集，或建立新的叢集。 若要擴充現有的叢集，其他節點都必須與現有節點相同的類型 (SKU)。 如需建立新的叢集，節點可以是不同的類型。 如需有關限制的私用雲端的詳細資訊，請參閱限制一節[CloudSimple 私人雲端概觀](cloudsimple-private-cloud.md)文章。

預設值建立私人雲端**Datacenter** vCenter 上。  每個資料中心做為最上層管理實體。  針對新叢集，CloudSimple 會提供將現有的資料中心加入或建立新的資料中心的選擇。

新的叢集組態的一部分，CloudSimple 設定 VMware 基礎結構。  設定包括 vSAN 磁碟群組、 VMware 高可用性，以及分散式資源排程器 」 (DRS) 的儲存體設定。

私用雲端可以展開多次。 只有當您未超出整體的節點限制時，即可擴充。 每次您展開私人雲端，您將新增至現有的叢集，或建立新的帳戶。

## <a name="before-you-begin"></a>開始之前

您可以展開您的私人雲端之前，必須佈建節點。  如需有關如何佈建節點的詳細資訊，請參閱[佈建的 VMware 的解決方案，由 CloudSimple-Azure 節點](create-nodes.md)文章。  如需建立新的叢集，您必須擁有至少三個可用的節點的相同 SKU。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="expand-a-private-cloud"></a>展開 私用雲端

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟**資源**頁面，然後選取您要擴充的私用雲端。

3. 在 [摘要] 區段中，按一下**展開**。

    ![展開 私用雲端](media/resources-expand-private-cloud.png)

4. 選擇是否要擴充您現有的叢集，或建立新的 vSphere 叢集。 當您進行變更時，會更新頁面上的摘要資訊。

    * 若要擴充您現有的叢集，按一下**展開現有叢集**。 選取您想要展開，然後輸入要新增的節點數目的叢集。 每個叢集可以有最多 16 個節點。
    * 若要新增新的叢集，請按一下**建立新叢集**。 輸入叢集的名稱。 選取現有的資料中心，或輸入名稱以建立新的資料中心。 選擇節點類型。 建立新的 vSphere 叢集時，而不是當擴充現有的 vSphere 叢集的時候，您可以選擇不同的節點類型。 選取節點的數目。 每個新的叢集必須有至少三個節點。

    ![展開 私用雲端-新增節點](media/resources-expand-private-cloud-add-nodes.png)

5. 按一下 **送出**展開私人雲端。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入了解[私用雲端](cloudsimple-private-cloud.md)