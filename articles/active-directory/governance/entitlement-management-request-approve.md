---
title: 核准或拒絕存取要求，在 Azure AD 權限管理 （預覽）-Azure Active Directory
description: 了解如何使用我的存取入口網站來核准或拒絕要求至 Azure Active Directory 權限管理 （預覽） 中存取套件。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541521"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>核准或拒絕存取要求，在 Azure AD 權限管理 （預覽）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 權限管理，您可以設定為需要核准才能存取套件的原則，並選擇一或多個核准者。 這篇文章說明如何指定核准者可以核准或拒絕存取套件的要求。

## <a name="open-request"></a>開啟要求

核准或拒絕存取要求的第一個步驟是尋找並開啟 等待核准存取要求。 有兩種方式可開啟 存取要求。

**必要角色：** 核准者

1. 從 Microsoft Azure 會要求您核准或拒絕的要求中尋找的電子郵件。 以下是範例電子郵件：

    ![核准的要求以存取封裝電子郵件](./media/entitlement-management-shared/email-approve-request.png)

1. 按一下 **核准或拒絕要求**連結以開啟 存取要求。

1. 登入我存取入口網站。

如果您沒有電子郵件，則您可以依照下列步驟來找到等待您核准存取要求。

1. 登入我存取入口網站，網址[ https://myaccess.microsoft.com ](https://myaccess.microsoft.com)。

1. 在左側功能表中，按一下**核准**來看到一份等待核准的存取要求。

1. 在 **暫止**索引標籤上，找到的要求。

## <a name="approve-or-deny-request"></a>核准或拒絕要求

開啟 等待核准的存取要求之後，您可以看到將協助您進行核准或拒絕決策的詳細資料。

**必要角色：** 核准者

1. 按一下 [檢視]  連結以開啟 [存取要求] 窗格。

1. 按一下 **詳細資料**以查看存取要求的相關詳細資料。

    詳細資料包括使用者的名稱、 組織、 存取開始和結束日期，如果提供業務正當理由，當已提交的要求，並要求的到期時間。

1. 按一下 **核准**或是**拒絕**。

1. 如有必要，請輸入原因。

    ![我的存取權入口網站 - 存取要求](./media/entitlement-management-shared/my-access-approve-request.png)

1. 按一下 [提交]  以提交您的決定。

    如果有多位核准者設定的原則，則只有一個核准者必須以決定要擱置的核准。 核准者已提交存取要求其決定之後，要求完成，且不再可供其他核准者核准或拒絕要求。 要求決策和決策者，其我存取入口網站中的，可以看到其他核准者。 在此階段中，支援只有單一階段核准。

    如果沒有任何已設定核准者可以核准或拒絕存取要求，要求會設定的要求持續時間之後到期。 取得通知使用者，其存取要求已過期，他們必須重新提交存取要求。

## <a name="next-steps"></a>後續步驟

- [要求存取套件的存取權](entitlement-management-request-access.md)
- [要求處理程序與電子郵件通知](entitlement-management-process.md)
