---
title: 教學課程：以 Azure Active Directory 設定 ThousandEyes 來自動佈建使用者 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 ThousandEyes。
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f008e981abb11a4927ec045c33342bbac9a05bd8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436790"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>教學課程：設定 ThousandEyes 來自動佈建使用者


本教學課程旨在說明您需要在 ThousandEyes 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ThousandEyes。 

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶
*   使用中[ThousandEyes 帳戶](https://www.thousandeyes.com/pricing)
*   已指派的角色，其中包含下列 3 個權限的 ThousandEyes 使用者帳戶：
    * 檢視所有使用者
    * 編輯使用者
    * API 存取權限

> [!NOTE]
> Azure AD 佈建整合仰賴[ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK_ThousandEyes-support-for-SCIM)。 

## <a name="assigning-users-to-thousandeyes"></a>將使用者指派給 ThousandEyes

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 ThousandEyes 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 ThousandEyes 應用程式︰

[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>將使用者指派給 ThousandEyes 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 ThousandEyes，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   當將使用者指派給 ThousandEyes，您必須選取**使用者**角色或另一個有效應用程式特有角色 （如果有的話） 在 [指派] 對話方塊中的。 [預設存取] 角色不適用於佈建，系統會略過這些使用者。

## <a name="configure-auto-provisioned-user-roles-in-thousandeyes"></a>在 ThousandEyes 中設定自動佈建使用者角色

每個帳戶群組，您會自動佈建到您的使用者可以設定一組新的使用者帳戶建立時套用的角色。 根據預設，自動佈建的使用者指派_一般使用者_所有帳戶的角色群組，除非另外設定。

1. 若要指定一組新的自動佈建使用者的角色登入 ThousandEyes 並瀏覽至 [SCIM 設定] 區段 **> 您在右上角的使用者圖示 > 帳戶設定 > 組織 > 安全性和驗證。** 

   ![瀏覽至 SCIM API 設定](https://monosnap.com/file/kqY8Il7eysGFAiCLCQWFizzM27PiBG)

2. 加入每個帳戶群組的項目，然後指派一組角色*儲存*您的變更。

   ![透過 SCIM API 所建立的使用者設定預設角色和帳戶群組](https://monosnap.com/file/16siam6U8xDQH1RTnaxnmIxvsZuNZG)


## <a name="configuring-user-provisioning-to-thousandeyes"></a>設定使用者佈建至 ThousandEyes 

本節會引導您將 Azure AD 連線至 ThousandEyes 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 ThousandEyes 中建立、更新和停用指派的使用者帳戶。

> [!TIP]
> 您也可以選擇啟用 SAML 型單一登入 (SSO) thousandeyes，遵循[提供 Azure 的知識庫中的指示](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-tutorial)完成 SSO。 您可以獨立設定自動佈建的 SSO，雖然這兩個功能彼此補充。


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>在 Azure AD 中設定將使用者帳戶自動佈建至 ThousandEyes


1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2. 如果您已經設定單一登入的 ThousandEyes，使用 [搜尋] 欄位搜尋您的 ThousandEyes 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [ThousandEyes]。 從搜尋結果中選取 ThousandEyes，並將它新增至您的應用程式清單。

3. 選取您的 ThousandEyes 執行個體，然後選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![ThousandEyes 佈建](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. 底下**系統管理員認證**區段中，輸入**OAuth 持有人權杖**ThousandEyes 的帳戶所產生 (您可以尋找及或產生權杖，在您的 ThousandEyes 帳戶下**設定檔**區段)。

    ![ThousandEyes 佈建](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. 在 Azure 入口網站中，按一下 [測試連線]以確保 Azure AD 可以連線到您的 ThousandEyes 應用程式。 如果連線失敗，請確定您的 ThousandEyes 帳戶具有管理員權限，然後再試一次步驟 5。

7. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

8. 按一下 [檔案] 。 

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 ThousandEyes]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 ThousandEyes 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 ThousandEyes 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

11. 若要啟用 ThousandEyes 的 Azure AD 佈建服務，在 [設定]區段中，將 [佈建狀態] 變更為 [開啟]

12. 按一下 [檔案] 。 

此作業會對 [使用者和群組] 區段中指派給 ThousandEyes 的任何使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並依循連結前往佈建活動記錄，此記錄會描述佈建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。


## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)
