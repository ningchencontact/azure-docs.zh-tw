---
title: Azure Marketplace SaaS 應用程式技術發行指南
description: 將 SaaS 應用程式發行至 Azure Marketplace 的逐步指南與發行檢查清單
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 應用程式技術發行指南

歡迎使用 Azure Marketplace SaaS 應用程式技術發行指南。 本指南旨在協助候選發行者和現有發行者，讓其能夠使用 SaaS 應用程式供應項目來列出其在 Azure Marketplace 中的應用程式和服務。  
當您的解決方案會部署在您自己的 Azure 訂用帳戶中，且客戶會透過您所設計和管理的介面登入以測試應用程式時，建議您使用 SaaS 應用程式供應項目。 其做法是使用 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) 以充分利用您現有的試用環境。 換句話說，它是客戶導向、合作夥伴託管的免費試用。 請務必透過以下方法公開您的解決方案：讓雲端購買者有機會免費獨立體驗您的解決方案，從而讓此供應項目類型提供與客戶搜尋雲端解決方案之方式相符的試用體驗。  

如需所有其他 Marketplace 供應項目的概觀，請參閱 [Marketplace 發行者指南](https://aka.ms/sellerguide)。

## <a name="saas-application-technical-guidance"></a>SaaS 應用程式技術指導
SaaS 應用程式的技術需求很簡單。 發行者只需要與所要發行的 Azure AD 整合即可。  Azure AD 與應用程式的整合已有完善的記載，Microsoft 會提供多個 SDK 和資源來完成這項作業。  

一開始，建議您備妥專用於 Azure Marketplace 發行的訂用帳戶，以便您可以讓此工作與其他計劃分開進行。 此外，也建議您在開發環境中備妥下列工具 (如果您尚未安裝)： 
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Azure 開發人員工具 (檢閱可用項目)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>資源
下列清單會提供可協助您開始工作之最佳 Azure AD 資源的連結： 

**Documentation**

- [Azure Active Directory 開發人員指南](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [與 Azure Active Directory 整合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [整合應用程式與 Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Azure 藍圖 - 安全性與身分識別](https://azure.microsoft.com/roadmap/?category=security-identity)

**影片**

- [以 Vittorio Bertocci 進行的 Azure Active Directory 驗證](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory 身分識別技術簡報 - 第 1 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory 身分識別技術簡報 - 第 2 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [使用 Microsoft Azure Active Directory 建置應用程式](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [內容重點放在 Active Directory 的 Microsoft Azure 影片](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**訓練**  
- [IT 專業人員的 Microsoft Azure 內容系列：Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Azure Active Directory 服務更新**  
- [Azure AD 服務更新](https://azure.microsoft.com/updates/?product=active-directory)

如需支援，您可以使用下列資源：
- [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Azure Active Directory 資源庫
您除了可在 Azure Marketplace/AppSource 中列出應用程式外，也可以在屬於 Azure Marketplace AppStore 一部分的 Azure AD 應用程式庫中列出應用程式。 使用 Azure AD 作為識別提供者的客戶可找到在此發佈的不同 SaaS 應用程式連接器。 IT 系統管理員可從應用程式庫新增連接器，然後設定並使用這些連接器進行單一登入 (SSO) 和佈建。 Azure AD 支援所有主要的同盟通訊協定進行 SSO，包括 SAML 2.0、OpenID Connect、OAuth 及 WS-Fed。  

在測試過應用程式可與 Azure AD 整合之後，在我們的應用程式網路入口網站提交存取要求。 如果您有 Office 365 帳戶，請使用該帳戶登入入口網站。 如果您沒有 Office 365 帳戶，則可以使用您的 Microsoft 帳戶 (例如 Outlook 或 Hotmail) 來登入。

## <a name="program-benefits"></a>方案權益
- 為客戶提供可能的最佳單一登入體驗
- 針對應用程式只需進行簡單且最基本的設定
- 客戶可以搜尋應用程式並在資源庫中找到它
- 不論客戶使用的 Azure AD SKU 為何 (「免費」、「基本」還是「進階」)，他們都可以使用此整合
- 適用於共同客戶的逐步設定教學課程
- 如果您使用的是 SCIM，可讓您針對相同應用程式進行使用者部署

## <a name="prerequisites"></a>先決條件
若要在 Azure AD 資源庫中列出某個應用程式，該應用程式必須先實作 Azure AD 所支援的其中一種同盟通訊協定。 請閱讀 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)中的 Azure AD 應用程式庫條款及條件。  

以下會說明根據您所使用之通訊協定所應具備的其他必要條件資訊：

**OpenID Connect** - 在 Azure AD 中建立多租用戶應用程式，然後為您的應用程式實作同意架構。 將登入要求傳送給通用端點，以便讓所有客戶都可以向該應用程式表示同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制客戶使用者存取權。  
**SAML 2.0 或 WS-Fed** - 您的應用程式應該要具備在 SP 或 IdP 模式下進行 SAML 或 WS-Fed SSO 整合的能力。
