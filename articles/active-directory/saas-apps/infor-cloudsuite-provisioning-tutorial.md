---
title: 'Tutorial: Configure Infor CloudSuite for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408712"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Infor CloudSuite and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Infor CloudSuite.

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [A Infor CloudSuite tenant](https://www.infor.com/products/infor-os)
* A user account in Infor CloudSuite with Admin permissions.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assigning users to Infor CloudSuite

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Infor CloudSuite. Once decided, you can assign these users and/or groups to Infor CloudSuite by following the instructions here:
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Important tips for assigning users to Infor CloudSuite

* It is recommended that a single Azure AD user is assigned to Infor CloudSuite to test the automatic user provisioning configuration. 其他使用者及/或群組可能會稍後再指派。

* When assigning a user to Infor CloudSuite, you must select any valid application-specific role (if available) in the assignment dialog. 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Set up Infor CloudSuite for provisioning

1. Sign in to your [Infor CloudSuite Admin Console](https://www.infor.com/customer-center). Click on the user icon and then navigate to **user management**.

    ![Infor CloudSuite Admin Console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Click on the menu icon on the left top corner of the screen. Click on **Manage**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigate to **SCIM Accounts**.

    ![Infor CloudSuite SCIM Account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Add an admin user by clicking on the plus icon. Provide a **SCIM Password** and type the same password under **Confirm Password**. Click on the folder icon to save the password. You will then see an **User Identifier** generated for the admin user.

    ![Infor CloudSuite Admin user](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite password](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. To generate the bearer token, copy the **User Identifier** and **SCIM Password**. Paste them in notepad++ separated by a colon. Encode the string value by navigating to **Plugins > MIME Tools > Basic64 Encode**. 

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copy the bearer token. This value will be entered in the Secret Token field in the Provisioning tab of your Infor CloudSuite application in the Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Add Infor CloudSuite from the gallery

Before configuring Infor CloudSuite for automatic user provisioning with Azure AD, you need to add Infor CloudSuite from the Azure AD application gallery to your list of managed SaaS applications.

**To add Infor CloudSuite from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)** , in the left navigation panel, select **Azure Active Directory**.

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

    ![新增應用程式按鈕](common/add-new-app.png)

4. In the search box, enter **Infor CloudSuite**, select **Infor CloudSuite** in the results panel, and then click the **Add** button to add the application.

    ![結果清單中的 Infor CloudSuite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuring automatic user provisioning to Infor CloudSuite 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Infor CloudSuite based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for Infor CloudSuite , following the instructions provided in the [Infor CloudSuite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). 雖然自動使用者佈建和單一登入這兩個功能彼此補充，您可以分開設定它們。

> [!NOTE]
> To learn more about Infor CloudSuite's SCIM endpoint, refer [this](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>To configure automatic user provisioning for Infor CloudSuite in Azure AD:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 Select **Enterprise Applications**, then select **All applications**.

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Infor CloudSuite]。

    ![應用程式清單中的 [Infor CloudSuite] 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![Provisioning tab](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![Provisioning tab](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **Tenant URL**. Input the bearer token value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Infor CloudSuite. If the connection fails, ensure your Infor CloudSuite account has Admin permissions and try again.

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]。

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Review the user attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Infor CloudSuite for update operations. 選取 [儲存] 按鈕以認可所有變更。

    ![Infor CloudSuite User Attributes](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Infor CloudSuite**.

    ![Infor CloudSuite Group Mappings](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Review the group attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Infor CloudSuite for update operations. 選取 [儲存] 按鈕以認可所有變更。

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. To enable the Azure AD provisioning service for Infor CloudSuite, change the **Provisioning Status** to **On** in the **Settings** section.

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Infor CloudSuite by choosing the desired values in **Scope** in the **Settings** section.

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Infor CloudSuite.

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)