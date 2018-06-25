---
title: 何謂 Azure Active Directory 條件式存取中的基準保護 | Microsoft Docs
description: 了解基準保護如何確保您的環境中至少會啟用基準層級的安全性。
services: active-directory
keywords: 應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248918"
---
# <a name="what-is-baseline-protection"></a>何謂基準保護？  

去年一年，身分識別攻擊增加了 300%。 為了讓您的環境免於遭受數量持續增加的攻擊，Azure Active Directory (Azure AD) 導入了稱為基準保護的新功能。 基準保護是一組預先定義的條件式存取原則。 這些原則的目標是要確保您的環境中至少會啟用基準層級的安全性。 

在預覽期間，如果您想要啟動基準原則，則需要自行啟用。 正式推出後，則會預設啟用這些原則。 

第一個基準保護原則需要對特殊權限帳戶進行 MFA。 攻擊者若掌控了特殊權限帳戶，將會造成極大的破壞，因此請務必先保護這些帳戶。 下列特殊權限角色在此原則的適用範圍內： 

- 全域管理員  

- SharePoint 管理員  

- Exchange 系統管理員  

- 條件式存取系統管理員  

- 安全性系統管理員  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>如何開始使用 

若要啟用基準原則：  

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分，登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 **Azure 入口網站**的左側導覽列上，按一下 [Azure Active Directory]。

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. 在 [Azure Active Directory] 頁面的 [管理] 區段中，按一下 [條件式存取]。

    ![條件式存取](./media/active-directory-conditional-access-baseline-protection/03.png)

4. 在原則清單中，按一下 [基準原則: 需要對系統管理員進行 MFA (預覽)]。 

5. 若要啟用原則，請按一下 [立即使用原則]。

6. 按一下 [檔案] 。 
 

基準原則會提供選項供您排除使用者和群組。 您可以排除一個*[緊急存取系統管理帳戶](active-directory-admin-manage-emergency-access-accounts.md)* 以確保系統不會將您鎖定而讓您無法使用租用戶。
  
 

## <a name="what-you-should-know"></a>您應該知道的事情 

基準原則中包含的目錄角色是具有最高特殊權限的 Azure AD 角色。 您也可以根據反饋在日後包含其他人。 

如果指令碼中的帳戶具有系統管理員特殊權限，則應該改為使用[受控服務識別 (MSI)](managed-service-identity/overview.md) 或[服務主體 (具有憑證)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)。 您暫時可以在基準原則中排除特定使用者帳戶，來解決此問題。 

此原則適用於舊有驗證流程，例如 POP、IMAP、舊版 Office 桌面用戶端。 

## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 
