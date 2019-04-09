---
title: 在入口網站中建立 Azure App 的身分識別 | Microsoft Docs
description: 描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee12cb2a0fdd4547ffae8dfce4fddee03167e539
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257410"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>作法：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體

這篇文章會示範如何建立新的 Azure Active Directory 應用程式和服務主體，可以搭配角色型存取控制。 如果您擁有需要存取或修改資源的程式碼，則可以建立應用程式的身分識別。 此身分識別就是所謂的服務主體。 然後，您可以將必要的權限指派給服務主體。 本文說明如何使用入口網站來建立服務主體。 其中著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。 您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。

> [!IMPORTANT]
> 若不要建立服務主體，可考慮使用 Azure 資源的受控識別作為應用程式識別碼。 若您的程式碼在支援受控識別的服務上執行，且存取的資源支援 Azure Active Directory (Azure AD) 驗證，則受控識別會是更好的選擇。 若要深入了解 Azure 資源的受控識別，包含目前哪些服務支援該功能，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

## <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

讓我們直接跳到建立身分識別。 如果您遇到問題，請檢查[必要的權限](#required-permissions)，以確定您的帳戶可以建立身分識別。

1. 透過 [Azure 入口網站](https://portal.azure.com)登入您的 Azure 帳戶。
1. 選取 **Azure Active Directory**。
1. 選取 [應用程式註冊]。

   ![選取應用程式註冊](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. 選取 [新增應用程式註冊]。

   ![新增應用程式](./media/howto-create-service-principal-portal/select-add-app.png)

1. 提供應用程式的名稱和 URL。 針對您想要建立的應用程式類型，選取 [Web 應用程式/API]。 您無法建立[原生應用程式](../manage-apps/application-proxy-configure-native-client-application.md)的認證。 您無法將該類型使用於自動化應用程式。 設定值之後，選取 [建立]。

   ![名稱應用程式](./media/howto-create-service-principal-portal/create-app.png)

您已建立 Azure AD 應用程式和服務主體。

## <a name="assign-the-application-to-a-role"></a>指派角色給應用程式

若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色可提供應用程式的適當權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 瀏覽至您想要讓應用程式指派至的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請選取 [所有服務] 和 [訂用帳戶]。

   ![選取訂用帳戶](./media/howto-create-service-principal-portal/select-subscription.png)

1. 選取指派應用程式時作為對象的特定訂用帳戶。

   ![選取要指派的訂用帳戶](./media/howto-create-service-principal-portal/select-one-subscription.png)

   如果您未看見所尋找的訂用帳戶，請選取 [全域訂閱篩選]。 確定您想要的訂用帳戶已針對入口網站選取。 

1. 選取 [存取控制 (IAM)]。
1. 選取 [新增角色指派]。

   ![選取新增角色指派](./media/howto-create-service-principal-portal/select-add.png)

1. 選取您想要將應用程式指派給哪個角色。 若要允許應用程式執行動作 (例如，**重新啟動**、**開始**和**停止**執行個體)，請選取 [參與者] 角色。 根據預設，Azure AD 應用程式不會顯示在可用選項中。 若要尋找您的應用程式，請搜尋名稱並加以選取。

   ![選取角色](./media/howto-create-service-principal-portal/select-role.png)

1. 選取 [儲存] 以完成角色指派。 您在使用者清單中看到應用程式已指派給該範圍的角色。

您的服務主體已設定。 您可以開始使用它來執行指令碼或應用程式。 下節說明如何取得以程式設計方式登入時所需的值。

## <a name="get-values-for-signing-in"></a>取得值以便登入

### <a name="get-tenant-id"></a>取得租用戶識別碼

以程式設計方式登入時，您需要將租用戶識別碼與驗證要求一起傳送。

1. 選取 **Azure Active Directory**。
1. 選取 [屬性] 。

   ![選取 Azure AD 屬性](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. 複製 [目錄識別碼] 以取得您的租用戶識別碼。

   ![租用戶識別碼](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>取得應用程式識別碼和驗證金鑰

您也需要應用程式識別碼和驗證金鑰。 若要取得這些值，請使用下列步驟︰

1. 在 Azure AD 中，從 [應用程式註冊] 選取您的應用程式。

   ![選取應用程式](./media/howto-create-service-principal-portal/select-app.png)

1. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。

   ![用戶端識別碼](./media/howto-create-service-principal-portal/copy-app-id.png)

1. 選取 [Settings] \(設定) 。

   ![選取 [設定]](./media/howto-create-service-principal-portal/select-settings.png)

1. 選取 [金鑰]。
1. 提供金鑰的描述和金鑰的持續時間。 完成時，選取 [儲存]。

   ![儲存金鑰](./media/howto-create-service-principal-portal/save-key.png)

   儲存金鑰之後會顯示金鑰的值。 請複製此值，因為您之後就無法擷取金鑰。 您需要提供金鑰值和應用程式識別碼，以應用程式身分登入。 將金鑰值儲存在應用程式可擷取的地方。

   ![儲存的金鑰](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>所需的權限

您必須有足夠權限向 Azure AD 租用戶註冊應用程式，並將應用程式指派給 Azure 訂用帳戶中的角色。

### <a name="check-azure-ad-permissions"></a>檢查 Azure AD 權限

1. 選取 **Azure Active Directory**。
1. 記下您的角色。 如果您具有 [使用者] 角色，則必須確定非系統管理員可以註冊應用程式。

   ![尋找使用者](./media/howto-create-service-principal-portal/view-user-info.png)

1. 選取 [使用者設定]。

   ![選取使用者設定](./media/howto-create-service-principal-portal/select-user-settings.png)

1. 檢查 [應用程式註冊] 設定。 此值只能由系統管理員設定。 若設定為 [是]，則 Azure AD 租用戶中的任何使用者都可以註冊應用程式。

   ![檢視應用程式註冊](./media/howto-create-service-principal-portal/view-app-registrations.png)

如果應用程式註冊設定設為 [否]，則只有具備系統管理員角色的使用者才能註冊這些類型的應用程式。 若要了解 Azure AD 中可用的系統管理員角色及賦予每個角色的特定權限，請參閱[可用的角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)和[角色權限](../users-groups-roles/directory-assign-admin-roles.md#role-permissions)。 如果您的帳戶已指派給「使用者」角色，但應用程式註冊設定僅限於系統管理員使用者，請洽詢系統管理員將您指派給其中一個可建立和管理所有應用程式註冊層面的系統管理員角色，或是讓使用者能夠註冊應用程式。

### <a name="check-azure-subscription-permissions"></a>檢查 Azure 訂用帳戶權限

在您的 Azure 訂用帳戶中，您的帳戶必須具有 `Microsoft.Authorization/*/Write` 存取權，才能將 AD 應用程式指派給角色。 此動作是[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色或[使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色來授與。 如果您的帳戶已指派給 [參與者] 角色，則您沒有足夠的權限。 當您嘗試將服務主體指派給角色時，您會收到錯誤。

若要檢查訂用帳戶權限：

1. 在右上角中，選取您的帳戶，然後選取 [我的權限]。

   ![選取使用者權限](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. 從下拉式清單中，選取您想要用以建立服務主體的訂用帳戶。 然後，選取 [按一下這裡以詳細檢視此訂用帳戶的完整存取權]。

   ![尋找使用者](./media/howto-create-service-principal-portal/view-details.png)

1. 檢視指派的角色，並判斷是否有足夠的權限可將 AD 應用程式指派給角色。 如果沒有，請洽詢訂用帳戶管理員，將您新增至「使用者存取系統管理員」角色。 在下圖中，使用者已指派給「擁有者」角色，這表示該使用者具有足夠的權限。

   ![顯示權限](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>後續步驟

* 若要設定多租用戶應用程式，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](../../azure-resource-manager/resource-manager-api-authentication.md)。
* 若要了解如何指定安全策略，请参阅 [Azure 基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)。  
* 如需可授與或拒絕使用者的可用動作清單，請參閱 [Azure Resource Manager 資源提供者作業](../../role-based-access-control/resource-provider-operations.md)。
