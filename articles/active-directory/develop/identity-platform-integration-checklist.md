---
title: 與 Microsoft 身分識別平台整合 |Azure
description: 當手寫筆與 Microsoft 身分識別平台 (v2.0) 整合時，深入了解最佳做法與常見疏漏。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 1f5a6d7501fd387548b111fcb0888515c8f8db25
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076401"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Microsoft 身分識別平台整合檢查清單

Microsoft 身分識別平台整合檢查清單的目的是引導您以高品質且安全的整合。 它會反白顯示的最佳作法，並與 Microsoft 身分識別平台整合時的常見疏漏需要檢閱的定期執行，藉此確定您維持品質和安全性與身分識別平台的應用程式整合上的清單。 檢查清單不想要檢閱整個應用程式。 檢查清單的內容會有所變更，當我們在平台的改進。

如果您剛開始使用，請參閱[文件](index.yml)若要了解驗證基本概念、 Microsoft 身分識別平台，在應用程式案例和更多功能。

## <a name="testing-your-integration"></a>測試您的整合

使用下列檢查清單，以確保您的應用程式會與有效地整合[Microsoft 身分識別平台](https://docs.microsoft.com/legal/mdsa)。

### <a name="basics"></a>基本概念

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 閱讀並了解[Microsoft 平台原則](https://docs.microsoft.com/legal/mdsa)。 請確定您的應用程式遵守條款所述，專門用來保護使用者與平台。 |

### <a name="ownership"></a>擁有權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 請確定您用來註冊和管理應用程式的帳戶相關聯的資訊是最新狀態。 |

### <a name="branding"></a>商標

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 遵守[商標指導方針的應用程式](howto-add-branding-in-azure-ad-apps.md)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 您的應用程式，請提供有意義的名稱和標誌。 這項資訊會出現在您的應用程式的同意提示。 請確定您的名稱和標誌是代表您的公司/產品，因此使用者可以做出明智的決策。 請確定您在不違反任何的商標。 |

### <a name="privacy"></a>隱私權

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 提供您的應用程式的服務及隱私權聲明的條款的連結。 |

### <a name="security"></a>安全性

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 維護所有重新導向 Uri 的擁有權，並保持它們的 DNS 記錄。 請勿在您的 Uri 中使用萬用字元 （*）。 針對 web 應用程式，請確定所有 Uri 都是安全且加密 （例如，使用 https 配置）。 公用用戶端，請使用平台特定重新導向 Uri，如果適用的話 （主要是針對 iOS 和 Android）。 否則，請使用重新導向 Uri，具有高容量的隨機性，若要避免發生衝突時回呼您的應用程式。 如果從獨立的 web 代理程式正在使用您的應用程式，您可以使用 https://login.microsoftonline.com/nativeclient。 檢閱並修剪所有未使用或不需要重新導向 Uri 以規則為基礎。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您的應用程式註冊在目錄中，最小化，並手動監視應用程式註冊擁有者的清單。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 未啟用支援[OAuth2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)除非有明確的需求。 深入了解有效的案例[此處](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 不用[資源擁有者密碼認證流程 (ROPC)](v2-oauth-ropc.md)，直接處理使用者的密碼。 此流程需要較高程度的信任和使用者的風險，而且應該只用於其他、 更安全的流程無法使用時。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 保護和管理您的應用程式的認證。 使用[憑證認證](active-directory-certificate-credentials.md)，而非密碼認證 （用戶端祕密）。 如果您必須使用密碼認證，不需要設定它以手動方式。 不要將認證儲存在程式碼或組態中，並永遠不會允許他們處理讓人判讀。 可能的話，請使用[管理 Azure 資源的身分識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)或[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)來儲存和定期輪替使用您的認證。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 請確定您的應用程式要求的最低權限。 只會要求您的應用程式絕對需要權限和時，才需要。 了解不同[類型的權限](v1-permissions-and-consent.md#types-of-permissions)。 只使用應用程式權限，如有必要，可能的話，請使用委派的權限。 如需 Microsoft Graph 權限的完整清單，請參閱此[權限參考](https://docs.microsoft.com/graph/permissions-reference)。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您要保護使用 Microsoft 身分識別平台 API，仔細考量它應該公開的權限。 請考慮適當的規模，您的解決方案是什麼，以及哪些權限需要系統管理員同意。 進行任何授權決策之前，先檢查連入權杖中的預期權限。 |

### <a name="implementation"></a>實作

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 使用新式驗證的解決方案 (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) 來安全地登入使用者。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 不實作您自己的通訊協定 – 使用[Microsoft 支援的驗證程式庫](reference-v2-libraries.md)(MSAL，伺服器中介軟體)。 請確定您使用您已與整合的驗證程式庫的最新版本。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 如果您的應用程式需要的資料會透過[Microsoft Graph](https://developer.microsoft.com/graph)，要求 Microsoft Graph 端點，而不是個別的 API 使用此資料的權限。 |

### <a name="end-user-experience"></a>使用者體驗

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | [了解同意體驗](application-consent-experience.md)並設定您的應用程式的同意提示的項目，讓使用者和系統管理員有足夠的資訊來判斷他們是否信任您的應用程式。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 最小化的使用者必須輸入登入認證時使用您的應用程式嘗試無訊息的次數之前互動流量的驗證 （無訊息的權杖取得）。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 請勿使用"prompt = 同意 「 針對每次登入。 只使用提示字元 = consent，如果您判斷您需要要求 （例如，如果您已經變更您的應用程式的必要權限） 的其他權限的同意。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 情況適用時，擴充您的應用程式與使用者資料。 使用[Microsoft Graph API](https://developer.microsoft.com/graph)是簡單的方法，若要這樣做。 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)可以幫助您入門的工具。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 註冊的完整應用程式需要讓系統管理員可以授與同意輕鬆地以其租用戶的權限集。 使用[增量同意](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)在執行階段，協助使用者了解為什麼您的應用程式正在要求權限，可能是關於或要求在第一次啟動時的使用者混淆。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 實作[清除單一登出體驗](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)。 隱私權和安全性需求，而且也很好的使用者經驗。 |

### <a name="testing"></a>測試

|   |   |
|---|---|
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 測試[條件式存取原則](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)，可能會影響您的使用者能夠使用您的應用程式。 |
| ![核取方塊](./media/active-directory-integration-checklist/checkbox-two.svg) | 測試您的應用程式與所有可能的帳戶，您打算支援 （例如工作或學校帳戶、 個人 Microsoft 帳戶、 子女帳戶和主權帳戶）。 |

## <a name="additional-resources"></a>其他資源

探索有關 v2.0 的深入資訊：

* [Microsoft 身分識別平台 （v2.0 概觀）](v2-overview.md)
* [Microsoft 身分識別平台的通訊協定參考](active-directory-v2-protocols.md)
* [存取權杖參考](access-tokens.md)
* [識別碼權杖參考](id-tokens.md)
* [驗證程式庫參考](reference-v2-libraries.md)
* [權限及同意在 Microsoft 身分識別平台](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)