---
title: 使用 Azure Active Directory 在 Azure Marketplace 中啟用試用版 | Azure
description: 使用 Azure Active Directory 在 Azure Marketplace 和 AppSource 中為應用程式和服務發行者啟用試用版清單類型。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160462"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>使用 Azure Active Directory 啟用試用版清單

Azure Active Directory (Azure AD) 是一種雲端身分識別服務，可使用以下業界標準架構以 Microsoft 公司或學校帳戶進行驗證。 Azure AD 支援 OAuth 和 OpenID Connect 驗證。 [Azure Marketplace](https://azuremarketplace.microsoft.com) 使用 Azure AD 來驗證您與客戶。

如需 Azure AD 的詳細資訊，請參閱 [Azure Active Directory](https://azure.microsoft.com/services/active-directory)。

在客戶於 Marketplace 中選取您的試用版清單之後，系統就會將客戶重新導向到您的試用版環境。 在試用版環境中，您可以直接設定客戶，而無須進行額外的登入步驟。 您的應用程式或供應項目會在驗證期間收到來自 Azure AD 的權杖。 權杖包含用來在應用程式或供應項目中建立使用者帳戶的寶貴使用者資訊。 您可以將將客戶設定自動化，並增加轉換的可能性。

如需有關在驗證期間從 Azure AD 傳送的權杖詳細資訊，請參閱[範例權杖](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)。

使用 Azure AD 在應用程式或試用版中啟用單鍵驗證。 Azure AD 提供下列優點︰ 
*   簡化客戶從 Marketplace 到試用版的體驗。
*   即使將使用者從 Marketplace 重新導向到您的網域或試用版環境，仍讓使用者感覺是在產品內部。
*   由於無須進行任何額外的登入步驟，因此可降低在重新導向時放棄的可能性。
*   減少眾多 Azure AD 使用者的部署障礙。

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>在 Marketplace 中確認您的 Azure AD 整合：多租用戶應用程式
使用 Azure AD 對您的解決方案支援下列選項：
*   在 Marketplace 店面中註冊您的應用程式。
*   在 Azure AD 中啟用多租用戶支援功能，以獲得單鍵試用體驗。

如需註冊應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。

如果您不熟悉 Azure AD 同盟單一登入 (SSO) 的用法，請完成下列步驟：
1.  在 Marketplace 中註冊您的應用程式。 
2.  使用 OAuth 2.0 或 OpenID Connect 來開發搭配 Azure AD 的 SSO。
    *   如需 OAuth 2.0 的詳細資訊，請參閱 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)。
    *   如需 Open ID Connect 的詳細資訊，請參閱 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)。
3.  在 Azure AD 中啟用多租用戶支援功能，以提供單鍵試用體驗。
    
    如需 AppSource 認證的詳細資訊，請參閱 [AppSource 認證](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)。 

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>在 Marketplace 中確認您的 Azure AD 整合：單一租用戶應用程式
使用 Azure AD 對單一租用戶解決方案支援下列其中一個選項： 
*   使用 Azure Active Directory B2B (Azure AD B2B) 將使用者新增至目錄作為來賓使用者。
    
    如需 Azure AD B2B 的詳細資訊，請參閱[什麼是 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。
*   使用 [與我連絡] 發佈選項，為客戶手動設定試用版。
*   開發每個客戶的「試用產品」。
*   建置可使用 SSO 的多租用戶範例示範應用程式。

## <a name="next-steps"></a>後續步驟
*   檢閱 [Azure Marketplace 和 AppSource 發行指南](./marketplace-publishers-guide.md)。
