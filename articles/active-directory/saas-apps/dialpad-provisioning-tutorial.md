---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的撥號盤 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建到撥號盤的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611770"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的撥號盤

本教學課程的目的是要示範撥號盤和 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組，以撥號盤要執行的步驟。

> [!NOTE]
>  本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

> 此連接器目前處於預覽狀態。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [撥號盤租用戶](https://www.dialpad.com/pricing/)。
* 中的使用者帳戶撥號盤具有管理員權限。

## <a name="assign-users-to-dialpad"></a>將使用者指派給撥號盤
Azure Active Directory 會使用稱為指派的概念，來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

之前設定，並啟用自動使用者佈建，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取撥號盤。 一旦決定後，您可以指派這些使用者和/或群組至撥號盤依照此處的指示：
 
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>將使用者指派給撥號盤的重要秘訣

 * 建議將單一 Azure AD 使用者指派給撥號盤，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給撥號盤，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有 「 預設存取 」 角色的使用者會從佈建中排除。

## <a name="setup-dialpad-for-provisioning"></a>安裝程式撥號盤，佈建

之前設定自動使用者佈建與 Azure AD 的撥號盤，您必須從撥號盤擷取某些佈建資訊。

1. 登入您[撥號盤 Admin Console](https://dialpadbeta.com/login) ，然後選取**系統管理員設定**。 請確認**我的公司**從下拉式清單中選取。 瀏覽至**驗證 > API 金鑰**。

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. 按一下 產生新的金鑰**新增金鑰**及設定您的祕密權杖的屬性。

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. 按一下 **按一下即可顯示值**按鈕最近建立的 API 金鑰，然後複製所顯示的值。 此值將會進入**祕密權杖**撥號盤應用程式在 Azure 入口網站中的 [佈建] 索引標籤中的欄位。 

    ![撥號盤建立權杖](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>從資源庫新增撥號盤

若要設定自動使用者佈建與 Azure AD 的撥號盤，您需要從 Azure AD 應用程式資源庫將撥號盤新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增撥號盤，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**撥號盤**，選取**撥號盤**在結果窗格中。
    ![結果清單中的撥號盤](common/search-new-app.png)

5. 瀏覽至**URL**反白顯示下面另一個瀏覽器中。 

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. 在右上角，選取**登入 > 線上使用撥號盤**。

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. 因為撥號盤 OpenIDConnect 應用程式，選擇 登入使用您的 Microsoft 工作帳戶的撥號盤。

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. 成功驗證之後接受同意提示同意頁面。 應用程式將會自動新增至您的租用戶，您會被重新導向至您的撥號盤帳戶。

    ![撥號盤新增 SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>設定自動使用者佈建至撥號盤

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組中撥號盤根據 Azure AD 中使用者和/或群組指派。

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的撥號盤：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取**撥號盤**。

    ![應用程式清單中的 [撥號盤] 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入`https://dialpad.com/scim`中**租用戶 URL**。 輸入您擷取，並從撥號盤中稍早儲存的值**祕密權杖**。 按一下 **測試連接**以確保 Azure AD 可以連線到撥號盤。 如果連線失敗，請確定您的撥號盤帳戶具有系統管理員權限並再試一次。

    ![租用戶 URL + 語彙基元](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]  。

8. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至撥號盤**。

    ![撥號盤使用者對應](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. 檢閱從 Azure AD 同步至撥號盤中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對的使用者帳戶在撥號盤，進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![撥號盤使用者屬性](media/dialpad-provisioning-tutorial/dialpad07.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Azure AD 佈建服務撥號盤，變更**佈建狀態**要**上**中**設定**一節。

    ![佈建狀態開啟](common/provisioning-toggle-on.png)

12. 藉由選擇需要的值中定義的使用者和/或您想要的群組要佈建到撥號盤**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務撥號盤上的 Azure AD 所執行的所有動作。

如需有關如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[自動使用者帳戶佈建的報告](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>連接器限制
* 撥號盤目前不支援群組重新命名。 這表示，任何變更**displayName**的群組，以在 Azure AD 將會無法更新及撥號盤反映。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
