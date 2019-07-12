---
title: 管理 Azure 地圖服務中的驗證 | Microsoft Docs
description: 您可以使用 Azure 入口網站來管理 Azure 地圖服務中的驗證。
author: walsehgal
ms.author: v-musehg
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1dc63a2c2350ad4f1d02d0c1b22050293d7b866c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837821"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

建立 Azure 地圖服務帳戶之後，用戶端識別碼和金鑰會建立以支援 Azure Active Directory (Azure AD) 或共用金鑰驗證。

## <a name="view-authentication-details"></a>檢視驗證詳細資料

您可以在 Azure 入口網站中檢視驗證詳細資料。 移至您的帳戶並選取**驗證**上**設定**功能表。

![驗證詳細資料](./media/how-to-manage-authentication/how-to-view-auth.png)

 若要進一步了解，請參閱[使用 Azure 地圖服務進行驗證](https://aka.ms/amauth)。


## <a name="set-up-azure-ad-app-registration"></a>設定 Azure AD 應用程式註冊

建立 Azure 地圖服務帳戶之後，您必須建立 Azure AD 租用戶與 Azure 地圖服務資源之間的連結。

1. 請移至 Azure AD 刀鋒視窗，並建立應用程式註冊。 提供註冊所需的名稱。 在 **登入 URL**方塊中，提供首頁上的 web 應用程式 / API (例如 https:\//localhost/)。 如果您已註冊的應用程式，請移至步驟 2。

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

    ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

2. 若要將委派的 API 權限指派給 Azure 地圖服務中，移至的應用程式**應用程式註冊**，然後選取**設定**。  選取 **必要的權限**，然後選取**新增**。 搜尋並選取**Azure Maps**下方**選取 API**，然後選取**選取** 按鈕。

    ![應用程式的 API 權限](./media/how-to-manage-authentication/app-permissions.png)

3. 底下**選取權限**，選取**存取 Azure Maps**，然後選取**選取** 按鈕。

    ![選取應用程式的 API 權限](./media/how-to-manage-authentication/select-app-permissions.png)

4. 完成步驟或 b，根據您的驗證方法。

    1. 如果您的應用程式會使用 Azure 地圖服務 Web SDK 中的使用者權杖驗證，啟用`oauthEnableImplicitFlow`應用程式註冊詳細資料頁面的資訊清單區段中將它設定為 true。
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果您的應用程式使用伺服器/應用程式驗證，請移至**金鑰** 刀鋒視窗中的應用程式註冊並建立密碼，或將公開金鑰憑證上傳至應用程式註冊。 如果您選取之後，您會建立密碼，**儲存**、 供稍後複製密碼，並安全地儲存它。 若要取得 Azure AD 的權杖，您將使用此密碼。

       ![應用程式金鑰](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>將 RBAC 授與給 Azure 地圖服務

您與 Azure AD 租用戶產生關聯的 Azure 地圖服務帳戶之後，您可以藉由指定使用者、 群組或應用程式以一或多個 Azure 地圖服務的存取控制角色來授與存取控制。

1. 移至**存取控制 （iam） 分頁**，選取**角色指派**，然後選取**新增角色指派**。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [**新增角色指派**] 視窗底下**角色**，選取**Azure Maps 日期讀者 （預覽）** 。 在 [存取權指派對象為]  底下，選取 [Azure AD 使用者、群組或服務主體]  。 底下**選取**，選取使用者或應用程式。 選取 [ **儲存**]。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

若要檢視適用於 Azure 地圖服務的角色型存取控制 (RBAC) 角色，請前往**存取控制 (IAM)** ，選取**角色**，然後搜尋角色開頭和**Azure Maps**. 這些是您可以授與存取權的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>檢視 Azure 地圖服務 RBAC

RBAC 可提供更細微的存取控制。

若要檢視使用者和已被授與 RBAC 的 Azure 地圖服務的應用程式，請前往**存取控制 (IAM)** ，選取**角色指派**，然後篩選**Azure Maps**。

![檢視使用者和應用程式授與 RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

您註冊您的應用程式與它相關聯 Azure 地圖服務之後，您可以要求存取權杖。

* 如果您的應用程式會使用 Azure 地圖服務 Web SDK 中的使用者權杖驗證，您需要設定您的 HTML 頁面，使用 Azure 地圖服務用戶端識別碼和 Azure AD 應用程式識別碼。

* 如果您的應用程式使用伺服器/應用程式驗證，您需要向 Azure AD 登入端點要求權杖`https://login.microsoftonline.com`的 Azure AD 資源識別碼`https://atlas.microsoft.com/`，Azure 地圖服務用戶端識別碼、 Azure AD 應用程式識別碼和 Azure AD 應用程式註冊密碼或憑證。

如需有關如何從 Azure AD 使用者和服務主體的要求存取權杖的詳細資訊，請參閱[Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure AD 驗證和 Azure 地圖服務 Web SDK，請參閱 [Azure AD 和 Azure 地圖服務 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

了解如何查看 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"] 
> [檢視使用計量](how-to-view-api-usage.md)