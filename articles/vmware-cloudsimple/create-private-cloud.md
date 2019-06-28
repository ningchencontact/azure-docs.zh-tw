---
title: 建立 Azure 的 VMware 解決方案 CloudSimple 私用雲端
description: 描述如何建立以擴充到雲端的 VMware 工作負載運作的彈性和持續性 CloudSimple 私用雲端
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332883"
---
# <a name="create-a-cloudsimple-private-cloud"></a>建立 CloudSimple 私用雲端

建立私人雲端，可協助您解決各種網路基礎結構的一般需求：

* **成長**。 如果您達到您現有的基礎結構的硬體重新整理點之後，私用雲端可讓您擴充所需的任何新的硬體投資。

* **快速擴充**。 如果發生時，需要任何暫存或非計劃性的容量，私用雲端可讓您建立額外的容量，而且毫無延遲。

* **增加保護**。 透過三個或多個節點的私用雲端，您會取得自動備援與高可用性保護。

* **長期的基礎結構需求**。 如果您的資料中心容量或您想要重新建構來降低成本，私用雲端可讓您淘汰資料中心，並移轉至雲端式解決方案同時保有相容與您企業的作業。

當您建立私用雲端時，您可以取得單一 vSphere 叢集和所有管理該叢集中所建立的 Vm。

## <a name="before-you-begin"></a>開始之前

您可以建立私用雲端之前，必須佈建節點。  如需有關如何佈建節點的詳細資訊，請參閱[佈建的 VMware 的解決方案，由 CloudSimple-Azure 節點](create-nodes.md)文章。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

存取權[CloudSimple 入口網站](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>建立新的私用雲端

1. 在 **資源**頁面上，按一下**新的私用雲端**。

    ![建立私人雲端-如何開始](media/create-pc-button.png)

2. 選取要裝載的私用雲端資源的位置。

3. 選擇 CS28 或 CS36 節點型別 you'ev 私用雲端佈建。 後面這個選項會包含最大的運算和記憶體容量。

4. 私用雲端中選取節點的數目。 您可以選取最多可用的節點數目，you'ev[佈建](create-nodes.md)。

    ![建立私用雲端-基本設定](media/create-private-cloud-basic-info.png)

5. 按一下 **下一步進階選項**。

6. 輸入 vSphere/vSAN 子網路的 CIDR 範圍。 請確定與任何您內部部署或其他 Azure 子網路 （虛擬網路），或與閘道子網路的 CIDR 範圍不能重疊。  請勿使用任何 Azure 虛擬網路上定義的 CIDR 範圍。
    
    **CIDR 範圍選項：**  /24、 / 23，/22 部或 /21。 A/24 的 CIDR 範圍支援最多 9 個節點，/23 CIDR 範圍最多支援 41 節點和 / 22 部 /21 CIDR 範圍支援最多 64 個節點 （私用雲端中的節點的數目上限）。

    > [!CAUTION]
    > 私用雲端基礎結構，vSphere/vSAN CIDR 範圍中的 IP 位址被保留供使用。  請勿在此範圍內，任何虛擬機器上使用的 IP 位址。

7. 按一下 **下一步檢閱並建立**。

8. 檢閱設定。 如果您需要變更任何設定，按一下**Previous**。

9. 按一下頁面底部的 [新增]  。

一旦您按一下，佈建的私用雲端就會啟動建立。  您可以監視從進度[任務](https://docs.azure.cloudsimple.com/activity/#tasks)CloudSimple 入口網站頁面上的。  佈建可能需要 30 分鐘到兩個小時的時間。  佈建完成之後，您會收到一封電子郵件。

## <a name="next-steps"></a>後續步驟

* [展開 私用雲端](expand-private-cloud.md)