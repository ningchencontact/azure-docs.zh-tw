---
title: Azure VMware Solution by CloudSimple-建立 CloudSimple 服務
description: 說明如何在 Azure 入口網站中建立 CloudSimple 服務
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f589adf5cbcd04b9642dfe1362fd13d5be1f9aa
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640641"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>依 CloudSimple 服務建立 Azure VMware 解決方案

若要開始使用 Azure VMware Solution by CloudSimple, 請在 Azure 入口網站中建立 Azure VMware Solution by CloudSimple 服務。

> [!IMPORTANT]
> 建立 CloudSimple 服務之前, 您必須先在您的 Azure 訂用帳戶上註冊 VMwareCloudSimple 資源提供者。 遵循在您的[Azure 訂用帳戶上啟用 VMwareCloudSimple 資源提供者](enable-cloudsimple-service.md)中的步驟。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](http://portal.azure.com)。

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
8. 輸入服務閘道的 CIDR。 指定/28 子網, 而不會與任何內部部署子網、Azure 子網或規劃的 CloudSimple 子網重迭。 建立服務之後, 您就無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)
9. 按一下 [確定]。

隨即建立服務, 並將其新增至服務清單。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立私用雲端](create-private-cloud.md)
* 瞭解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
