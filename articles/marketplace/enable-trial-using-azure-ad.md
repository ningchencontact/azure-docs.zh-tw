---
title: 使用 Azure AD 來啟用試用版 | Azure
description: 使用 Azure Active Directory (Azure AD) 在 Azure Marketplace 和 AppSource 中為應用程式和服務發行者啟用試用版清單類型
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825889"
---
# <a name="enable-trial-using-azure-ad"></a>使用 Azure AD 來啟用試用版  
Azure Active Directory (Azure AD) 是一種雲端身分識別服務，可使用以下業界標準架構以 Microsoft 公司或學校帳戶進行驗證：OAuth 與 OpenID Connect。  
*   如需有關 Azure AD 的詳細資訊，請瀏覽 Azure Active Directory 頁面：[azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory)。  

您和您的客戶會使用 Azure AD 在市集上進行驗證。 在客戶於市集上選取您的試用版清單之後，系統就會將客戶重新導向到您的試用版環境。  在試用版環境中，您可以直接設定客戶，而無須進行額外的登入步驟。 您的應用程式或供應項目會在驗證期間從 Azure AD 收到權杖，其中包含用來在您應用程式或供應項目中建立使用者帳戶的寶貴使用者資訊。 您可以將設定自動化，並增加轉換的可能性。  
*   如需有關在驗證期間從 Azure AD 所傳送權杖的詳細資訊，請瀏覽＜權杖範例＞一節：[docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

使用 Azure AD 在應用程式或試用版中啟用單鍵驗證。  
*   簡化客戶從市集到試用版的體驗。  
*   即使將使用者從 Marketplace 重新導向到您的網域或試用版環境，仍讓使用者感覺是在產品內部。  
*   由於無須進行任何額外的登入步驟，因此可降低在重新導向時放棄的可能性。  
*   減少眾多 Azure AD 使用者的部署障礙。  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>確認您 Azure AD 的市集相關整合：多租用戶應用程式  
針對使用 Azure AD 的解決方案支援下列選項。  
*   在市集店面中註冊您的應用程式。  
*   在 Azure AD 中啟用多租用戶支援功能，以獲得單鍵試用體驗。  
    *   如需有關應用程式註冊的詳細資訊，請瀏覽「整合應用程式與 Azure Active Directory」頁面：[docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。  

如果您不熟悉 Azure AD 同盟單一登入 (SSO) 的用法，則請依照下列步驟進行操作。  
1.  在市集上註冊您的應用程式。 
2.  使用 OAuth 2.0 或 OpenID Connect 來開發搭配 Azure AD 的 SSO。  
    *   如需有關 OAuth 2.0 的詳細資訊，請瀏覽 OAuth 2.0 頁面：[docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)。  
    *   如需有關 Open ID Connect 的詳細資訊，請瀏覽 OpenID Connect 頁面：[docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)。  
3.  在 Azure AD 中啟用多租用戶支援功能，以提供單鍵試用體驗。  
    *   如需有關 AppSource 認證的詳細資訊，請瀏覽 AppSource 認證頁面：[docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)。 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>確認您 Azure AD 的市集相關整合：單一租用戶應用程式  
針對單一租用戶解決方案支援下列其中一個選項。  
*   使用 Azure AD B2B 將使用者新增至目錄作為來賓使用者。  
    *   如需有關 Azure AD B2B 的詳細資訊，請瀏覽「什麼是 Azure AD B2B 共同作業」頁面：[docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。
*   使用「與我連絡」為客戶手動設定試用版。  
*   開發每個客戶的「試用產品」。  
*   建置使用 SSO 的多租用戶範例示範應用程式。  

## <a name="next-steps"></a>後續步驟
*   請瀏覽 [Azure Marketplace 和 AppSource 發行者指南](./marketplace-publishers-guide.md)頁面。  
 
---  

