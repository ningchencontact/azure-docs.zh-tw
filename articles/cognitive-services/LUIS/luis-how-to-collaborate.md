---
title: 與其他人共用作業
titleSuffix: Language Understanding - Azure Cognitive Services
description: 應用程式擁有者可以將共同作業者新增至應用程式。 這些共同作業者可以修改模型、訓練及發佈應用程式。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: bf714e5bd47e244a410d1062488af623253bbee6
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217776"
---
# <a name="how-to-manage-authors-and-collaborators"></a>如何管理建立者及共同作業者 

應用程式擁有者可以將共同作業者新增至應用程式。 這些共同作業者可以修改模型、訓練及發佈應用程式。 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>新增共同作業者

一個應用程式只有單一建立者 (擁有者)，但可以有許多共同作業者。 若要允許共同作業者編輯您的 LUIS 應用程式，您必須將他們用來存取 LUIS 入口網站的電子郵件新增至共同作業者清單。 新增之後，應用程式就會顯示在其 LUIS 入口網站中。

1. 從右上方功能表中選取 [管理]，然後選取左側功能表中的 [共同作業者]。

2. 從工具列中選取 [新增共同作業者]。

    [![新增共同作業者](./media/luis-how-to-collaborate/add-collaborator.png "新增共同作業者")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. 輸入共同作業者用來登入 LUIS 入口網站的電子郵件地址。

    ![新增共同作業者的電子郵件地址](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>擁有權的轉讓

LUIS 目前並不支援擁有權的轉讓，但您可以匯出應用程式，而其他 LUIS 使用者則可以匯入應用程式。 兩個應用程式的 LUIS 分數可能會有些許差異。 

## <a name="azure-active-directory-resources"></a>Azure Active Directory 資源

如果您在組織中使用 Azure Active Directory (Azure AD)，當使用者想要使用 LUIS 時，LUIS 需要權限來存取使用者的相關資訊。 LUIS 需要的資源非常少。 

當您嘗試使用具有管理員同意或不需要管理員同意的帳戶註冊時，您會看到詳細的描述，例如系統管理員同意：

* 允許您使用貴組織帳戶來登入應用程式，並且讓應用程式讀取您的設定檔。 它也允許應用程式讀取基本公司資訊。
* 允許應用程式查看並更新您的資料，即使您目前並未使用該應用程式。

第一個權限讓 LUIS 有權讀取基本設定檔資料，例如使用者識別碼、電子郵件、名稱。 需要第二個權限，才能重新整理使用者的存取權杖。

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租用戶使用者

LUIS 使用標準 Azure Active Directory (Azure AD) 同意流程。 

租用戶系統管理員應該與需要被授權允許在 Azure AD 中使用 LUIS 的使用者直接合作。 

首先，使用者登入 LUIS，然後看到一個需要系統管理員核准的快顯對話方塊。 使用者會先連絡租用戶系統管理員，然後才能繼續後面的步驟。 

第二，租用戶系統管理員登入 LUIS，然後看到一個同意流程快顯對話方塊。 系統管理員必須在這個對話方塊中授與權限給使用者。 當系統管理員接受權限後，使用者便能繼續使用 LUIS。

如果租用戶系統管理員將不會登入 LUIS，系統管理員可以存取 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)。 

![依應用程式網站分類的 Azure Active Directory 權限](./media/luis-how-to-collaborate/tenant-permissions.png)

如果租用戶系統管理員只希望特定使用者使用 LUIS，則有一些可能的解決方式：
* 提供「管理員同意」(同意 Azure AD 的所有使用者)，然後將 [企業應用程式屬性] 之下的 [需要使用者指派] 設定為 [是]，最後只將想要的使用者指派/新增至應用程式。 使用此方法，系統管理員仍提供應用程式的「管理員同意」，不過，能夠控制可以存取該應用程式的使用者。
* 第二個解決方式是使用 [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview)，以同意每個特定的使用者。 

深入了解 Azure Active Directory 使用者和同意： 
* [將您的應用程式限制](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)為僅供一組使用者存取

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>具有多個共同作業者電子郵件的使用者帳戶

如果將共同作業者新增至 LUIS 應用程式，便是在指定一個確切的電子郵件地址，讓共同作業者能以共同作業者的身分來使用 LUIS。 Azure Active Directory (Azure AD) 可讓單一使用者擁有多個能交替使用的電子郵件帳戶，LUIS 會要求使用者以共同作業者的清單中所指定的電子郵件地址來登入。

