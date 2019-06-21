---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的縮放 |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建使用者帳戶進行縮放。
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
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: ca79b3901e3933e25c5be92673e0c5bcc464782d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167852"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的縮放

本教學課程旨在示範將 Azure AD 設定的 縮放比例與 Azure Active Directory (Azure AD) 中執行的步驟來自動佈建和取消佈建使用者和/或群組來縮放。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關 Microsoft Azure 的一般使用規定預覽功能的詳細資訊，請參閱 <<c0> [ 補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [Zoom 租用戶](https://zoom.us/pricing)
* 縮放中具有系統管理員權限的使用者帳戶

## <a name="add-zoom-from-the-gallery"></a>從資源庫新增 Zoom

設定縮放為之前自動使用者佈建與 Azure AD，您需要從 Azure AD 應用程式資源庫將縮放新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 Zoom，請執行下列步驟：**

1. 在   **[Azure 入口網站](https://portal.azure.com)** ，在左側的導覽窗格中，選取**Azure Active Directory**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取**新的應用程式**窗格頂端的按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在 [搜尋] 方塊中，輸入**縮放**，選取**縮放**在結果窗格中，然後按一下**新增**按鈕以新增應用程式。

    ![結果清單中的 Zoom](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>指派使用者給 Zoom

Azure Active Directory 使用稱為概念*指派*來判斷哪些使用者應接收所選的應用程式的存取。 在自動使用者佈建的內容中，只有使用者和/或已指派給 Azure AD 中的應用程式的群組同步處理。

在設定並啟用自動使用者佈建之前, 中，您應先決定哪些使用者和/或 Azure AD 中的群組需要存取 Zoom。 一旦決定後，您可以指派這些使用者和/或群組以縮放依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>將使用者指派給 Zoom 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Zoom，以測試自動使用者佈建的設定。 其他使用者及/或群組可能會稍後再指派。

* 當將使用者指派給 Zoom，您必須選取任何有效的應用程式特有角色 （如果有的話） 在 [指派] 對話方塊中。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configure-automatic-user-provisioning-to-zoom"></a>設定自動使用者佈建至縮放 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者或群組中縮放根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 zoom，下列指示中提供[Zoom 單一登入教學課程](zoom-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>設定 Azure ad 自動使用者佈建的縮放

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 **企業應用程式**，然後選取**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zoom]  。

    ![應用程式清單中的 Zoom 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![佈建 索引標籤](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![佈建 索引標籤](common/provisioning-automatic.png)

5. 底下**系統管理員認證**區段中，輸入`https://api.zoom.us/scim`中**租用戶 URL**。 若要擷取**祕密權杖**縮放帳戶、 遵循本逐步解說步驟 6 中所述。

6. 登入您[縮放 Admin Console](https://zoom.us/signin)。 瀏覽至**進階 > 適用於開發人員縮放**左側的導覽窗格中。

    ![Zoom 整合](media/zoom-provisioning-tutorial/zoom01.png)

    瀏覽至**管理**頁面右上角。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom02.png)

    瀏覽至您建立 Azure AD 應用程式。 
    
    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom03.png)

    選取 **應用程式認證**左側的導覽窗格中。

    ![縮放應用程式](media/zoom-provisioning-tutorial/zoom04.png)

    擷取 JWT 權杖值如下所示，然後輸入分成**祕密權杖**在 Azure AD 中的欄位。 如果您需要新的非過期權杖時，您必須重新設定到期時間，將會自動產生新的權杖。 

    ![縮放安裝](media/zoom-provisioning-tutorial/zoom05.png)

7. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到縮放。 如果連線失敗，請確定 縮放帳戶具有管理員權限，並再試一次。

    ![token](common/provisioning-testconnection-tenanturltoken.png)

8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案]  。

10. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至縮放**。

    ![縮放使用者對應](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. 檢閱從 Azure AD 同步至放大的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對使用者帳戶在 Zoom 中的進行更新作業。 選取 [儲存]  按鈕以認可所有變更。
    
     ![縮放使用者對應](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要啟用 Azure AD 佈建服務進行縮放，變更**佈建狀態**要**上**中**設定**一節。
    
    ![佈建狀態開啟](common/provisioning-toggle-on.png)

14. 藉由選擇需要的值在定義使用者和/或您想要的群組要佈建到 Zoom**領域**中**設定**一節。

    ![佈建的範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![正在儲存佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述 Azure AD 佈建服務上縮放所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* 縮放不支援的群組佈建。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
