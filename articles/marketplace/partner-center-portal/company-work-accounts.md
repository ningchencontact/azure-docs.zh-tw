---
title: 公司的工作帳戶 」 和 「 合作夥伴中心
description: 如何檢查您的公司是否有工作帳戶與 Microsoft 設定、 建立新的工作帳戶，或將多個工作帳戶設定為使用與合作夥伴中心。
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619555"
---
# <a name="company-work-accounts-and-partner-center"></a>公司的工作帳戶 」 和 「 合作夥伴中心

合作夥伴中心會使用公司的工作帳戶，也就是 Azure Active Directory (AD) 租用戶，來管理多個使用者、 控制 」 權限、 主機群組和應用程式的帳戶存取權和維護設定檔資料。 將您公司的工作電子郵件帳戶的網域連結至您的合作夥伴中心帳戶中，您公司的員工可以登入合作夥伴中心來管理他們自己的工作帳戶使用者名稱和密碼的 marketplace 供應項目。

## <a name="check-whether-your-company-already-has-a-work-account"></a>請檢查您的公司是否已有一個工作帳戶

如果您的公司已訂閱 Microsoft 雲端服務，例如 Azure、 Microsoft Intune 或 Office 365，則您已經有的工作電子郵件帳戶網域 （也稱為 「 Azure Active Directory 租用戶 」），可與合作夥伴中心。

請遵循下列步驟來檢查：
1. 登入 Azure 管理入口網站在 https://portal.azure.com 。
2. 選取  **Azure Active Directory**從左導覽功能表，然後選取**自訂網域名稱**。
3. 如果您已經有工作帳戶時，會列出您的網域名稱。

如果您的公司還沒有工作帳戶，將會為您建立一個在合作夥伴中心 」 註冊程序。

## <a name="set-up-multiple-work-accounts"></a>設定多個工作帳戶

然後再決定要使用現有的工作帳戶，請考慮需要多少工作帳戶的使用者存取合作夥伴中心。 如果您有使用者不需要存取合作夥伴中心的工作帳戶時，您可能要考慮建立多個工作帳戶，以便只有這些使用者將需要存取合作夥伴中心會表示特定的帳戶。

## <a name="create-a-new-work-account"></a>建立新的工作帳戶

若要為您的公司建立新的工作帳戶，請遵循下列步驟。 您可能需要要求您提供協助人員在貴公司的 Microsoft Azure 帳戶上具有系統管理權限。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。
2. 從左側的導覽功能表中，選取**Azure Active Directory** -> **使用者**。
3. 選取 **新的使用者**並輸入名稱和電子郵件地址建立新的 Azure 工作帳戶。 請確認**目錄角色**設為**使用者**，然後選取**顯示密碼**底部檢視，並記下自動產生密碼的核取方塊。
4. 選取 **建立**以儲存新的使用者。

電子郵件地址的使用者帳戶必須是您目錄中的已驗證的網域名稱。 您也可以選取您的目錄中列出所有已驗證的網域**Azure Active Directory** -> **自訂網域名稱**左導覽功能表中。

若要深入了解 Azure Active Directory 中新增自訂網域，請參閱[新增或建立 Azure AD 中的網域關聯](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)。

## <a name="troubleshoot-work-email-sign-in"></a>工作電子郵件登入進行疑難排解

如果您無法登入您的工作帳戶 (也稱為 Azure AD 租用戶），尋找最下方圖表上的案例符合您的情況，並遵循建議的步驟。

![以工作帳戶登入進行疑難排解的圖表](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>後續步驟

- [管理您的商業的 Marketplace 帳戶在合作夥伴中心](./manage-account.md) 
