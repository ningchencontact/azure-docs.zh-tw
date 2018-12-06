---
title: 了解 Azure Digital Twins API 驗證 | Microsoft Docs
description: 使用 Azure Digital Twins 連線至 API 並進行驗證
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711050"
---
# <a name="connect-and-authenticate-to-apis"></a>連線至 API 並進行驗證

Azure Digital Twins 會使用 Azure Active Directory (Azure AD) 來驗證使用者並保護應用程式。 Azure AD 支援各種現代架構的驗證。 全部都以業界標準通訊協定 OAuth 2.0 或 OpenID Connect 為基礎。 此外，開發人員可以使用 Azure AD來建置單一租用戶和企業營運 (LOB) 應用程式。 開發人員也可以使用 Azure AD 來開發多租用戶應用程式。

如需 Azure AD 的概觀，請造訪[基本概念頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/index)，以取得逐步指南、概念及快速入門。

若要整合應用程式或服務與 Azure AD，開發人員必須先向 Azure AD 註冊應用程式。 如需詳細的指示和螢幕擷取畫面，請參閱[此快速入門](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

Azure AD 支援[五個主要應用程式案例](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)：

* 單頁應用程式 (SPA)：使用者必須登入 Azure AD 所保護的單頁應用程式。
* 網頁瀏覽器到 Web 應用程式：使用者必須登入 Azure AD 所保護的 Web 應用程式。
* 原生應用程式到 Web API：在手機、平板電腦或電腦上執行的原生應用程式必須驗證使用者，才能從 Azure AD 所保護的 Web API 取得資源。
* Web 應用程式到 Web API：Web 應用程式必須從 Azure AD 所保護的 Web API 取得資源。
* 精靈或伺服器應用程式到 Web API：無 Web UI 的精靈應用程式或伺服器應用程式必須從 Azure AD 所保護的 Web API 取得資源。

Windows Azure 驗證程式庫提供許多方式來取得 Active Directory 權杖。 如需有關程式庫的詳細資料及程式碼範例，請參閱[這篇文章](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki) \(英文\)。

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>從中介層 Web API 呼叫 Digital Twins

當開發人員打造 Digital Twins 解決方案時，他們通常會建立中介層應用程式或 API。 應用程式或 API 接著會呼叫 Digital Twins API 下游。 若要支援此標準 Web 解決方案架構，請確定該使用者先：

1. 向中介層應用程式驗證

1. 在驗證期間會取得 OAuth 2.0 代理者權杖

1. 所取得的權杖接著用於進行驗證，或使用代理者流程呼叫更下游的 API

如需如何協調代理者流程的相關指示，請參閱 [OAuth 2.0 代理者流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 您也可以在[呼叫下游 Web API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) 中檢視程式碼範例。

## <a name="next-steps"></a>後續步驟

若要使用 OAuth 2.0 隱含授權流程設定及測試 Azure Digital Twins，請閱讀[設定 Postman](./how-to-configure-postman.md)。

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。