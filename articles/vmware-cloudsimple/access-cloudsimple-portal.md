---
title: CloudSimple-入口網站存取 Azure 的 VMware 方案
description: 描述如何從 Azure 入口網站的 CloudSimple 入口網站來存取 VMware 的解決方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544122"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>從 Azure 入口網站的 CloudSimple 入口網站存取 VMware 的解決方案

單一登入存取 CloudSimple 入口網站的支援。 登入 Azure 入口網站之後，您可以存取 CloudSimple 入口網站，而不需要再次登入。 第一次存取 CloudSimple 入口網站，系統會提示您授權[CloudSimple Service 授權](#consent-to-cloudsimple-service-authorization-application)應用程式。  授權是一次性的動作。

## <a name="before-you-begin"></a>開始之前

使用內建的使用者**擁有者**並**參與者**角色可以存取 CloudSimple 入口網站。  角色必須 CloudSimple 服務部署所在的資源群組上設定。  角色也可以設定 CloudSimple 服務物件上。  如需有關如何檢查您的角色的詳細資訊，請參閱[檢視角色指派](https://docs.microsoft.com/azure/role-based-access-control/check-access)文章。

如果您使用自訂角色、 角色應該擁有任何下列作業下```Actions```。  如需有關自訂角色的詳細資訊，請參閱 <<c0> [ 適用於 Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。  如果任何作業的一部分```NotActions```，使用者無法存取 CloudSimple 入口網站。 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選取 [所有服務]  。

2. 搜尋**CloudSimple 服務**。

3. 選取您要建立私用雲端的 CloudSimple 服務。

4. 在 **概觀**頁面上，按一下**移至 CloudSimple 入口網站**。  如果您第一次，從 Azure 入口網站存取 CloudSimple 入口網站，您將會提示您授權[CloudSimple Service 授權](#consent-to-cloudsimple-service-authorization-application)應用程式。 

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

> [!TIP]
> 如果您直接從 Azure 入口網站選取私用雲端的作業 （例如建立或擴充私人雲端），CloudSimple 入口網站會開啟指定的頁面。

在 CloudSimple 入口網站中，選取**首頁**側邊功能表以顯示您的私用雲端的摘要資訊。 資源和私用雲端的容量會顯示，以及警示和需要注意的工作。 針對常見的工作，按一下位於頁面頂端的已命名的圖示。

![首頁](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple Service 授權應用程式

第一次啟動 CloudSimple 入口網站中的，從 Azure 入口網站會需要貴用戶同意 CloudSimple Service 授權應用程式。  選取 **接受**授與要求的權限，以及存取 CloudSimple 入口網站。 

![同意 CloudSimple 服務授權管理員](media/cloudsimple-azure-consent.png)

如果您有全域系統管理員權限，您可以為您的組織來同意。  選取 **代表您的組織同意**。

![CloudSimple Service 授權全域系統管理員同意](media/cloudsimple-azure-consent-global-admin.png)

如果您的權限不允許存取 CloudSimple 入口網站，請連絡您授與必要權限的租用戶的全域管理員。  全域管理員可以代表您的組織同意。

![CloudSimple Service 授權同意-需要系統管理員](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>後續步驟

* 了解如何[建立私人雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
