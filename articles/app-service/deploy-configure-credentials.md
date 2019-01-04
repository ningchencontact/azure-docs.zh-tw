---
title: 設定部署認證 - Azure App Service | Microsoft Docs
description: 了解如何使用 Azure App Service 部署認證。
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a92440e97f47f0778eb73b81b239b45476d4e733
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550873"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>設定 Azure App Service 的部署認證
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 支援兩種認證類，用於[本機 Git 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 這些與您的 Azure Active Directory 認證不相同。

* **使用者層級認證**︰一組用於整個 Azure 帳戶的認證。 它可以用來將任何應用程式部署至 Azure 帳戶有權存取的任何訂用帳戶的 App Service 中。 其為呈現在入口網站 GUI 中的預設集合 (例如應用程式[資源頁面](../azure-resource-manager/resource-group-portal.md#manage-resources)的 [概觀] 和 [屬性])。 透過角色型存取控制 (RBAC) 或共同管理員權限來為使用者授與應用程式存取權時，該使用者可以使用自己的使用者層級認證，直到存取權遭到撤銷為止。 請勿與其他 Azure 使用者共用這些認證。

* **應用程式層級認證**︰一組用於單個 應用程式的認證。 它只可以用來部署該應用程式。 每個應用程式的認證都會在建立應用程式時自動產生。 您無法手動設定它們，但可隨時重設。 若是要透過 (RBAC) 授與應用程式層級認證存取權的使用者，該使用者就必須是應用程式上的參與者或更高權限。 讀者不允許發佈且無法存取那些認證。

## <a name="userscope"></a>設定及重設使用者層級的認證

您可以在任何應用程式的[資源頁面](../azure-resource-manager/resource-group-portal.md#manage-resources)中設定使用者層級認證。 無論在哪一個應用程式中設定這些認證，它都會套用至所有應用程式和您的 Azure 帳戶中的所有訂用帳戶。 

設定使用者層級認證：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，按一下 [應用程式服務] > **&lt;any_app>** > [部署中心] > [部署認證]。

    在入口網站中，您必須至少有一個應用程式，才能存取 [部署認證] 頁面。 不過，若使用 [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set)，沒有現有應用程式也可以設定使用者層級認證。

2. 按一下 [使用者認證]、設定使用者名稱和密碼，然後按一下 [儲存認證]。

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

一旦設定好您的部署認證，就可以在應用程式的 [概觀] 中找到「Git」部署使用者名稱，

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

以及在應用程式的 [屬性] 中找到 「FTP」部署使用者名稱。

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure 不會顯示您的使用者層級部署密碼。 如果您忘記密碼，可依照本節的步驟來重設認證。
>
>  

## <a name="appscope"></a>設定及重設應用程式層級的認證
若要取得應用程式層級的認證：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，按一下 [應用程式服務] > **&lt;any_app>** > [部署中心] > [部署認證]。

2. 按一下 [應用程式認證]，然後按一下 [複製] 連結來複製使用者名稱或密碼。

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

若要重設應用程式層級的認證，請按一下同一個對話方塊中的 [重設認證]。

## <a name="next-steps"></a>後續步驟

了解如何使用這些認證從[本機 Git](deploy-local-git.md)或使用[FTP/S](deploy-ftp.md) 部署應用程式。
