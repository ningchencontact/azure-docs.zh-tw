---
title: 透過 CloudSimple 私用雲端縮減 Azure VMware 解決方案
description: 說明如何壓縮 CloudSimple 私用雲端。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 67bb7e0cd8a800c2c2ba87c768739fd573ef6888
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812200"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>壓縮 CloudSimple 私用雲端

CloudSimple 提供動態縮減私用雲端的彈性。  私人雲端是由一或多個 vSphere 叢集所組成。 每個叢集可以有3到16個節點。 縮小私人雲端時, 您可以從現有的叢集中移除節點, 或刪除整個叢集。 

## <a name="before-you-begin"></a>開始之前

若要壓縮私人雲端, 必須符合下列條件。  無法刪除建立私人雲端時所建立的管理叢集 (第一個叢集)。

* VSphere 叢集必須有三個節點。  只有三個節點的叢集無法壓縮。
* 已耗用的總儲存體不應超過叢集壓縮後的總容量。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="shrink-a-private-cloud"></a>縮小私人雲端 

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面。

3. 按一下您要壓縮的私人雲端

4. 在 [摘要] 頁面上, 按一下 [**壓縮**]。

    ![壓縮私用雲端](media/shrink-private-cloud.png)

5. 選取您想要壓縮或刪除的叢集。 

    ![壓縮私用雲端-選取叢集](media/shrink-private-cloud-select-cluster.png)

6. 選取 [**移除一個節點**] 或 **[刪除整個**叢集]。 

7. 驗證叢集容量

8. 按一下 [**提交**] 以縮小私人雲端。

開始壓縮私用雲端。  您可以在 [工作] 中監視進度。  壓縮程式可能需要幾個小時的時間, 視資料而定, 這需要在 vSAN 上 resynced。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[私人](cloudsimple-private-cloud.md)雲端