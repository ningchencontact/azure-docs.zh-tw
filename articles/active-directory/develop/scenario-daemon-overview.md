---
title: 精靈應用程式呼叫 web Api （概觀）-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075876"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>案例：呼叫 web Api 的精靈應用程式

了解所有您要建置精靈應用程式呼叫 web Api。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>總覽

您的應用程式可以取得權杖來呼叫 web API 代表本身 （不代表使用者）。 這個案例是適用於精靈應用程式。 它使用標準的 OAuth 2.0[用戶端認證](v2-oauth2-client-creds-grant-flow.md)授與。

![精靈應用程式](./media/scenario-daemon-app/daemon-app.svg)

以下是一些範例的精靈應用程式的使用案例：

- 用來佈建或管理的使用者，或執行批次的目錄中的程序的 web 應用程式
- 執行批次作業或在背景中執行的作業系統服務 （在 Windows、 windows 服務） 或 Linux 上的精靈處理程序等桌面應用程式
- Web Api 需要處理目錄，不是特定的使用者

還有另一個常見的案例，其中非精靈應用程式會使用用戶端認證： 即使它們代表使用者執行動作，需要存取 web API 或其身分識別的資源，若是技術原因。 舉例來說，快取存取金鑰保存庫或 Azure SQL database 中的祕密。

取得權杖，以自己的身分識別的應用程式：

- 是機密用戶端應用程式。 這些應用程式，假設他們存取資源獨立使用者，需要證明其身分識別。 它們也而不是機密的應用程式，他們要由 Azure Active Directory (Azure AD) 租用戶系統管理員核准。
- 已向 Azure AD 祕密 （應用程式密碼或憑證）。 此密碼是傳入的呼叫 Azure AD 取得權杖期間。

## <a name="specifics"></a>詳細資料

> [!IMPORTANT]
>
> - 使用者互動並不可行的精靈應用程式。 精靈應用程式需要它自己的身分識別。 這種類型的應用程式要求存取權杖，藉由使用其應用程式識別，並呈現其應用程式識別碼、 認證 （密碼或憑證），與應用程式識別碼 URI，以 Azure AD。 驗證成功後，精靈會從 Microsoft 身分識別平台端點，然後用來呼叫 web API （以及視需要重新整理） 接收存取權杖 （及重新整理權杖）。
> - 使用者互動不可行，因為不可能使用增量同意。 所有必要的 API 權限必須設定在應用程式註冊和應用程式的程式碼只會要求以靜態方式定義的權限。 這也表示精靈應用程式將不支援增量同意。

適用於開發人員，此案例的端對端體驗有下列層面：

- 精靈應用程式僅適用於 Azure AD 租用戶中。 它徒勞無功的舉動建置精靈應用程式，嘗試處理 Microsoft 個人帳戶。 如果您是特定業務 (LOB) 應用程式開發人員，您將建立租用戶中的精靈應用程式。 如果您是 ISV，您可能想要建立的多租用戶的精靈應用程式。 需要在每個租用戶管理員同意
- 期間[應用程式註冊](./scenario-daemon-app-registration.md)，則**回覆 URI** ，則不需要。 您需要與 Azure AD 中，共用密碼或憑證，您需要要求應用程式權限，並授與系統管理員同意，若要使用這些應用程式權限。
- [應用程式組態](./scenario-daemon-app-configuration.md)必須與 Azure AD 應用程式註冊期間提供與共用的用戶端認證。
- [範圍](scenario-daemon-acquire-token.md#scopes-to-request)用來取得權杖，以使用用戶端認證流程必須是靜態的範圍。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [精靈應用程式-應用程式註冊](./scenario-daemon-app-registration.md)
