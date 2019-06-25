---
title: VMware CloudSimple-Azure 解決方案的佈建節點
description: 了解如何將節點新增至您的 VMWare CloudSimple 部署
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165262"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>VMware CloudSimple-Azure 解決方案的佈建節點

在 Azure 入口網站中佈建節點。 然後您可以設定-付移容量 CloudSimple 私用雲端環境。

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>將佈建的節點加入 CloudSimple 私用雲端

1. 選取 [所有服務]  。
2. 搜尋**CloudSimple 節點**。

   ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取  **CloudSimple 節點**。
4. 按一下 **新增**建立節點。

    ![新增 CloudSimple 節點](media/create-cloudsimple-node-add.png)

5. 選取您想要佈建 CloudSimple 節點的訂用帳戶。
6. 選取節點的資源群組。 若要新增新的資源群組，請按一下**新建**。
7. 輸入要識別的節點的前置詞。
8. 選取的節點資源的位置。
9. 選取要裝載節點資源的固定的位置。
10. 選取的節點型別。 您可以選擇[CS28 或 CS36 選項](cloudsimple-node.md)。 後面這個選項會包含最大的運算和記憶體容量。
11. 選取要佈建的節點數目。
12. 選取 [檢閱 + 建立]  。
13. 檢閱設定。 若要修改任何設定，請按一下**Previous**。
14. 選取 [建立]  。

## <a name="next-steps"></a>後續步驟

* [建立私用雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
