---
title: 建立 Azure VMware CloudSimple-服務方案
description: 描述如何在 Azure 入口網站中建立 CloudSimple 服務
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154955"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>建立 Azure 的 VMware 解決方案由 CloudSimple-服務

若要開始使用 Azure 的 VMware 解決方案 CloudSimple，CloudSimple 服務在 Azure 入口網站中建立 Azure VMware 的解決方案。

> [!NOTE]
> 建立 CloudSimple 服務之前，您必須在您的 Azure 訂用帳戶註冊 Microsoft.VMwareCloudSimple 資源提供者。 請依照下列中的步驟[啟用 Microsoft.VMwareCloudSimple 資源提供者，您的 Azure 訂用帳戶](enable-cloudsimple-service.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

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

8. 輸入 CIDR 服務閘道。 指定/28 子網路不會重疊任何您現有的子網路。  這些包括內部部署子網路，Azure 的子網路，或任何計劃 CloudSimple 子網路。 建立服務之後，您無法變更 CIDR。

    ![建立 CloudSimple 服務](media/create-cloudsimple-service.png)

9. 按一下 [確定]  。

建立服務，並新增至服務的清單。

## <a name="next-steps"></a>後續步驟

* 了解如何[建立私人雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
