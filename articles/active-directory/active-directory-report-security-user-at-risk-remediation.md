---
title: "Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告 | Microsoft Docs"
description: "了解 Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ed6201e9edcef39b14b948b6b2f6e0b5da01ec60
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>補救 Azure Active Directory 入口網站中標幟為有風險的使用者

利用 Azure Active Directory (Azure AD) 中的安全性報告，您可以深入了解環境中使用者帳戶被盜用的可能性。 [標幟為有風險的使用者](active-directory-identityprotection.md#users-flagged-for-risk)表示可能被盜用的使用者帳戶。

Microsoft 致力於協助保護您的環境。 為此承諾，Microsoft 會持續監視異常或與已知攻擊模式相符的活動。 


如果偵測到可能表示未經授權存取某些使用者帳戶的異常活動，您就會收到可讓您採取行動的通知。 提供通知給您，並不表示 Microsoft 的自有系統受到任何方式的危害。
 

## <a name="azure-active-directory-report-access"></a>Azure Active Directory 報告存取

您可以透過線上 Azure Active Directory 報告檢閱標幟為有風險的使用者。 如果您不是 Azure 的訂閱者，則可在 [http://aka.ms/AccessAAD](http://aka.ms/AccessAAD) 進行免費訂閱程序。  
完成後，您可以使用 Office 365 認證來存取 Azure 系統管理中心。 請注意，在基本訂閱等級提供的詳細資料數量有限。 其他資料和分析資料則適用於 Azure 進階訂閱者。 如需詳細資訊，請參閱 [Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告](active-directory-reporting-security-user-at-risk.md)。

當您啟動您的 Azure AD 存取時，您就會重新導向至 [Azure AD 入口網站](https://portal.azure.com)。 若要直接移至此報告，請瀏覽至下列 URL：[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk)。

**若要在 Office 365 系統管理中心存取標幟為有風險的使用者報告：**

1.  在左側的導覽功能表中，按一下 [系統管理中心]。 
2.  按一下 [Azure AD]。
3.  登入 [Azure Active Directory 系統管理中心]。
4.  如果頁面頂端顯示的橫幅表示「查看新的入口網站」，請按一下此連結。
4.  在左側導覽功能表中，按一下 [Azure Active Directory]。 
5.  在瀏覽窗格中，按一下 [標幟為有風險的使用者] 下的 [安全性]。

檢閱此處顯示的資訊。 您應該為此處所列的任何帳戶重設密碼。 

## <a name="remediation-actions"></a>補救動作

採取下列動作來協助改正受影響的帳戶以及保護您的環境：

1.  [驗證](http://aka.ms/MFAValid)正確的資訊，以進行多重要素驗證和自助式密碼重設。 
2.  對所有使用者[啟用](http://aka.ms/MFAuth)多重要素驗證。 
3.  使用此[補救指令碼](http://aka.ms/remediate)，對於每個受影響的帳戶，您可以自動執行下列步驟： 

    a. 重設密碼以保護帳戶及刪除作用中的工作階段。

    b. 移除信箱委派。

    c. 停用外部網域的郵件轉寄規則。

    d. 移除信箱上的全域郵件轉寄屬性。

    e. 在使用者帳戶上啟用 MFA。

    f. 將帳戶的密碼複雜性設定為很高。

    g. 啟用信箱稽核。

    h. 產生稽核記錄，以供系統管理員檢閱。

4. 調查您的 Office 365 租用戶和其他 IT 基礎結構是否可能修改，包括檢閱所有租用戶設定、使用者帳戶，以及每個使用者的組態設定。 檢查持續性方法的指標，以及入侵者可能已利用初始據點來取得 VPN 認證，或存取其他組織資源的指標。 

5.  在調查過程中，請考慮是否應該或必須通知政府機關，包括執法機關。

此外，您應該：

- 讀取及實作處理異常活動的這個[指引](http://aka.ms/fixaccount)。 
- [啟用稽核管線](http://aka.ms/improvesecurity)可協助您分析您租用戶的活動。 完成後，您的稽核存放區會開始填入所有的活動記錄。 此時，您也能夠運用[安全和規範中心的搜尋和調查](http://aka.ms/sccsearch)。 
- 使用此[指令碼](http://aka.ms/mailboxaudit1)來為所有帳戶啟用信箱稽核。 
- 檢閱您所有信箱的委派權限和郵件轉寄規則。 您可以使用此 [PowerShell 指令碼](http://aka.ms/delegateforwardrules)來執行此工作。 



## <a name="next-steps"></a>後續步驟

- 如需 Azure Active Directory Identity Protection 的詳細資訊，請參閱 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)。

