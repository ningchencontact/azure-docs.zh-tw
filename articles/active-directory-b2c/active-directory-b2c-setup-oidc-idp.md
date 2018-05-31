---
title: 在內建原則中新增 OpenID Connect 識別提供者 - Azure AD B2C | Microsoft Docs
description: 如何在 Azure AD B2C 的內建原則中新增 OpenID Connect 提供者的概觀指南
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.assetid: 357d193a-e33b-469c-8a93-0a8f45a60a9f
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.openlocfilehash: 46223f1ec6b82828983861dae26ec8d777b77b86
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308979"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C︰在內建原則中新增自訂 OpenID Connect 識別提供者

>[!NOTE]
> 這項功能處於公開預覽狀態。 請勿在生產環境中使用這項功能。

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 是以 OAuth 2.0 為基礎的驗證通訊協定，可用於讓使用者安全地登入。 Azure AD B2C 支援使用此通訊協定的大部分識別提供者，例如[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md) 本文說明如何將自訂 OpenID Connect 識別提供者新增至內建原則中。

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>設定自訂 OpenID Connect 識別提供者

若要新增自訂 OpenID Connect 識別提供者：

1. 遵循下列步驟，以在 Azure 入口網站中[瀏覽至 Azure AD B2C 設定](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
1. 按一下 [識別提供者]。
1. 按一下 [新增]。
1. 針對 [識別提供者類型]，選取 [OpenID Connect]。

### <a name="setting-up-the-openid-connect-identity-provider"></a>設定 OpenID Connect 識別提供者

#### <a name="metadata-url"></a>中繼資料 URL

依據規格，每個 OpenID Connect 識別提供者所描述的中繼資料文件都包含執行登入所需的大部分資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 OpenID Connect 中繼資料文件一律位於以 `.well-known\openid-configuration` 結尾的端點上。

針對您想要新增的 OpenID Connect 識別提供者，請輸入其中繼資料 URL。

#### <a name="client-id-and-secret"></a>用戶端識別碼和祕密

若要允許使用者登入，識別提供者需要求開發人員在其服務中註冊應用程式。 此應用程式會有識別碼 (稱為**用戶端識別碼**) 和**用戶端祕密**。 從識別提供者複製這些值，然後在對應的欄位中輸入這些值。

> [!NOTE]
> 用戶端祕密為選擇性項目。 不過，如果您想要使用[授權碼流程](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) (其使用祕密將程式碼換成權杖)，您必須輸入用戶端祕密。

#### <a name="scope"></a>Scope

範圍可定義您想要從自訂識別提供者蒐集的資訊和權限。 OpenID Connect 要求必須包含 `openid` 範圍值，以便接收來自識別提供者的識別碼權杖。 沒有識別碼權杖，使用者將無法使用自訂識別提供者登入 Azure AD B2C。

可以附加其他範圍 (以空格分隔)。 請參閱自訂識別提供者的文件，查看其他可用的範圍。

#### <a name="response-type"></a>回應類型

回應類型描述在自訂識別提供者的 `authorization_endpoint` 初始呼叫中可傳回何種資訊。 

* `code`：依照[授權碼流程](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)，將授權碼傳回至 Azure AD B2C。 Azure AD B2C 會繼續呼叫 `token_endpoint` 以將程式碼換成權杖。
* `token`：自訂識別提供者會將存取權杖傳回至 Azure AD B2C。
* `id_token`：自訂識別提供者會將識別碼權杖傳回至 Azure AD B2C。


#### <a name="response-mode"></a>回應模式

回應模式可定義應用於將資料從自訂識別提供者傳送回 Azure AD B2C 的方法。

* `form_post`：建議使用此回應模式，以獲得最佳安全性。 回應會透過 HTTP `POST` 方法傳輸，並使用 `application/x-www-form-urlencoded` 格式在主體中將程式碼或權杖編碼。
* `query`：傳回程式碼或權杖作為查詢參數。


#### <a name="domain-hint"></a>網域提示

網域提示可用於直接跳到指定識別提供者的登入頁面，使用者不需在可用的識別提供者清單中進行選取。 若要允許這種行為，請輸入網域提示的值。

若要跳至自訂識別提供者，請在呼叫 Azure AD B2C 進行登入時，將參數 `domain_hint=<domain hint value>` 附加至您的要求結尾。


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>對應來自 OpenID Connect 識別提供者的宣告

在自訂識別提供者將識別碼權杖傳送回 Azure AD B2C 之後，Azure AD B2C 必須能夠將來自所接收權杖的宣告對應至 Azure AD B2C 可辨識及使用的宣告。 

對於以下每個對應，請參閱自訂識別提供者的文件，以了解在識別提供者的權杖中傳回的宣告。

* `User ID`：輸入可為已登入使用者提供唯一識別碼的宣告。
* `Display Name`：輸入可為使用者提供顯示名稱或完整名稱的宣告。
* `Given Name`：輸入可提供使用者名字的宣告。
* `Surname`：輸入可提供使用者姓氏的宣告。
* `Email`：輸入可提供使用者電子郵件地址的宣告。

## <a name="next-steps"></a>後續步驟

將自訂 OpenID Connect 識別提供者新增至您的[內建原則](active-directory-b2c-reference-policies.md)。
