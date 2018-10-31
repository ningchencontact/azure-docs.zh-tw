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
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638013"
---
# <a name="connect-and-authenticate-to-apis"></a>連線至 API 並進行驗證

Azure Digital Twins 會使用 Azure Active Directory (Azure AD) 來驗證使用者並保護應用程式。 Azure AD 支援各種新型架構的驗證，它們全都以產業標準通訊協定 OAuth 2.0 或 OpenID Connect 為基準。 此外，Azure AD 還可讓開發人員建置單一租用戶的企業營運 (LOB) 應用程式及開發多租用戶應用程式。

如需 Azure AD 的概觀，請造訪逐步指南的[基礎頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/index)、概念以及快速入門。

若要整合應用程式或服務與 Azure AD，開發人員必須先向 Azure AD 註冊應用程式。 如需詳細的指示和螢幕擷取畫面，請在[這裡](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)檢視指示

以下是 Azure AD 支援的[五個主要應用程式案例](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)：

* 單頁應用程式 (SPA)：使用者必須登入 Azure AD 所保護的單頁應用程式。
* 網頁瀏覽器到 Web 應用程式：使用者必須登入 Azure AD 所保護的 Web 應用程式。
* 原生應用程式到 Web API：在手機、平板電腦或電腦上執行的原生應用程式必須驗證使用者，才能從 Azure AD 所保護的 Web API 取得資源。
* Web 應用程式到 Web API：Web 應用程式必須從 Azure AD 所保護的 Web API 取得資源。
* 精靈或伺服器應用程式到 Web API：無 Web 使用者介面的精靈應用程式或伺服器應用程式必須從 Azure AD 所保護的 Web API 取得資源。

Windows Azure 驗證程式庫提供許多方式來取得 Active Directory 權杖。 若要深入了解程式庫以及程式碼範例，請查看[這裡](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)。

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>從中介層 Web API 呼叫 Digital Twins

建構 Digital Twins 解決方案時。開發人員通常會選擇建立中介層應用程式或 API，然後再呼叫下游的 Digital Twins API。 使用者會先驗證中介層應用程式，然後在呼叫下游時使用代表權杖流程。 如需有關如何協調代理流程的詳細指示，請瀏覽[本頁](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 您也可以在[這裡](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)檢視程式碼範例。


## <a name="test-with-the-postman-client"></a>使用 Postman 用戶端測試

若要使用 Digital Twins API 啟動並執行，您可以使用 Postman 之類的用戶端作為 API 環境。 Postman 可協助您快速建立複雜的 HTTP 要求。 下列指示將著重在如何在 Postman UI 中，取得呼叫 Digital Twins 所需的 Azure AD 權杖。


1. 瀏覽至 https://www.getpostman.com/ 以下載應用程式
1. 請依照[這裡](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)的步驟，建立 Azure Active Directory 應用程式 (或者您可以選擇重複使用現有的註冊)。 
1. 在 [必要權限] 底下，新增 “Azure Digital Twins”，然後選取 [委派的權限]。 請記得按一下 [授與權限] 來完成。
1. 開啟應用程式資訊清單，並將 oauth2AllowImplicitFlow 設為 true
1. 設定 [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) 的回覆 URL。
1. 選取 [授權] 索引標籤，按一下 [OAuth 2.0]，然後選取 [取得新的存取權杖]。

    |**欄位**  |**值** |
    |---------|---------|
    | 授與類型 | 隱含 |
    | 回呼 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 驗證 URL | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | 用戶端識別碼 | 使用從步驟 1 中建立或重新決定用途之 Azure AD 應用程式的應用程式識別碼 |
    | 影響範圍 | 保留空白 |
    | State | 保留空白 |
    | 用戶端驗證 | 當作基本驗證標頭傳送 |

1. 按一下 [要求權杖]。

    >[!NOTE]
    >如果您收到錯誤訊息「無法完成 OAuth 2」，請嘗試下列方法：
    > * 關閉 Postman 後重新開啟，然後再試一次。
   
1. 向下捲動，然後按一下 [使用權杖]。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。
