---
title: 什麼是 Azure Active Directory (Azure AD)？ | Microsoft Docs
description: 使用 Azure Active Directory 將現有的內部部署身分識別延伸至雲端，或開發 Azure AD 整合式應用程式。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 01183a2c85b748d593146f6b52a7d4dbd34524cb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory"></a>什麼是 Azure Active Directory？
Azure Active Directory (Azure AD) 是 Microsoft 的多租用戶雲端式目錄，也是身分識別管理服務，將核心目錄服務、應用程式存取管理、身分識別保護結合在單一解決方案中。 Azure AD 同時也提供豐富且標準型的平台，可讓開發人員根據集中式原則和規則，提供對其應用程式的存取控制。

![Azure AD Connect 堆疊](./media/active-directory-whatis/Azure_Active_Directory.png)

- **針對 IT 管理員。** Azure AD 為組織提供更安全的解決方案，使用更強的身分識別管理和單一登入 (SSO) 存取來存取數千個[雲端式 SaaS 應用程式](active-directory-saas-tutorial-list.md)和內部部署應用程式。 透過這些應用程式，您的員工也員工能享受雲端式應用程式的安全性、順暢的存取、增強的共同作業、自動化的身分識別生命週期，有助於提高安全性與合規性。

    此外，只要[按四下滑鼠](./connect/active-directory-aadconnect-get-started-express.md)，就可以將 Azure AD 與現有的 Windows Server Active Directory 整合，讓組織能夠運用現有的內部部署身分識別資產，來管理雲端式 SaaS 應用程式的存取權。

- **針對應用程式開發人員。** Azure AD 可以整合全球各地數百萬個組織所使用的身分識別管理解決方案，讓您專注於建置應用程式。

- **針對 Office 365、Azure 或 Dynamics CRM Online 的客戶。** 您已經在使用 Azure AD。 每個 Office 365、Azure、Dynamics CRM Online 租用戶實際上就是 Azure AD 租用戶，可讓您立即開始管理員工對整合後雲端應用程式的存取。

## <a name="how-reliable-is-azure-ad"></a>Azure AD 有多可靠？
Azure AD 經悉心設計，不但支援多租用戶、地理分布周全，更具有高可用性，表示您可以依賴 Azure AD 來應付最重要的商務需求。 使用世界各地具自動容錯移轉的 28 個資料中心，您能夠輕易感受到 Azure AD 非常可靠，即使資料中心中斷，您的目錄資料至少在兩個以上地域分散的資料中心都有留存複本，而且可讓您立即存取。

如需服務等級協定的詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

## <a name="choose-an-edition"></a>選擇版本
所有 Microsoft Online 商務服務都依賴 Azure Active Directory (Azure AD) 來進行登入和其他身分識別需求。 如果您訂閱任何 Microsoft Online 商務服務 (例如 Office 365、Microsoft Azure)，您就會取得 Azure AD 並可存取所有免費的功能。 利用 Azure Active Directory 免費版，您可以管理使用者和群組、與內部部署目錄同步處理、取得 Azure、Office 365 和數千個熱門 SaaS 應用程式 (像是 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等) 之間的單一登入。 

若要增強您的 Azure Active Directory，您可以使用 Azure Active Directory Basic、Premium P1 及 Premium P2 版本來新增付費功能。 付費版本的 Azure Active Directory 是建立在您現有的免費目錄上，提供的企業級功能跨越自助、增強的監視、安全性報告、Multi-Factor Authentication (MFA) 及您的行動工作力的安全存取。

> [!NOTE]
> 這兩種版本的詳細價格請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。 目前在中國不支援 Premium P1、Premium P2 及 Azure Active Directory Basic。 如需有關 Azure AD 定價的詳細資訊，請透過 Azure Active Directory 論壇與我們連絡。
>

* **Azure Active Directory Basic** - 針對具有雲端優先需求的任務背景工作角色設計，此版本提供以雲端為中心的應用程式存取和自助身分識別管理解決方案。 有了 Azure Active Directory 的 Basic 版本，您可獲得生產力增強和成本節約功能，例如：群組式存取管理、雲端應用程式的自助式密碼重設、Azure Active Directory 應用程式 Proxy (以使用 Azure Active Directory 發佈內部部署 Web 應用程式)，全都由可獲得 99.9% 運作時間的企業級 SLA 支援。
* **Azure Active Directory Premium P1** - Azure Active Directory Premium 版本的設計目的是要協助在身分識別及存取管理需求上有更嚴格要求的組織，此版本除了新增功能豐富的企業級身分識別管理功能之外，也可讓混合式使用者順暢地存取內部部署和雲端功能。 此版本包含資訊背景工作角色和混合環境中身分識別管理員對於應用程式存取、自助身分識別和存取管理 (IAM)、雲端中的身分識別保護和安全性所需的一切。 它支援進階管理和委派資源，例如：動態群組和自助群組管理。 它包含 Microsoft Identity Manager (一項內部部署及身分識別和存取管理套件)，並提供可為您的內部部署使用者啟用自助密碼重設之類解決方案的雲端回寫功能。
* **Azure Active Directory Premium P2** - 這個新方案採用了為所有使用者和系統管理員提供進階保護的設計，此方案不僅包括 Azure AD Premium P1 中的所有功能，也包括 Identity Protection 與 Privileged Identity Management。 Azure Active Directory Identity Protection 利用數十億個訊號，針對應用程式及重要公司資料提供以風險為基礎的條件式存取。 此外，我們還使用 Azure Active Directory Privileged Identity Management 來協助您管理及保護授權帳戶，讓您能夠探索、限制和監視系統管理員及他們對資源的存取，並在需要時，提供及時的存取權。  

> [!NOTE]
> 許多 Azure Active Directory 功能是透過「隨用隨付」版本提供：
>
> * Active Directory B2C 是面向消費者應用程式適用的身分識別和存取管理解決方案。 如需詳細資訊，請參閱 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)。
> * Azure Multi-Factor Authentication 可透過每一使用者或每一驗證提供者方式使用。 如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](authentication/multi-factor-authentication.md)。
>

## <a name="how-can-i-get-started"></a>如何開始使用？

**如果您是 IT 管理員：**

* [立即試用！](https://azure.microsoft.com/trial/get-started-active-directory/) - 您可以立即註冊免費的 30 天試用版，並使用此連結在 5 分鐘內部署第一個雲端解決方案

* 閱讀[開始使用 Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) 以取得獲得 Azure AD 租用戶並快速執行的秘訣和訣竅

**如果您是開發人員：**
 
* 請查看 Azure Active Directory 的 [開發人員指南](active-directory-developers-guide.md)

* [開始使用試用版](https://azure.microsoft.com/trial/get-started-active-directory/) – 立即註冊免費 30 天的試用版，並開始整合您的應用程式與 Azure AD

## <a name="next-steps"></a>後續步驟
[深入了解 Azure 身分識別和存取管理的基本概念](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
