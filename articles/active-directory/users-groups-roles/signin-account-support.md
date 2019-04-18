---
title: 如何知道是否 Azure AD 登入頁面會接受 Microsoft 帳戶 |Microsoft Docs
description: 如何螢幕上訊息會反映出在登入期間的使用者名稱查閱
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491590"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>針對 Azure Active Directory 中的 Microsoft 帳戶登入選項

Microsoft 365 登入頁面的 Azure Active Directory (Azure AD) 支援工作或學校帳戶和 Microsoft 帳戶，但根據使用者的情況下，可能是其中一個或兩者。 例如，Azure AD 登入頁面支援：

* 接受來自這兩種帳戶類型的登入的應用程式
* 接受訪客的組織

## <a name="identification"></a>識別
您可以告訴您的組織使用的登入頁面是否會支援 Microsoft 帳戶，藉由查看 [使用者名稱] 欄位中的提示文字。 「 電子郵件、 手機或 Skype 」，就會顯示提示文字，如果登入頁面支援 Microsoft 帳戶。

![帳戶登入頁面之間的差異](./media/signin-account-support/ui-prompt.png)

[其他登入選項只適用於個人 Microsoft 帳戶](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )但無法用於登入到公司或學校帳戶的資源。

## <a name="next-steps"></a>後續步驟

[自訂您登入的品牌](../fundamentals/add-custom-domain.md)