---
title: 在 Azure 中的 LUIS 應用程式上與其他參與者共同作業 | Microsoft Docs
description: 了解如何在 Language Understanding (LUIS) 應用程式上與其他參與者共同作業。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397777"
---
# <a name="how-to-manage-authors-and-collaborators"></a>如何管理建立者及共同作業者 

您可以在 LUIS 應用程式上與其他人共同作業。 

## <a name="owner-and-collaborators"></a>擁有者和共同作業者

一個應用程式只有單一建立者 (擁有者)，但可以有許多共同作業者。 

## <a name="add-collaborator"></a>新增共同作業者

若要允許共同作業者編輯您的 LUIS 應用程式，請在 LUIS 應用程式的 [設定] 頁面上輸入共同作業者的電子郵件，然後按一下 [新增共同作業者]。 共同作業者可在您使用 LUIS 應用程式的同時登入及編輯該應用程式。

![新增共同作業者](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>擁有權的轉讓

LUIS 目前並不支援擁有權的轉讓，但您可以匯出應用程式，而其他 LUIS 使用者則可以匯入應用程式。 兩個應用程式的 LUIS 分數可能會有些許差異。 

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