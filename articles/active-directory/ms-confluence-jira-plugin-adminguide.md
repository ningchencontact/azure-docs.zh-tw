---
title: Microsoft Azure Active Directory 單一登入外掛程式管理指南 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與適用於 JIRA 的 Microsoft Azure Active Directory 單一登入之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory 單一登入外掛程式管理指南

## <a name="table-of-contents"></a>目錄

1. **[概觀](#overview)**
2. **[運作方式](#how-it-works)**
3. **[對象](#audience)**
4. **[假設](#assumptions)**
5. **[必要條件](#prerequisites)**
6. **[支援的 JIRA 和 Confluence 版本](#supported-versions-of-jira-and-confluence)**
7. **[安裝](#installation)**
8. **[外掛程式設定](#plugin-configuration)**
9. **[附加元件設定畫面的欄位說明：](#field-explanation-for-add---on-configuration-screen:)**
10. **[疑難排解](#troubleshooting)**

## <a name="overview"></a>概觀

這些附加元件可讓 Microsoft Azure AD 客戶使用其組織的使用者名稱和密碼登入以 Atlassian Jira 和 Confluence Server 為基礎的產品。 它會實作以 SAML 2.0 為基礎的 SSO。

## <a name="how-it-works"></a>運作方式

當使用者要登入 Atlassian Jira 或 Confluence 應用程式時，他們會在登入頁面上看到 [以 Azure AD 登入] 按鈕。 當使用者按下按鈕，系統會要求他們使用 Azure AD 組織登入頁面來登入。

使用者經過驗證後，應該就能登入應用程式。 如果他們已經使用組織識別碼與密碼驗證過，便可以直接登入應用程式。 另請注意，此登入在 JIRA 和 Confluence 皆有效。 如果使用者登入 JIRA 應用程式，而且 Confluence 也在相同瀏覽器視窗中開啟，他們只需要登入一次，就不需要再對其他應用程式提供認證。 使用者也可以透過 Azure 帳戶底下的 myapps 進入 Atlassian 產品，他們應該會直接登入，無須認證。

> [!NOTE]
> 使用者佈建不是使用這個附加元件來進行的。

## <a name="audience"></a>對象

計劃使用此外掛程式啟用 Azure AD SSO 的 JIRA 和 Confluence 管理員。

## <a name="assumptions"></a>假設

* JIRA/Confluence 執行個體已啟用 HTTPS
* 已在 JIRA/Confluence 中建立使用者
* 已在 JIRA/Confluence 中指派使用者的角色
* 管理員可以存取設定外掛程式所需的資訊
* 從公司網路外部也可以使用 JIRA/Confluence
* 附加元件只能在內部部署版本的 JIRA 和 Confluence 中運作

## <a name="prerequisites"></a>必要條件

請注意，需符合下列必要條件，才能繼續進行附加元件安裝：

* JIRA/Confluence 是安裝在 64 位元版本的 Windows 上
* JIRA/Confluence 版本已啟用 HTTPS
* 請注意下一節「支援的版本」中的外掛程式支援版本。
* 可從網際網路上使用 JIRA/Confluence。
* JIRA/Confluence 的管理員認證
* Azure AD 的管理員認證
* 應該在 JIRA 和 Confluence 中停用 WebSudo

## <a name="supported-versions-of-jira-and-confluence"></a>支援的 JIRA 和 Confluence 版本

目前支援下列 JIRA 和 Confluence 版本：

* JIRA 核心和軟體：6.0 至 7.2.0
* JIRA 服務台：3.0 至 3.2
* Confluence：5.0 到 5.10

## <a name="installation"></a>安裝

管理員應遵循以下步驟以安裝外掛程式：

1. 以管理員身分登入 JIRA/Confluence 執行個體
    
2. 移至 JIRA/Confluence [管理]，按一下 [附加元件]。
    
3. 在 Atlassian Marketplace 中搜尋 **Microsoft SAML SSO 外掛程式**
 
4. 適當版本的附加元件會出現在搜尋中
 
5. 選取外掛程式，UPM 會安裝它。
 
6. 安裝外掛程式之後，它會出現在 [管理附加元件] 區段的 [使用者安裝的附加元件] 區段中
 
7. 您必須先設定外掛程式，才能開始使用它。
 
8. 按一下外掛程式，您會看到 [設定] 按鈕。
 
9. 按一下按鈕以提供設定輸入
    
## <a name="plugin-configuration"></a>外掛程式設定

下圖顯示 JIRA 和 Confluence 中的附加元件設定畫面
    
![附加元件設定](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>附加元件設定畫面的欄位說明：

1.   中繼資料 URL：用來從 Azure AD 取得同盟中繼資料的 URL
 
2.   識別碼：Azure AD 用於驗證要求的來源。 這會對應至 Azure AD 中的識別碼元素。 這會由外掛程式自動衍生為 https://<domain:port>/
 
3.   回覆 URL：在您的 IdP 中使用回覆 URL 以起始 SAML 登入。 這會對應至 Azure AD 中的回覆 URL 元素。 這會由外掛程式自動衍生為 https://<domain:port>/plugins/servlet/saml/auth
 
4.   登入 URL (Sign On URL)：在您的 IdP 中使用登入 URL 以起始 SAML 登入。 這會對應至 Azure AD 中的登入 (Sign On) 元素。 這會由外掛程式自動衍生為 https://<domain:port>/plugins/servlet/saml/auth
 
5.   IdP 實體識別碼：您的 IdP 所使用的實體識別碼。 解析中繼資料 URL 時就會填入這個項目。
 
6.   登入 URL (Login URL)：來自您 IdP 的登入 URL。 解析中繼資料 URL 時 Azure AD 就會填入這個項目。
 
7.   登出 URL：來自您 IdP 的登出 URL。 解析中繼資料 URL 時 Azure AD 就會填入這個項目。
 
8.   X.509 憑證：您 IdP 的 X.509 憑證。 解析中繼資料 URL 時 Azure AD 就會填入這個項目。
 
9.   登入按鈕名稱：組織想要看到的登入按鈕名稱。 使用者會在登入畫面的登入按鈕上看到此文字。
 
10.   SAML 使用者識別碼位置：使用者識別碼在 SAML 回應中的預期位置。 可能是在 NameID 或自訂屬性名稱中。 此識別碼必須為 JIRA/Confluence 使用者識別碼。
 
11.   屬性名稱：預期出現使用者識別碼之屬性的名稱。
 
12.   啟用主領域探索：如果公司使用 ADFS 型登入，請勾選此旗標。
 
13.   網域名稱：如果是使用 ADFS 型登入，請在此提供網域名稱。
 
14.   啟用單一登出：如果您想要在使用者登出 JIRA/Confluence 時登出 Azure AD，請勾選此項目。

### <a name="troubleshooting"></a>疑難排解

* 如果您遇到多個憑證錯誤
    
    * 登入 Azure AD，移除應用程式可使用的多個憑證。 確定只剩一個憑證。

* Azure AD 中的憑證即將到期。
    
    * 附加元件會負責自動變換憑證。 當憑證即將到期時，新的憑證應該會標示為使用中，並刪除未使用的憑證。 當使用者嘗試在此情況下登入 JIRA 時，附加元件會擷取新的憑證，並儲存在外掛程式中。

* 如何停用 WebSudo (停用安全的系統管理員工作階段)
    
    * JIRA：預設會啟用安全的系統管理員工作階段 (也就是確認密碼後才能存取管理功能)。 如果您想要在 JIRA 執行個體中停用此功能，在 jira-config.properties 檔案中指定下列這一行即可停用這項功能："ira.websudo.is.disabled = true"
    
    * Confluence：依照下列 URL 中提供的步驟執行 https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* 中繼資料 URL 應該填入的欄位未填入資料
    
    * 檢查 URL 是否正確。 檢查是否已對應正確的租用戶和應用程式識別碼。
    
    * 在瀏覽器中輸入 URL，看看是否收到同盟中繼資料 XML。

* 內部伺服器錯誤：
    
    * 查看安裝記錄目錄中的記錄。 如果您是在使用者嘗試使用 Azure AD SSO 登入時遇到錯誤，您可以使用本文件下面所提供的支援資訊來分享記錄。

* 使用者嘗試登入時出現找不到使用者識別碼的錯誤
    
    * 未在 JIRA/Confluence 中建立使用者，請建立。

* 在 Azure AD 中出現找不到應用程式的錯誤
    
    * 檢查是否以正確 URL 對應至 Azure AD 中的應用程式。

* 支援詳細資料：請連繫我們：[Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 我們會在營業時間 24-48 小時內回應。
    
    * 您也可以透過 Azure 入口網站管道向 Microsoft 發出支援票證。