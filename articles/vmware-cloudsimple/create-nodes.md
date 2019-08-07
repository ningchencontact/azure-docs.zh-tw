---
title: CloudSimple 的 VMware 解決方案布建節點-Azure
description: 瞭解如何使用 CloudSimple 部署將節點新增至 VMWare
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9eb02f04b5873e5906782a27ce833a724ceecfe3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812373"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>CloudSimple 的 VMware 解決方案布建節點-Azure

在 Azure 入口網站中布建節點。 接著, 您可以為 CloudSimple 私人雲端環境設定隨用隨付容量。

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>將布建的節點新增至您的 CloudSimple 私人雲端

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 節點**。

   ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。
4. 按一下 [**新增**] 以建立節點。

    ![新增 CloudSimple 節點](media/create-cloudsimple-node-add.png)

5. 選取您要布建 CloudSimple 節點的訂用帳戶。
6. 選取節點的資源群組。 若要新增新的資源群組,請按一下 [新建]。
7. 輸入前置詞以識別節點。
8. 選取節點資源的 [位置]。
9. 選取要裝載節點資源的專用位置。
10. 選取節點類型。 您可以選擇 [ [CS28] 或 [CS36] 選項](cloudsimple-node.md)。 第二個選項包含最大計算和記憶體容量。
11. 選取要布建的節點數目。
12. 選取 [檢閱 + 建立]。
13. 檢查設定。 若要修改任何設定, 請按一下 [**上一步**]。
14. 選取 [建立]。

## <a name="next-steps"></a>後續步驟

* [建立私用雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
