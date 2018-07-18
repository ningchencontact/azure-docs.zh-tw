---
title: 管理您在 LUIS 中的帳戶設定 | Microsoft Docs
description: 使用 LUIS 網站來管理您的帳戶設定。
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749958"
---
# <a name="manage-your-luis-account"></a>管理您的 LUIS 帳戶
LUIS 帳戶的兩個重要資訊為使用者帳戶和撰寫金鑰。 您的登入資訊是在 [account.microsoft.com](https://account.microsoft.com) 進行管理的。 您的撰寫金鑰是從 [LUIS][LUIS] 網站**設定**頁面進行管理的。 

## <a name="authoring-key"></a>撰寫金鑰

在 [設定] 頁面上的這個單一、特定地區的撰寫金鑰，可讓您從 [LUIS][LUIS] 網站和[撰寫 API](https://aka.ms/luis-authoring-api) 撰寫所有應用程式。 為了方便起見，允許撰寫金鑰每個月建立[有限](luis-boundaries.md)數量的端點查詢。 

![LUIS 設定頁面](./media/luis-how-to-account-settings/account-settings.png)

撰寫金鑰是用於您所擁有的任何應用程式，以及任何您被列為共同作業者的應用程式。

## <a name="authoring-key-regions"></a>撰寫金鑰區域
不同的[撰寫區域](luis-reference-regions.md#publishing-regions)有自己特有的撰寫金鑰。 這種金鑰無法在不同的區域中使用。 

## <a name="reset-authoring-key"></a>重設撰寫金鑰
如果您的撰寫金鑰已被盜用，請重設金鑰。 該金鑰會在 [LUIS] 網站的所有應用程式上重設。 如果您透過撰寫 API 來撰寫您的應用程式，則需要將 `Ocp-Apim-Subscription-Key` 的值變更成新的金鑰。 

## <a name="delete-account"></a>刪除帳戶
如需刪除帳戶時會刪除哪些資料的相關資訊，請參閱[資料儲存和移除](luis-concept-data-storage.md#accounts)。 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租用戶使用者
LUIS 使用標準 Azure Active Directory (Azure AD) 同意流程。 

租用戶系統管理員應該與需要被授權允許在 Azure AD 中使用 LUIS 的使用者直接合作。 

首先，使用者登入 LUIS，然後看到一個需要系統管理員核准的快顯對話方塊。 使用者會先連絡租用戶系統管理員，然後才能繼續後面的步驟。 

第二，租用戶系統管理員登入 LUIS，然後看到一個同意流程快顯對話方塊。 系統管理員必須在這個對話方塊中授與權限給使用者。 當系統管理員接受權限後，使用者便能繼續使用 LUIS。

如果租用戶系統管理員將不會登入 LUIS，系統管理員可以存取 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)。 

![依應用程式網站分類的 Azure Active Directory 權限](./media/luis-how-to-account-settings/tenant-permissions.png)

如果租用戶系統管理員只希望特定使用者使用 LUIS，請參閱這個[身分識別部落格](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多個共同作業者電子郵件的使用者帳戶
如果將共同作業者新增至 LUIS 應用程式，便是在指定一個確切的電子郵件地址，讓共同作業者能以共同作業者的身分來使用 LUIS。 Azure Active Directory (Azure AD) 可讓單一使用者擁有多個能交替使用的電子郵件帳戶，LUIS 會要求使用者以共同作業者的清單中所指定的電子郵件地址來登入。 


## <a name="next-steps"></a>後續步驟

深入了解您的[撰寫金鑰](luis-concept-keys.md#authoring-key)。 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
