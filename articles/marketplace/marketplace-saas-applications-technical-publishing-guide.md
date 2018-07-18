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
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809466"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 應用程式技術發行指南

歡迎使用 Azure Marketplace SaaS 應用程式技術發行指南。 本指南旨在協助候選發行者和現有發行者，讓其能夠使用 SaaS 應用程式供應項目來列出其在 Azure Marketplace 中的應用程式和服務。 

為了讓您更了解如將發行 SaaS 供應項目，本指南分為下列各節：
* 供應項目概觀
* 商業需求
* 技術需求
* 發佈程序
* 使用 Azure Active Directory 啟用試用版
* 向 Marketplace 證明您的 Azure Active Directory 整合

## <a name="offer-overview"></a>供應項目概觀  

兩個 Azure 店面皆提供 SaaS 應用程式，下表說明目前可用的選項：

| 店面選項 | 列出清單 | 試用版/交易 |  
| --- | --- | --- |  
| AppSource | 是 (與我連絡) | 是 (PowerBI/Dynamics) |
| Azure Marketplace | 否 | 是 (SaaS 應用程式) |   

**列出清單：**「列出清單」發行選項包含「與我連絡」的供應項目類型，當試用版層級或交易層級的參與方式不可行時，適用此方法。 此方法的優點是可讓發行者的解決方案上市，立即開始接收可能產生交易的潛在客戶，進而提升業績。  
**試用版/交易：** 客戶可以選擇直接購買或要求試用您的解決方案。 提供試用版體驗會提升客戶的參與度，並可讓客戶先探索您的解決方案，再進行購買。 使用試用版體驗，較可能在店面獲得推廣機會，而且從客戶參與而來的潛在客戶也應會更多、更廣泛。 試用版必須至少包括在試用期間提供免費支援。  

| SaaS 應用程式供應項目 | 商業需求 | 技術需求 |  
| --- | --- | --- |  
| **與我們連絡** | yes | 否 |  
| **PowerBI / Dynamics** | yes | 是 (Azure AD 整合) |  
| **SaaS 應用程式**| yes | 是 (Azure AD 整合) |     

如需有關 Marketplace 店面和每個發行選項說明的詳細資訊，請參閱 [Marketplace 發行者指南](https://aka.ms/sellerguide)和[發行選項](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option)。

## <a name="business-requirements"></a>商業需求
您可以平行完成 SaaS 供應項目的商業需求與技術需求。 大部分業務需求與資訊都會在您從 Cloud Partner 入口網站中建置 SaaS 供應項目時進行收集。 商業需求如下所示： 
* 同意參與原則
* 與 Microsoft 整合 
* 識別供應項目的對象
* 定義與決定要使用的前置管理
* 設定隱私權原則及使用規定
* 定義支援連絡人  

您可以在 [Marketplace 發行的必要條件](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)主題中找到詳細資訊

## <a name="technical-requirements"></a>技術需求

SaaS 應用程式的技術需求很簡單。 發行者只需要與 Azure Active Directory (Azure AD) 整合即可發行。 Azure AD 與應用程式的整合已有完善的記載，Microsoft 會提供多個 SDK 和資源來完成這項作業。  

一開始，建議您備妥專用於 Azure Marketplace 發行的訂用帳戶，以便您可以讓此工作與其他計劃分開進行。 準備好之後，您就可以開始在此訂用帳戶中部署 SaaS 應用程式，並開始進行開發工作。  

您可以在下列網站取得最適合的 Azure Active Directory 文件、範例和指引： 

* [Azure Active Directory 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [與 Azure Active Directory 整合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure 藍圖 - 安全性與身分識別](https://azure.microsoft.com/roadmap/?category=security-identity)

如需影片教學課程，請檢閱下列內容：

* [以 Vittorio Bertocci 進行的 Azure Active Directory 驗證](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory 身分識別技術簡報 - 第 1 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory 身分識別技術簡報 - 第 2 部分 (共 2 部分)](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [使用 Microsoft Azure Active Directory 建置應用程式](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [內容重點放在 Active Directory 的 Microsoft Azure 影片](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

免費的 Azure Active Directory 訓練如下  
* [IT 專業人員的 Microsoft Azure 內容系列：Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

此外，Azure Active Directory 提供可檢查服務更新的網站   
* [Azure AD 服務更新](https://azure.microsoft.com/updates/?product=active-directory)

如需支援，您可以使用下列資源：
* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>發佈程序

SaaS 發行程序分為技術和商業步驟。  大部分用於開發和整合 Azure Active Directory 的工作，都可以平行用來完成供應項目的商業需求。 大量商業需求都屬於 Cloud Partner 入口網站中的 SaaS 應用程式供應項目組態。  
下圖顯示試用版/交易供應項目的主要發行步驟：  

![SaaS 發行步驟](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

每個主要發行步驟的說明如下表所示：  

| 發行步驟 | 說明 |   
| --- | --- |  
| **建立 SaaS 應用程式** | 登入 Cloud Partner 入口網站，選取 [新增]，然後選取 **SaaS 應用程式**供應項目。 |  
| **建立與 Azure AD 的整合** | 請遵循前一節所述的技術需求，將您的 SaaS 供應項目與 Azure AD 整合。 |  
| **配置供應項目的設定**| 輸入所有 SaaS 供應項目初始資訊。 您想要使用的供應項目識別碼和供應項目名稱。 |     
| **設定技術資訊** | 輸入供應項目的技術資訊。 針對 SaaS 應用程式，供應項目取得按鈕 (免費、追蹤或與我連絡) 的解決方案 URI 與類型是必要項目。 |  
| **試用產品 (選擇性)** | 這是試用版的選擇性類型，大部分其他類型的 Marketplace 供應項目皆需要此項目。 它可讓您在發行者 (不是終端消費者) 的訂用帳戶中部署試用版。 |  
| **設定供應項目的店面素材**| 在這一節中，發行者將連結和上傳標誌、行銷素材、法律文件，以及設定潛在客戶管理系統。 |
| **設定供應項目連絡人** | 輸入 SaaS 供應項目的工程連絡人和支援連絡資訊。 |  
| **確認 SaaS 應用程式已與 Azure AD 整合** | 在提交您的 SaaS 應用程式以進行發行之前，您必須確認應用程式已與 Azure AD 整合 |  
| **發行供應項目**| 完成供應項目和技術的資產之後，您就可以提交供應項目。 這會啟動發行程序，其中解決方案範本需經過測試、驗證、認證和核准後，才能發行。 |

## <a name="using-azure-active-directory-to-enable-trials"></a>使用 Azure Active Directory 啟用試用版  

Microsoft 可使用 Azure AD 來驗證所有 Marketplace 使用者，因此已驗證的使用者點選試用版清單並重新導向至試用版環境時，您可以將使用者直接佈建置至試用版，而不需要額外的登入步驟。 應用程式在驗證期間從 Azure AD 收到的權杖，包括可用以在應用程式中建立使用者帳戶的寶貴使用者資訊，讓您將佈建體驗自動化並增加轉換的可能性。 如需有關權杖的詳細資訊，請參閱[權杖範例](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

使用 Azure AD 對應用程式或試用版啟用單鍵驗證，可達到以下效果：  
* 簡化客戶從 Marketplace 至試用版的體驗。  
* 即使將使用者從 Marketplace 重新導向至您的網域或試用版環境，依然覺得是在「產品內部」。  
* 因為沒有額外的登入步驟，所以可減少在重新導向時放棄的可能性。  
* 減少眾多 Azure AD 使用者的部署障礙。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>向 Marketplace 證明您的 Azure Active Directory 整合  

您可以使用幾個不同的方式來證明 Azure AD 整合，而這取決於您的應用程式是單一租用戶或多租用戶，以及您是初次接觸 Azure AD 同盟單一登入 (SSO)，還是已經支援此項目。  

**對於多租用戶應用程式：**  

如果您已支援 Azure AD，請執行下列動作：
1.  在 Azure 入口網站註冊您的應用程式
2.  在 Azure AD 中啟用多租用戶支援功能，以獲得「單鍵」試用體驗。 您可以在[此處](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)找到更多特定資訊。  

如果您剛剛接觸 Azure AD 同盟 SSO，請執行下列動作： 
1.  在 Azure 入口網站註冊您的應用程式
2.  使用 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 或 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)，開發透過 Azure AD 的 SSO。
3.  啟用 AAD 中的多租用戶支援功能以獲得「單鍵」試用版體驗，您可以在[此處](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)找到更多特定資訊。  

**對於單一租用戶應用程式，請使用下列任一選項：**  
* 使用 [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 將使用者新增至目錄作為來賓使用者
* 使用「與我連絡」為客戶手動佈建試用版
* 開發每個客戶的「試用產品」
* 建置使用 SSO 的多租用戶範例示範應用程式

