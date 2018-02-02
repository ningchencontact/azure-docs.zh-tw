---
title: "Azure Active Directory Domain Services：針對服務主體設定進行疑難排解 | Microsoft Docs"
description: "針對 Azure AD Domain Services 的服務主體設定進行疑難排解"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD Domain Services - 針對服務主體設定進行疑難排解

為服務、管理及更新網域，Microsoft 會使用各種[服務主體](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)來與目錄通訊。 如果其中一個服務主體未正確設定或已刪除，就可能會對服務造成干擾。

## <a name="aadds102-service-principal-not-found"></a>AADDS102：找不到服務主體

**警示訊息：**

*Azure AD 目錄中已刪除要讓 Azure AD Domain Services 正常運作所需的服務主體。此設定會影響 Microsoft 監視、管理、修補及同步處理受控網域的能力。*

服務主體是 Microsoft 用來管理、更新和維護受控網域的應用程式。 如果將它們刪除，就會使 Microsoft 無法為網域提供服務。 請使用下列步驟來判斷必須重新建立的服務主體。

1. 在 Azure 入口網站中，瀏覽至[企業應用程式 - 所有應用程式](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)頁面。
2. 使用 [顯示] 下拉式清單，選取 [所有應用程式]，然後按一下 [套用]。
3. 使用下表，將應用程式識別碼貼到搜尋方塊然後按 Enter，以搜尋各個應用程式識別碼。 如果搜尋結果是空的，您就必須遵循「解決方案」資料行中的步驟來重新建立服務主體。

| 應用程式識別碼 | 解決方案 |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [使用 PowerShell 重新建立遺失的服務主體](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [重新註冊 Microsoft.AAD 命名空間](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [重新註冊 Microsoft.AAD 命名空間](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [會自動更正的服務主體](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>使用 PowerShell 重新建立遺失的服務主體

遵循下列步驟來尋找遺失的識別碼：2565bd9d-da50-47d4-8b85-4c97f669dc36

**補救：**

您需要 Azure AD PowerShell 才能完成這些步驟。 如需安裝 Azure AD PowerShell 的相關資訊，請參閱[這篇文章](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)。

若要解決此問題，請在 PowerShell 視窗中輸入下列命令：
1. Install-Module AzureAD
2. Import-Module AzureAD
3. 執行下列 PowerShell 命令，檢查目錄中是否遺失 Azure AD Domain Services 所需的服務主體：
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. 輸入下列 PowerShell 命令，以建立服務主體：
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. 在遺失的服務主體建立好之後，請等待兩個小時再檢查網域的健康情況。


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>使用 Azure 入口網站重新註冊 Microsoft AAD 命名空間

遵循下列步驟來尋找遺失的識別碼：443155a6-77f3-45e3-882b-22b3a8d431fb 和 abba844e-bc0e-44b0-947a-dc74e5d09022

**補救：**

使用下列步驟，在目錄上還原 Domain Services：

1. 在 Azure 入口網站中，瀏覽至[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)頁面。
2. 從與受控網域相關聯的資料表中選擇訂用帳戶
3. 使用左側導覽，選擇 [資源提供者]
4. 在資料表中搜尋 "Microsoft.AAD"，然後按一下 [重新註冊]
5. 若要進行檢查以確定警示已解決，請在兩個小時後檢視您的 [健康情況警示] 頁面。


### <a name="service-principals-that-self-correct"></a>會自動更正的服務主體

遵循下列步驟來尋找遺失的識別碼：d87dcbc6-a371-462e-88e3-28ad15ec4e64

**補救：**

當特定服務主體遺失、設定不正確或遭到刪除時，Microsoft 可識別出來。 為快速解決服務的問題，Microsoft 會自行重新建立服務主體。 請在兩個小時之後檢查網域的健康情況，確保其已重新建立主體。

## <a name="contact-us"></a>與我們連絡
請連絡 Azure Active Directory Domain Services 產品小組， [分享意見或尋求支援](active-directory-ds-contact-us.md)。
