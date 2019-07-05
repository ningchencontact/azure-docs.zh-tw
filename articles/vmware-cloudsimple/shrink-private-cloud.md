---
title: 壓縮 CloudSimple 私用雲端的 Azure 的 VMware 解決方案
description: 描述如何壓縮 CloudSimple 私用雲端。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544513"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>壓縮 CloudSimple 私用雲端

CloudSimple 提供動態壓縮私用雲端的彈性。  私用雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有 3 到 16 個節點。 當壓縮私用雲端，您會從現有的叢集移除節點，或刪除整個叢集。 

## <a name="before-you-begin"></a>開始之前

壓縮功能的私用雲端時，必須先符合下列條件。  管理叢集 （第一個叢集） 建立私人雲端建立時無法刪除。

* 在 vSphere 叢集必須有三個節點。  無法壓縮具有只有三個節點的叢集。
* 取用的儲存體總計不應該超過的總容量的叢集中的壓縮後。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="shrink-a-private-cloud"></a>壓縮私用雲端 

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟**資源**頁面。

3. 按一下您想要壓縮的私用雲端

4. 在 摘要 頁面中，按一下 **壓縮**。

    ![壓縮的私用雲端](media/shrink-private-cloud.png)

5. 選取您想要壓縮或刪除叢集。 

    ![壓縮私用雲端-選取的叢集](media/shrink-private-cloud-select-cluster.png)

6. 選取 **移除一個節點**或是**刪除整個叢集**。 

7. 請確認叢集容量

8. 按一下 **送出**壓縮私用雲端。

壓縮的私用雲端會開始。  您可以監視工作的進度。  壓縮程序可能需要幾個小時，根據的資料，必須 resynced 上 vSAN。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入了解[私用雲端](cloudsimple-private-cloud.md)