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
ms.openlocfilehash: 2f4a3d791e6b5d6ff20c09408d1a0bf5995c32fd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756567"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

建立 Azure 地圖服務帳戶之後，會建立用戶端識別碼和金鑰，以支援 Azure Active Directory （Azure AD）或共用金鑰驗證。

## <a name="view-authentication-details"></a>檢視驗證詳細資料

您可以在 Azure 入口網站上查看驗證詳細資料。 移至您的帳戶，然後選取 [**設定**] 功能表上的 [**驗證**]。

![驗證詳細資料](./media/how-to-manage-authentication/how-to-view-auth.png)

 若要深入瞭解，請參閱[使用 Azure 地圖服務進行驗證](https://aka.ms/amauth)。


## <a name="set-up-azure-ad-app-registration"></a>設定 Azure AD 應用程式註冊

建立 Azure 地圖服務帳戶之後，您需要在您的 Azure AD 租使用者與 Azure 地圖服務資源之間建立連結。

1. 移至 [Azure AD] 分頁，並建立應用程式註冊。 提供註冊的名稱。 在 [登入**URL** ] 方塊中，提供 web 應用程式/API 的首頁（例如，HTTPs： \//localhost/）。 如果您已經有已註冊的應用程式，請移至步驟2。

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

    ![應用程式註冊詳細資料](./media/how-to-manage-authentication/app-create.png)

2. 若要將委派的 API 許可權指派給 Azure 地圖服務，請移至**應用程式註冊**底下的應用程式，然後選取 [**設定**]。  選取 [**必要許可權**]，然後選取 [**新增**]。 搜尋並選取 [**選取 API**] 底下的**Azure 地圖服務**，然後選取 [**選取**] 按鈕。

    ![應用程式 API 許可權](./media/how-to-manage-authentication/app-permissions.png)

3. 在 [**選取許可權**] 底下，選取 [**存取 Azure 地圖服務**]，然後選取 [**選取**] 按鈕。

    ![選取應用程式 API 許可權](./media/how-to-manage-authentication/select-app-permissions.png)

4. 完成步驟 a 或 b，視您的驗證方法而定。

    1. 如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，請在應用程式註冊詳細資料頁面的 [資訊清單] 區段中，將其設定為 true，以啟用 `oauthEnableImplicitFlow`。
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果您的應用程式使用伺服器/應用程式驗證，請移至應用程式註冊中的 [**金鑰**] 分頁，並建立密碼或將公開金鑰憑證上傳至應用程式註冊。 如果您建立密碼，請在選取 [**儲存**] 之後複製密碼以供稍後使用，並將其安全地儲存。 您將使用此密碼從 Azure AD 取得權杖。

       ![應用程式金鑰](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>將 RBAC 授與給 Azure 地圖服務

將 Azure 地圖服務帳戶與您的 Azure AD 租使用者建立關聯之後，您可以將使用者、群組或應用程式指派給一或多個 Azure 地圖服務存取控制角色，以授與存取控制。

1. 移至 **[存取控制（IAM）** ]，選取 [**角色指派**]，然後選取 [**新增角色指派**]。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [**新增角色指派**] 視窗的 [**角色**] 底下，選取 **[Azure 地圖服務日期讀取器（預覽）** ]。 在 [存取權指派對象為] 底下，選取 [Azure AD 使用者、群組或服務主體]。 在 [**選取**] 底下，選取使用者或應用程式。 選取 [儲存]。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

若要查看可用於 Azure 地圖服務的角色型存取控制（RBAC）角色，請移至 **[存取控制（IAM）** ]，選取 [**角色**]，然後搜尋以**Azure 地圖服務**開頭的角色。 這些是您可以授與存取權的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure 地圖服務 RBAC 的觀點

RBAC 提供更細微的存取控制。

若要查看已為 Azure 地圖服務授與 RBAC 的使用者和應用程式，請移至 **[存取控制（IAM）** ]，選取 [**角色指派**]，然後依**Azure 地圖服務**進行篩選。

![查看已授與 RBAC 的使用者和應用程式](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

註冊應用程式並將它與 Azure 地圖服務相關聯之後，您就可以要求存取權杖。

* 如果您的應用程式使用 Azure 地圖服務 Web SDK 的使用者權杖驗證，您必須使用 Azure 地圖服務用戶端識別碼和 Azure AD 應用程式識別碼來設定您的 HTML 網頁。

* 如果您的應用程式使用伺服器/應用程式驗證，您需要向 Azure AD 登入端點 `https://login.microsoftonline.com` 要求權杖，其 Azure AD 資源識別碼 `https://atlas.microsoft.com/`、Azure 地圖服務用戶端識別碼、Azure AD 應用程式識別碼，以及 Azure AD 應用程式註冊密碼或證書.

如需從 Azure AD 針對使用者和服務主體要求存取權杖的詳細資訊，請參閱[Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure AD 驗證和 Azure 地圖服務 Web SDK，請參閱 [Azure AD 和 Azure 地圖服務 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

了解如何查看 Azure 地圖服務帳戶的 API 使用計量：
> [!div class="nextstepaction"] 
> [檢視使用計量](how-to-view-api-usage.md)

如需顯示如何整合 Azure Active Directory （AAD）與 Azure 地圖服務的範例清單，請參閱：

> [!div class="nextstepaction"]
> [AAD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)