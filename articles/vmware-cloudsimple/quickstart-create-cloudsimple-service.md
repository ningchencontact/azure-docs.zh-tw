---
title: Azure VMware Solution by CloudSimple 快速入門-建立服務
description: 瞭解如何建立 CloudSimple 服務、購買節點和保留節點
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574520"
---
# <a name="quickstart---create-cloudsimple-service"></a>快速入門-建立 CloudSimple 服務

若要開始使用, 請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple。

CloudSimple 服務可讓您使用 CloudSimple 的 Azure VMware 解決方案。  建立服務可讓您購買節點、保留節點, 以及建立私人雲端。  您會在可使用 CloudSimple 服務的每個 Azure 區域中新增 CloudSimple 服務。  服務會依 CloudSimple 定義 Azure VMware 解決方案的邊緣網路。  此 edge 網路適用于包含 VPN、ExpressRoute 和網際網路連線的服務, 可連接到您的私人雲端。

若要新增 CloudSimple 服務, 您必須建立閘道子網。 建立 edge 網路時, 會使用閘道子網, 而且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不能與您的任何內部部署網路位址空間或 Azure 虛擬網路位址空間重迭。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>啟用 VMwareCloudSimple 資源提供者

請遵循下列步驟來啟用 CloudSimple 服務的資源提供者。

1. 選取 [所有服務]。
2. 搜尋並選取 [**訂閱**]。

    ![選取訂用帳戶](media/cloudsimple-service-select-subscriptions.png)

3. 選取您要啟用 CloudSimple 服務的訂用帳戶。
4. 按一下訂用帳戶的 [**資源提供者**]。
5. 使用**VMwareCloudSimple**來篩選資源提供者。
6. 選取**VMwareCloudSimple**資源提供者, 然後按一下 [**註冊**]。

    ![註冊資源提供者](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 服務**。

    ![搜尋 CloudSimple 服務](media/create-cloudsimple-service-search.png)

3. 選取 [ **CloudSimple 服務**]。
4. 按一下 [**新增**] 以建立新的服務。

    ![新增 CloudSimple 服務](media/create-cloudsimple-service-add.png)

5. 選取您要在其中建立 CloudSimple 服務的訂用帳戶。
6. 選取服務的資源群組。 若要新增新的資源群組,請按一下 [新建]。
7. 輸入名稱以識別服務。
8. 輸入服務閘道的 CIDR。 指定/28 子網, 而不會與任何內部部署子網、Azure 子網或規劃的 CloudSimple 子網重迭。 建立服務之後, 您就無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]。

隨即建立服務, 並將其新增至服務清單。

## <a name="purchase-nodes"></a>購買節點

若要設定 CloudSimple 私用雲端環境的隨用隨付容量, 請先在 Azure 入口網站中布建節點。

1. 選取 [所有服務]。
2. 搜尋**CloudSimple 節點**。

    ![搜尋 CloudSimple 節點](media/create-cloudsimple-node-search.png)

3. 選取 [ **CloudSimple 節點**]。
4. 按一下 [**新增**] 以建立節點。

    ![新增 CloudSimple 節點](media/create-cloudsimple-node-add.png)

5. 選取您要在其中購買 CloudSimple 節點的訂用帳戶。
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

* [建立私用雲端及設定環境](quickstart-create-private-cloud.md)
* 深入瞭解[CloudSimple 服務](cloudsimple-service.md)
