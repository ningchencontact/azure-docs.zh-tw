---
title: 教學課程：設定自動使用者佈建與 Azure Active Directory 的 MerchLogix |Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建和取消佈建 merchlogix 的使用者帳戶。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263585"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>教學課程：設定自動使用者佈建的 MerchLogix

本教學課程的目的是要示範在 MerchLogix 與 Azure Active Directory (Azure AD) 設定 Azure AD 自動佈建及取消佈建使用者和/或群組 merchlogix 中執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* MerchLogix 租用戶
* 在該人員可以提供 SCIM 端點 URL 和使用者佈建所需的祕密權杖 MerchLogix 的技術連絡人

## <a name="adding-merchlogix-from-the-gallery"></a>從資源庫新增 MerchLogix

MerchLogix 設定自動化使用者佈建與 Azure AD 時，您需要從 Azure AD 應用程式資源庫將 MerchLogix 新增到受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增 MerchLogix，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式區段][2]

3. 若要新增 MerchLogix，請按一下**新的應用程式**對話方塊頂端的按鈕。

    ![新增應用程式按鈕][3]

4. 在 [搜尋] 方塊中，輸入**MerchLogix**。

5. 在結果窗格中，選取**MerchLogix**，然後按一下**新增**按鈕將 MerchLogix 新增至您的 SaaS 應用程式的清單。

    ![MerchLogix 佈建][4]

## <a name="assigning-users-to-merchlogix"></a>將使用者指派給 MerchLogix

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。 

之前設定，並啟用自動使用者佈建，您應該決定哪些使用者和/或 Azure AD 中的群組需要存取 MerchLogix。 一旦決定後，您可以指派這些使用者和/或群組 merchlogix 依照此處的指示：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>將使用者指派給 MerchLogix 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 MerchLogix，以測試您初始的自動使用者佈建的設定。 您可於稍後在測試成功之後指派其他使用者及/或群組。

* 當將使用者指派給 MerchLogix，您必須選取 [指派] 對話方塊中的任何有效的應用程式特有角色 （如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>設定自動使用者佈建至 MerchLogix 

本節將引導您逐步設定 Azure AD 佈建服務來建立、 更新和停用使用者和/或群組，在 MerchLogix 中的根據 Azure AD 中使用者和/或群組指派。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入的 MerchLogix，下列指示中提供[MerchLogix 單一登入教學課程](merchlogix-tutorial.md)。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>若要設定 Azure ad 自動使用者佈建的 MerchLogix:

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 您可以選取 MerchLogix 從您的 SaaS 應用程式清單。

3. 選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![MerchLogix 佈建](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. 底下**系統管理員認證**區段：

    * 在 **租用戶 URL**欄位中，輸入您的 MerchLogix 技術連絡人所提供的 SCIM 端點 URL。

    * 在 **祕密權杖**欄位中，輸入您的 MerchLogix 技術連絡人所提供的祕密權杖。

6. 填寫後在步驟 5 中所顯示的欄位，按一下**測試連接**以確保 Azure AD 可以連線到 MerchLogix。 如果連線失敗，請確定您的 MerchLogix 帳戶具有系統管理員權限並再試一次。

7. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

8. 按一下 [檔案] 。

9. 底下**對應**區段中，選取**同步處理 Azure Active Directory 使用者至 MerchLogix**。

10. 檢閱從 Azure AD 同步至 MerchLogix 中的使用者屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對在 MerchLogix 中的使用者帳戶進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

11. 底下**對應**區段中，選取**同步處理 Azure Active Directory 群組至 MerchLogix**。

12. 檢閱從 Azure AD 同步至 MerchLogix 中的群組屬性**屬性對應**一節。 為選取的屬性**比對**屬性用來比對在 MerchLogix 中進行更新作業的群組。 選取 [儲存] 按鈕以認可所有變更。

13. 若要啟用 Azure AD 佈建服務 MerchLogix，請變更**佈建狀態**要**上**中**設定**一節。

14. 當您準備好要佈建時，按一下 [儲存]。

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用**同步處理詳細資料**區段來監視進度，並依循連結前往佈建活動報告，當中會描述佈建服務在 MerchLogix 的 Azure AD 所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
