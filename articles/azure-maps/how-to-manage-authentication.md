---
title: 管理 Azure 地圖服務中的驗證 | Microsoft Docs
description: 您可以使用 Azure 入口網站來管理 Azure 地圖服務中的驗證。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242680"
---
# <a name="manage-authentication-in-azure-maps"></a>管理 Azure 地圖服務中的驗證

在您建立 Azure 地圖服務帳戶之後，系統會建立用戶端識別碼和金鑰，以支援 Azure Active Directory (Azure AD) 或共用金鑰驗證。

您可以在 Azure 入口網站中瀏覽至 [設定] 下方的 [驗證] 頁面，尋找您的驗證詳細資料。 瀏覽至您的帳戶。 然後在左側功能表中選取 [驗證]。


## <a name="view-authentication-details"></a>檢視驗證詳細資料

若要檢視驗證詳細資料，請瀏覽至 [設定] 功能表中的 [驗證] 選項。

![檢視驗證](./media/how-to-manage-authentication/how-to-view-auth.png)

 若要深入了解驗證和 Azure 地圖服務，請參閱 [Azure 地圖服務驗證](https://aka.ms/amauth)。


## <a name="configure-azure-ad-app-registration"></a>設定 Azure AD 應用程式註冊

建立 Azure 地圖服務帳戶之後，Azure AD 租用戶與 Azure 地圖服務的 Azure 資源之間需要有連結。 

1. 移至 Azure AD 刀鋒視窗並建立應用程式註冊，以其名稱和登入 URL 作為 Web 應用程式 / API 的首頁，例如 "https://localhost/"。 如果您已經有註冊的應用程式，請繼續步驟 2。

    ![應用程式註冊](./media/how-to-manage-authentication/app-registration.png)

    ![應用程式註冊](./media/how-to-manage-authentication/app-create.png)

2. 瀏覽至 [應用程式註冊] 之下的應用程式，將委派的 API 權限指派給 Azure 地圖服務，然後按一下 [設定]。  選取 [必要權限]，然後選取 [新增]。 搜尋而後在 [選取 API] 之下選取 Azure 地圖服務，然後按一下 [選取]。

    ![應用程式 API 權限](./media/how-to-manage-authentication/app-permissions.png)

3. 最後，在 [選取權限] 之下選擇 [存取 Azure 地圖服務]，然後按一下 [選取]。

    ![選取應用程式 API 權限](./media/how-to-manage-authentication/select-app-permissions.png)

4. 視驗證實作而定，依照步驟 a 或 b 操作。

    1. 如果應用程式想要使用使用者權杖驗證搭配 Azure 地圖服務 Web SDK，您必須在應用程式註冊詳細資料頁面的 [資訊清單] 區段中將 `oauthEnableImplicitFlow` 設定為 true。
    
       ![應用程式資訊清單](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果您的應用程式使用伺服器/應用程式驗證，請移至應用程式註冊內的 [金鑰] 刀鋒視窗，建立密碼或將公開金鑰憑證上傳至應用程式註冊。 如果您建立密碼，一旦**儲存**複製密碼以供稍後使用，並安全地儲存後，您就會使用此密碼向 Azure AD 取得權杖。

       ![應用程式金鑰](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>將 RBAC 授與給 Azure 地圖服務

在 Azure 地圖服務帳戶與 Azure AD 租用戶產生關聯後，將使用者或應用程式指派給可用的 Azure 地圖服務存取控制角色，即可授與存取控制。

1. 瀏覽至 [存取控制] 選項，依序按一下 [角色指派] 和 [新增角色指派]。

    ![授與 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 [角色指派] 快顯視窗上，選取 Azure 地圖服務日期讀取者 (預覽) **角色**，**將存取權指派給**：Azure AD 使用者、群組或服務主體，從下拉式清單選取 [使用者] 或 [應用程式]，然後選取 [儲存]。

    ![新增角色指派](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>檢視可用的 Azure 地圖服務 RBAC 角色

若要檢視 Azure 地圖服務可用的角色型存取控制角色 (可獲取存取權)，請瀏覽至 [存取控制 (IAM)] 選項，按一下 [角色]，然後搜尋以 **Azure 地圖服務**開頭的角色。

![檢視可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>檢視 Azure 地圖服務角色型存取控制 (RBAC)

Azure AD 允許透過角色型存取控制 (RBAC) 進行細微的存取控制。 

若要檢視已獲取 Azure 地圖服務 RBAC 的使用者或應用程式，請瀏覽至 [存取控制 (IAM)] 選項，選取 [角色指派]，並依照 **Azure 地圖服務**篩選。 所有可用的角色都會出現在下面。

![檢視 RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>要求 Azure 地圖服務的權杖

您的應用程式向 Azure 地圖服務註冊並與其產生關聯後，您現在即可要求存取權杖。

* 如果應用程式想要使用使用者權杖驗證搭配 Azure 地圖服務 Web SDK (Web)，您需要使用 Azure 地圖服務用戶端識別碼和 Azure AD 應用程式識別碼來設定 html 頁面。

* 針對使用伺服器/應用程式驗證的應用程式，使用 Azure AD 資源識別碼 `https://atlas.microsoft.com/`、Azure 地圖服務用戶端識別碼、Azure AD 應用程式識別碼及 Azure AD 應用程式註冊密碼或憑證，向 Azure AD 登入端點 `https://login.microsoftonline.com` 要求權杖。

如需針對使用者和服務主體向 Azure AD 要求存取權杖的詳細資訊，請參閱 [Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure AD 驗證和 Azure 地圖服務 Web SDK，請參閱 [Azure AD 和 Azure 地圖服務 Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。