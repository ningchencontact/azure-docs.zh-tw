---
title: 瞭解使用 Azure 數位 Twins 的 API 驗證 |Microsoft Docs
description: 瞭解如何使用 Azure 數位 Twins 連接至 Api 並進行驗證。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: lyhughes
ms.openlocfilehash: ad51fbe7d2f8e8f115adf03d6333c0747765ee43
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338596"
---
# <a name="connect-to-and-authenticate-with-apis"></a>連接至 Api 並進行驗證

Azure Digital Twins 會使用 Azure Active Directory (Azure AD) 來驗證使用者並保護應用程式。 Azure AD 支援各種現代架構的驗證。 全部都以業界標準通訊協定 OAuth 2.0 或 OpenID Connect 為基礎。 此外，開發人員可以使用 Azure AD來建置單一租用戶和企業營運 (LOB) 應用程式。 開發人員也可以使用 Azure AD 來開發多租用戶應用程式。

如需 Azure AD 的概觀，請造訪[基本概念頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/)，以取得逐步指南、概念及快速入門。

> [!TIP]
> 依照[教學](tutorial-facilities-setup.md)課程來設定和執行 Azure 數位 Twins 範例應用程式。

若要整合應用程式或服務與 Azure AD，開發人員必須先向 Azure AD 註冊應用程式。 如需詳細的指示和螢幕擷取畫面，請參閱[此快速入門](../active-directory/develop/quickstart-register-app.md)。

Azure AD 支援[五個主要應用程式案例](../active-directory/develop/v2-app-types.md)：

* 單一頁面應用程式 (SPA):使用者必須登入 Azure AD 所保護的單一頁面應用程式。
* Web 瀏覽器到 web 應用程式:使用者必須登入 Azure AD 所保護的 web 應用程式。
* 要 Web API 的原生應用程式:在手機、平板電腦或 PC 上執行的原生應用程式需要驗證使用者, 才能從 Azure AD 所保護的 Web API 取得資源。
* 要 Web API 的 Web 應用程式:Web 應用程式必須從 Azure AD 所保護的 Web API 取得資源。
* 要 Web API 的 Daemon 或伺服器應用程式:沒有 web UI 的背景程式應用程式或伺服器應用程式, 必須從 Azure AD 保護的 Web API 取得資源。

Windows Azure 驗證程式庫提供許多方式來取得 Active Directory 權杖。 如需程式庫和程式碼範例的詳細資訊，請參閱[ADAL.NET Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)。

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>從中介層 Web API 呼叫 Digital Twins

當開發人員打造 Digital Twins 解決方案時，他們通常會建立中介層應用程式或 API。 應用程式或 API 接著會呼叫 Digital Twins API 下游。 若要支援此標準 Web 解決方案架構，請確定該使用者先：

1. 向中介層應用程式驗證

1. 在驗證期間會取得 OAuth 2.0 代理者權杖

1. 所取得的權杖接著用於進行驗證，或使用代理者流程呼叫更下游的 API

如需如何協調代理者流程的相關指示，請參閱 [OAuth 2.0 代理者流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 您也可以在[呼叫下游 Web API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) 中檢視程式碼範例。

## <a name="next-steps"></a>後續步驟

若要使用 OAuth 2.0 隱含授權流程設定及測試 Azure Digital Twins，請閱讀[設定 Postman](./how-to-configure-postman.md)。

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。