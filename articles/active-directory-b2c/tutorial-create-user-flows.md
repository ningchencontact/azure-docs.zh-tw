---
title: 教學課程 - 在 Azure Active Directory B2C 中建立使用者流程 | Microsoft Docs
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中建立應用程式的流程。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 6e651d23e3b5cced78088d59979507eb09723165
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845586"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中建立使用者流程

在您的應用程式中，您可能會有[使用者流程](active-directory-b2c-reference-policies.md)可讓使用者註冊、登入或管理其設定檔。 您可以在 Azure Active Directory (Azure AD) B2C 租用戶中建立多個不同類型的使用者流程，並視需要在您的應用程式中使用。 使用者流程可以跨應用程式重複使用。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

本教學課程會示範如何使用 Azure 入口網站建立一些建議的使用者流程。 如果您正在尋找有關如何在應用程式中設定資源擁有者密碼認證 (ROPC) 流程的資訊，請參閱[在 Azure AD B2C 中設定資源擁有者密碼認證流程](configure-ropc.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[註冊應用程式](tutorial-register-applications.md) (屬於您想要建立的使用者流程的一部分)。 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>建立註冊和登入使用者流程

註冊和登入使用者流程會透過單一組態來處理註冊與登入體驗。 系統會視內容而定，將您應用程式的使用者引導到正確的路徑。

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。

    ![切換至訂用帳戶目錄](./media/tutorial-create-user-flows/switch-directories.png)

2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 在左側功能表中，選取 [使用者流程]，然後選取 [新使用者流程]。

    ![選取新的使用者流程](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

4. 在 [建議] 索引標籤上，選取 [註冊並登入]。

    ![選取註冊和登入使用者流程](./media/tutorial-create-user-flows/signup-signin-type.png)

5. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
6. 針對 [識別提供者]，選取 [電子郵件註冊]。

    ![設定流程屬性](./media/tutorial-create-user-flows/signup-signin-properties.png)

7. 針對 [使用者屬性與宣告]，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 例如，選取 [顯示更多]，然後選取 [國家/地區]、[顯示名稱]，及 [郵遞區號]。 按一下 [確定]。

    ![選取屬性和宣告](./media/tutorial-create-user-flows/signup-signin-attributes.png)

8. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]。
2. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]，然後選取 [立即註冊]。

    ![執行使用者流程](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. 輸入有效的電子郵件地址，按一下 [傳送驗證碼]，然後輸入您收到的驗證碼。
5. 輸入新密碼並確認密碼。
6. 輸入您想要顯示的名稱、選取您的國家/地區和區域、輸入郵遞區號，然後按一下 [建立]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。
7. 您現在可以再次執行使用者流程，且您應能夠使用您建立的帳戶登入。 傳回的權杖包含您所選名稱、國家/地區和郵遞區號的宣告。

## <a name="create-a-profile-editing-user-flow"></a>建立設定檔編輯使用者流程

如果您要在您的應用程式中允許使用者編輯其設定檔，您可使用設定檔編輯使用者流程。

1. 在左側功能表中，選取 [使用者流程]，然後選取 [新使用者流程]。
2. 在 [建議] 索引標籤上選取 [設定檔編輯] 使用者流程。
3. 輸入使用者流程的 [名稱]。 例如，*profileediting1*。
4. 針對 [識別提供者] 選取 [本機帳戶登入]。
5. 針對 [使用者屬性]，選擇您要客戶能夠在其設定檔中編輯的屬性。 例如，選取 [顯示更多]，然後選擇 [顯示名稱] 和 [職稱]。 按一下 [確定]。
6. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]。
2. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]，然後使用您先前建立的帳戶登入。
4. 現在您有機會變更使用者的顯示名稱和職稱。 按一下 [繼續]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="create-a-password-reset-user-flow"></a>建立密碼重設使用者流程

您可以視需要允許您應用程式的使用者重設其密碼。 若要允許重設密碼，您可以使用密碼重設使用者流程。

1. 在左側功能表中，選取 [使用者流程]，然後選取 [新使用者流程]。
2. 在 [建議] 索引標籤上，選取 [密碼重設] 使用者流程。
3. 輸入使用者流程的 [名稱]。 例如，*passwordreset1*。
4. 針對 [識別提供者]啟用 [使用電子郵件地址重設密碼]。
5. 在 [應用程式宣告] 底下，按一下 [顯示更多]，並選擇您要以傳送回應用程式的授權權杖傳回的宣告。 例如，選取 [使用者的物件識別碼]。
6. 按一下 [確定]。
7. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 在您所建立使用者流程的 [概觀] 頁面上，選取 [執行使用者流程]。
2. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]，然後使用您先前建立的帳戶登入。
4. 您現在應該有機會變更使用者的密碼。 按一下 [繼續]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立註冊和登入使用者流程
> * 建立設定檔編輯使用者流程
> * 建立密碼重設使用者流程

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)