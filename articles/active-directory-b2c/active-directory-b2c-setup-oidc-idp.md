---
title: 使用 OpenID Connect 設定註冊並登入 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C 透過 OpenID Connect 設定註冊和登入。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: de0c866889f15f096bea26915f58cd4e268dfed3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428750"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 透過 OpenID Connect 設定註冊和登入

>[!NOTE]
> 這項功能處於公開預覽狀態。 請勿在生產環境中使用這項功能。


[OpenID Connect](active-directory-b2c-reference-oidc.md) 是以 OAuth 2.0 為基礎的驗證通訊協定，可用於讓使用者安全地登入。 Azure AD B2C 中支援使用此通訊協定的大部分識別提供者。 本文說明如何將自訂 OpenID Connect 識別提供者新增至使用者流程中。

## <a name="add-the-identity-provider"></a>新增識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
4. 選取 [識別提供者]，然後按一下 [新增]。
5. 針對 [身分識別提供者類型]，選取 [OpenID Connect (預覽)]。

## <a name="configure-the-identity-provider"></a>設定識別提供者

每個 OpenID Connect 識別提供者所說明的中繼資料文件都包含執行登入所需的大部分資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 OpenID Connect 中繼資料文件一律位於以 `.well-known\openid-configuration` 結尾的端點上。 針對您想要新增的 OpenID Connect 識別提供者，請輸入其中繼資料 URL。

為允許使用者登入，識別提供者會要求開發人員在其服務中註冊應用程式。 此應用程式具有稱為**用戶端識別碼**的識別碼與**用戶端祕密**。 從識別提供者複製這些值，然後在對應的欄位中輸入這些值。

> [!NOTE]
> 用戶端祕密為選擇性項目。 不過，如果您想要使用[授權碼流程](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) (其使用祕密將程式碼換成權杖)，您必須輸入用戶端祕密。

範圍會定義您想要從自訂識別提供者蒐集的資訊與權限。 OpenID Connect 要求必須包含 `openid` 範圍值，以便接收來自識別提供者的識別碼權杖。 沒有識別碼權杖，使用者就無法使用自訂識別提供者來登入 Azure AD B2C。 您可以附加其他範圍 (以空格分隔)。 請參閱自訂識別提供者的文件，查看其他可用的範圍。

回應類型會說明要在對自訂識別提供者進行的 `authorization_endpoint` 初始呼叫中傳回哪種資訊。 您可以使用下列回應類型：

- `code`：依照[授權碼流程](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)，將授權碼傳回至 Azure AD B2C。 Azure AD B2C 會繼續呼叫 `token_endpoint` 來交換權杖的授權碼。
- `token`：存取權杖會從自訂識別提供者傳回到 Azure AD B2C。
- `id_token`：識別碼權杖會從自訂識別提供者傳回到 Azure AD B2C。

回應模式可定義應用於將資料從自訂識別提供者傳送回 Azure AD B2C 的方法。 您可以使用下列回應模式：

- `form_post`：建議使用此回應模式，以獲得最佳安全性。 回應會透過 HTTP `POST` 方法傳輸，並使用 `application/x-www-form-urlencoded` 格式在主體中將程式碼或權杖編碼。
- `query`：傳回授權碼或權杖作為查詢參數。

網域提示可用於直接跳到指定識別提供者的登入頁面，使用者不需在可用的識別提供者清單中進行選取。 若要允許這種行為，請輸入網域提示的值。 若要跳至自訂識別提供者，請在呼叫 Azure AD B2C 進行登入時，將參數 `domain_hint=<domain hint value>` 附加至您的要求結尾。

在自訂識別提供者將識別碼權杖傳送回 Azure AD B2C 之後，Azure AD B2C 必須能夠將來自所接收權杖的宣告對應至 Azure AD B2C 可辨識及使用的宣告。 針對下列每個對應，請參閱自訂識別提供者的文件，以了解在識別提供者的權杖中傳回的宣告：

- `User ID`：輸入可為已登入使用者提供唯一識別碼的宣告。
- `Display Name`：輸入可為使用者提供顯示名稱或完整名稱的宣告。
- `Given Name`：輸入可提供使用者名字的宣告。
- `Surname`：輸入可提供使用者姓氏的宣告。
- `Email`：輸入可提供使用者電子郵件地址的宣告。

