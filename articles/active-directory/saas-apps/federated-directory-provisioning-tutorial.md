---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的同盟目錄 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到同盟目錄的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840585"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的同盟目錄

本教學課程的目的是要示範在同盟目錄與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建和取消佈建使用者和/或群組至同盟目錄中執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [A Federated Directory](https://www.federated.directory/pricing)。
* 使用者帳戶在同盟目錄中具有管理員權限。

## <a name="assign-users-to-federated-directory"></a>將使用者指派給同盟目錄
Azure Active Directory 會使用稱為指派的概念，來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應該決定哪些使用者和/或 Azure AD 中的群組需要同盟目錄的存取權。 一旦決定後，您可以指派這些使用者和/或群組至同盟目錄依照此處的指示：

 * [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>將使用者指派給同盟目錄的重要秘訣
 * 建議將單一 Azure AD 使用者指派給同盟目錄，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給同盟目錄中，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有 「 預設存取 」 角色的使用者會從佈建中排除。
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>設定同盟目錄佈建

然後再設定自動使用者佈建與 Azure AD 的同盟目錄，您必須啟用 SCIM 佈建在同盟目錄上。

1. 登入您[同盟目錄系統管理員主控台](https://federated.directory/of)

    ![同盟的 Directory 教學課程](media/federated-directory-provisioning-tutorial/companyname.png)

2. 瀏覽至**目錄 > 使用者目錄**，然後選取您的租用戶。 

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  若要產生永久的持有人權杖，瀏覽至**目錄金鑰 > 建立新的金鑰。** 

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/federated01.png)

4. 建立目錄機碼。 

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. 複製 [存取權杖]  值。 此值將會進入**祕密權杖**同盟目錄應用程式在 Azure 入口網站中的 [佈建] 索引標籤中的欄位。 

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>從資源庫新增同盟目錄

若要設定自動使用者佈建與 Azure AD 同盟的目錄，您需要從 Azure AD 應用程式資源庫將同盟目錄新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增同盟的目錄，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**同盟目錄**，選取**Federated Directory**在結果窗格中。

    ![結果清單中的同盟的目錄](common/search-new-app.png)

5. 瀏覽至**URL**反白顯示下面另一個瀏覽器中。 

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. 按一下 **登入**。

    ![同盟的 directory](media/federated-directory-provisioning-tutorial/federated04.png)

7.  當同盟目錄是 OpenIDConnect 應用程式，選擇同盟的目錄，使用您的 Microsoft 工作帳戶登入。
    
    ![同盟的 directory](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. 成功驗證之後接受同意提示同意頁面。 應用程式將會自動新增至您的租用戶，您將會重新導向至您的同盟 Directory 帳戶。

    ![同盟的目錄中新增 SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>設定自動使用者佈建至同盟目錄 

本節會引導您完成設定 Azure AD 佈建服務以建立、 更新和停用使用者和/或群組，根據使用者和/或群組指派 Azure AD 中的同盟目錄中的步驟。

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建同盟目錄：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取**Federated Directory**。

    ![同盟目錄中的 連結應用程式清單](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入`https://api.federated.directory/v2/`租用戶 url。 輸入您擷取，並從同盟目錄在稍早儲存的價值**祕密權杖**。 按一下 **測試連接**以確保 Azure AD 可以連線至同盟目錄。 如果連線失敗，請確定您的同盟目錄帳戶具有系統管理員權限並再試一次。

    ![租用戶 URL + 語彙基元](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [儲存]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至同盟目錄**。

    ![同盟的 Directory 教學課程](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. 檢閱從 Azure AD 同步至同盟中的目錄的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對同盟目錄中的使用者帳戶進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![同盟的 Directory 教學課程](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Azure AD 佈建服務同盟目錄，請變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

14. 使用者和/或您想要的群組選擇定義要佈建到同盟目錄中的所需的值**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務同盟目錄上的 Azure AD 所執行的所有動作。

如需有關如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[自動使用者帳戶佈建的報告](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)