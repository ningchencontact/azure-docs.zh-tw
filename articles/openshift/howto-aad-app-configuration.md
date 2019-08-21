---
title: Azure Active Directory for Azure 的 Red Hat OpenShift 的整合 |Microsoft Docs
description: 了解如何建立用於測試 Microsoft Azure 的 Red Hat OpenShift 叢集上的應用程式的 Azure AD 安全性群組和使用者。
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669390"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory for Azure 的 Red Hat OpenShift 的整合

如果您尚未建立 Azure Active Directory (Azure AD) 租用戶，請依照下列中的指示[建立 Azure AD 租用戶的 Azure Red Hat OpenShift](howto-create-tenant.md)再繼續進行這些指示。

Microsoft Azure 的 Red Hat OpenShift 需要代表您的叢集執行工作的權限。 如果您的組織尚未在 Azure AD 使用者，Azure AD 安全性群組或做為服務主體的 Azure AD 應用程式註冊，請遵循這些指示來建立它們。

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者

在  [Azure 入口網站](https://portal.azure.com)，確保您的租用戶，會出現在右上角您的使用者名稱下入口網站的權限：

![與租用戶入口網站的螢幕擷取畫面示右上方](./media/howto-create-tenant/tenant-callout.png)如果顯示錯誤的租用戶，請按一下右上方，使用者名稱，然後按一下 **切換目錄**，然後選取正確的租用戶從**所有目錄**清單。

建立新的 Azure Active Directory 全域管理員使用者來登入您的 Azure Red Hat OpenShift 叢集。

1. 移至[使用者所有使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)刀鋒視窗。
2. 按一下  **+ 新增使用者**來開啟**使用者**窗格。
3. 請輸入**名稱**這位使用者。
4. 建立**使用者名**根據您所建立的租用戶名稱與`.onmicrosoft.com`附加在結尾。 例如： `yourUserName@yourTenantName.onmicrosoft.com` 。 記下此使用者名稱。 您將需要它來登入您的叢集。
5. 按一下 **目錄角色**以開啟 目錄角色 窗格中，選取**全域系統管理員**，然後按一下  **Ok**窗格的底部。
6. 在 **使用者**窗格中，按一下**顯示密碼**和錄製的暫時密碼。 登入第一次之後，系統會提示您重設它。
7. 在窗格的底部，按一下**建立**來建立使用者。

## <a name="create-an-azure-ad-security-group"></a>建立 Azure AD 安全性群組

若要授與叢集系統管理員存取權，請在 Azure AD 安全性群組中的成員資格都會同步處理到 OpenShift 群組 「 osa-客戶-系統管理員 」。 如果未指定，就會授不與任何叢集系統管理員存取權。

1. 開啟[Azure Active Directory 群組](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)刀鋒視窗。
2. 按一下  **+ 新增群組**。
3. 提供群組名稱和描述。
4. 設定**群組類型**要**安全性**。
5. 設定**成員資格類型**要**指派**。

    新增您在此安全性群組的先前步驟中建立 Azure AD 使用者。

6. 按一下 **成員**來開啟**選取成員**窗格。
7. 在 [成員] 清單中，選取您先前建立的 Azure AD 使用者。
8. 在入口網站底部，按一下**選取**  ，然後**建立**建立安全性群組。

    記下的群組識別碼值。

9. 建立群組時，您會在所有群組清單中看見它。 按一下新的群組。
10. 在出現的頁面上，複製**物件識別碼**。 我們會將此值作為`GROUPID`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

您可以自動建立 Azure Active Directory (Azure AD) 應用程式註冊用戶端藉由略過建立叢集的過程`--aad-client-app-id`旗標設為`az openshift create`命令。 本教學課程會示範如何建立 Azure AD 應用程式註冊，為求完整性。

如果您的組織還沒有 Azure Active Directory (Azure AD) 應用程式註冊來做為服務主體，請遵循下列指示來建立一個。

1. 開啟[應用程式註冊 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)然後按一下 **+ 新的註冊**。
2. 在 [**註冊應用程式**] 窗格中，輸入您的應用程式註冊的名稱。
3. 請確認下**支援的帳戶類型**，**只有此組織目錄中的帳戶**已選取。 這是最安全的選擇。
4. 一旦我們知道叢集的 URI，我們會新增重新導向 URI 更新版本。 按一下 [**註冊**] 按鈕來建立 Azure AD 應用程式註冊。
5. 在出現的頁面上，複製**應用程式 （用戶端） 識別碼**。 我們會將此值作為`APPID`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

![應用程式物件頁面的螢幕擷取畫面](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>建立用戶端密碼

產生用於驗證您的應用程式至 Azure Active Directory 用戶端祕密。

1. 在 **管理**區段的 應用程式註冊 頁面中，按一下 **憑證與密碼**。
2. 在 **憑證與祕密**窗格中，按一下 **+ 新的用戶端祕密**。  **新增用戶端祕密**窗格隨即出現。
3. 提供**描述**。
4. 設定**Expires**想，比方說的持續期間**2 年內**。
5. 按一下 **新增**金鑰的值會出現在**用戶端祕密**頁面區段。
6. 複製金鑰值。 我們會將此值作為`SECRET`中[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

![憑證和祕密 窗格的螢幕擷取畫面](./media/howto-create-tenant/create-key.png)

如需有關 Azure 應用程式物件的詳細資訊，請參閱 <<c0> [ 應用程式和 Azure Active Directory 中的服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。

如需有關建立新的 Azure AD 應用程式，請參閱[註冊應用程式與 Azure Active Directory v1.0 端點](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

## <a name="add-api-permissions"></a>新增 API 權限

1. 在 **管理**區段中，按一下**API 的權限**。
2. 按一下 **新增權限**，然後選取**Azure Active Directory Graph**然後**委派的權限**
3. 依序展開**使用者**下方的清單，請確定上**User.Read**已啟用。
4. 向上捲動並選取**應用程式權限**。
5. 依序展開**Directory**下方的清單和啟用**Directory.ReadAll**
6. 按一下 **新增權限**以接受變更。
7. API 權限 面板現在應該會顯示這兩*User.Read*並*Directory.ReadAll*。 請注意在警告**所需的系統管理員同意**旁的資料行*Directory.ReadAll*。
8. 您若是*Azure 訂用帳戶系統管理員*，按一下**授與系統管理員同意*訂用帳戶名稱***  如下。 如果您不是*Azure 訂用帳戶管理員*，要求系統管理員同意。
![API 權限 面板的螢幕擷取畫面。 User.Read 和 Directory.ReadAll 權限新增，系統管理員同意所需的 Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 叢集系統管理員群組的同步處理可授與同意之後，才。 您會看到訊息與核取記號的綠色圓圈"獲得*訂用帳戶名稱*」 中*所需的系統管理員同意*資料行。

如需有關管理系統管理員和其他角色的詳細資訊，請參閱[新增或變更 Azure 訂用帳戶系統管理員](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)。

## <a name="resources"></a>資源

* [應用程式與 Azure Active Directory 中的服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [快速入門：使用 Azure Active Directory v1.0 端點註冊 app](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>後續步驟

如果您已符合所有[Azure Red Hat OpenShift 必要條件](howto-setup-environment.md)，您即可建立您的第一個叢集 ！

請嘗試本教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
