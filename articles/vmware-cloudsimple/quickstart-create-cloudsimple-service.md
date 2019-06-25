---
title: Azure 的 VMware 解決方案 CloudSimple 快速入門-建立服務
description: 了解如何建立 CloudSimple 服務中，佈建節點，並保留節點
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164211"
---
# <a name="quickstart---create-service"></a>快速入門-建立服務

若要開始，請在 Azure 入口網站中建立 CloudSimple Azure VMware 解決方案。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 的解決方案，由 CloudSimple-服務概觀

CloudSimple 服務可讓您取用 Azure CloudSimple VMware 方案。  建立此服務可讓您將佈建節點保留節點，以及建立私人雲端。  您可以新增 CloudSimple 服務 CloudSimple 服務可使用的每個 Azure 區域中。  服務定義 CloudSimple Azure VMware 解決方案的 edge 網路。  此部署邊緣網路使用服務，包括 VPN、 ExpressRoute 和網際網路連線到您的私人雲端。

若要新增 CloudSimple 服務，您必須建立閘道子網路。 閘道子網路可用於建立緣網路時，而且需要/28 的 CIDR 區塊。 閘道子網路位址空間必須是唯一的。 它不能與任何內部部署網路位址空間或 Azure 虛擬網路位址空間重疊。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>啟用 Microsoft.VMwareCloudSimple 資源提供者

請遵循下列步驟來啟用 CloudSimple 服務的資源提供者。

1. 選取 [所有服務]  。
2. 搜尋並選取**訂用帳戶**。

    ![選取訂用帳戶](media/cloudsimple-service-select-subscriptions.png)

3. 選取您想要啟用 CloudSimple 服務所在的訂用帳戶
4. 按一下 **資源提供者**訂用帳戶
5. 使用**Microsoft.VMwareCloudSimple**篩選資源提供者
6. 選取  **Microsoft.VMwareCloudSimple**資源提供者，然後按一下 **註冊**

    ![註冊資源提供者](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]  。
2. 搜尋**CloudSimple 服務**。

    ![搜尋 CloudSimple 服務](media/create-cloudsimple-service-search.png)

3. 選取  **CloudSimple 服務**。
4. 按一下 **新增**來建立新的服務。

    ![新增 CloudSimple 服務](media/create-cloudsimple-service-add.png)

5. 選取您想要用來建立 CloudSimple 服務的訂用帳戶。
6. 選取服務的資源群組。 若要新增新的資源群組，請按一下**新建**。
7. 輸入名稱來識別服務。
8. 輸入 CIDR 服務閘道。 指定/28 未與任何內部部署子網路、 Azure 子網路，或計劃的 CloudSimple 子網路重疊的子網路。 建立服務之後，您無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]  。

建立服務，並新增至服務的清單。

## <a name="provision-nodes"></a>佈建節點

若要設定-付移容量 CloudSimple 私用雲端環境，您必須先佈建 Azure 入口網站中的節點。

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

* [建立私用雲端，並設定環境](quickstart-create-private-cloud.md)
* 深入了解[CloudSimple 服務](https://docs.azure.cloudsimple.com/cloudsimple-service)
