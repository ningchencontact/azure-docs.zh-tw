---
title: 了解 Azure Digital Twins API 驗證 | Microsoft Docs
description: 使用 Azure Digital Twins 連線至 API 並進行驗證
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016211"
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

當開發人員打造 Digital Twins 解決方案時，他們通常會建立中介層應用程式或 API。 應用程式或 API 接著會呼叫 Digital Twins API 下游。 使用者會先驗證中介層應用程式，然後使用代理者權杖流程來呼叫下游。 如需如何協調代理者流程的相關指示，請參閱[此頁面](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 您也可以在[此頁面](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) \(英文\) 上檢視程式碼範例。


## <a name="test-with-the-postman-client"></a>使用 Postman 用戶端測試

若要使用 Digital Twins API 啟動並執行，您可以使用 Postman 之類的用戶端作為 API 環境。 Postman 可協助您快速建立複雜的 HTTP 要求。 下列步驟示範如何取得 Azure AD 權杖，需要有此權杖才能在 Postman UI 內呼叫 Digital Twins。


1. 移至 https://www.getpostman.com/ 以下載應用程式。
1. 按照[此快速入門](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)的步驟來建立 Azure AD 應用程式。 或者，您可以重複使用現有的註冊。 
1. 在 [必要權限] 底下，輸入 “Azure Digital Twins”，然後選取 [委派的權限]。 接著選取 [授與權限]。
1. 開啟應用程式資訊清單，並將 **oauth2AllowImplicitFlow** 設定為 True。
1. 將回覆 URL 設定為 [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback)。
1. 選取 [授權] 索引標籤、選取 [OAuth 2.0]，然後選取 [取得新的存取權杖]。

    |**欄位**  |**值** |
    |---------|---------|
    | 授與類型 | 隱含 |
    | 回呼 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 驗證 URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | 用戶端識別碼 | 使用從步驟 2 中建立或重新決定用途之 Azure AD 應用程式的應用程式識別碼。 |
    | 影響範圍 | 保留空白。 |
    | State | 保留空白。 |
    | 用戶端驗證 | 當作基本驗證標頭傳送。 |

1. 選取 [要求權杖]。

    >[!NOTE]
    >如果您收到錯誤訊息「無法完成 OAuth 2」，請嘗試下列方法：
    > * 關閉 Postman 後重新開啟，然後再試一次。
   
1. 向下捲動，然後選取 [使用權杖]。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。
