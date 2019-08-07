---
title: 建立 Azure VMware Solution by CloudSimple-服務
description: 說明如何在 Azure 入口網站中建立 CloudSimple 服務
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812437"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>建立 Azure VMware Solution by CloudSimple-服務

若要開始使用 Azure VMware Solution by CloudSimple, 請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple 服務。

## <a name="before-you-begin"></a>開始之前

為閘道子網配置/28 CIDR 區塊。  每個 CloudSimple 服務都需要閘道子網, 而且對其建立所在的區域而言是唯一的。 閘道子網用於邊緣網路服務, 且需要/28 CIDR 區塊。 閘道子網位址空間必須是唯一的。 它不得與任何與 CloudSimple 環境通訊的網路重迭。  與 CloudSimple 通訊的網路包括內部部署網路和 Azure 虛擬網路。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="create-the-service"></a>建立服務

1. 選取 [所有服務]。

2. 搜尋**CloudSimple Services**。

    ![搜尋 CloudSimple 服務](media/create-cloudsimple-service-search.png)

3. 選取 [ **CloudSimple 服務**]。

4. 按一下 [**新增**] 以建立新的服務。

    ![新增 CloudSimple 服務](media/create-cloudsimple-service-add.png)

5. 選取您要在其中建立 CloudSimple 服務的訂用帳戶。

6. 選取服務的資源群組。 若要新增新的資源群組,請按一下 [新建]。

7. 輸入名稱以識別服務。

8. 輸入服務閘道的 CIDR。 指定/28 子網, 而不會與任何現有的子網重迭。  這些包括內部部署子網、Azure 子網或任何規劃的 CloudSimple 子網。 建立服務之後, 您就無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]。

隨即建立服務, 並將其新增至服務清單。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立私用雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 瞭解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
