---
title: Azure AD .NET 通訊協定概觀 | Microsoft Docs
description: 如何使用 HTTP 訊息來使用 Azure AD 授權存取租用戶中的 Web 應用程式和 Web API。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174152"
---
## <a name="register-your-application-with-your-ad-tenant"></a>向 AD 租用戶註冊應用程式
首先，您必須向您的 Azure Active Directory (Azure AD) 租用戶註冊應用程式。 這會讓應用程式獲得應用程式識別碼，以及讓它可以接收權杖。

* 登入 [Azure 入口網站](https://portal.azure.com)。
* 在頁面的右上角按一下您的帳戶，接著按一下 [切換目錄]  瀏覽，然後選取適當的租用戶，以選擇您的 Azure AD 租用戶。 
  * 如果您的帳戶底下只有一個 Azure AD 租用戶，或如果您已選取適當的 Azure AD 租用戶，請略過此步驟。
* 在左瀏覽窗格中，按一下 [Azure Active Directory]  。
* 按一下 **應用程式註冊**，然後按一下**新的註冊**。
* 遵照提示進行，並建立新的應用程式。 它並不重要如果它是 web 應用程式或本教學課程中，公用用戶端 （行動和桌面） 應用程式，但是如果您想要特定 web 應用程式或公用用戶端應用程式範例，請參閱我們[快速入門](../articles/active-directory/develop/v1-overview.md)。
  * [名稱]  為應用程式名稱，並能向使用者描述您的應用程式。
  * 在 [支援的帳戶類型]  底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  。
  * 提供**重新導向 URI**。 Web 應用程式，這是您的應用程式的基底 URL 的使用者可以登入。  例如： `http://localhost:12345`。 公用用戶端 （行動和桌面），Azure AD 會使用它來傳回權杖回應。 輸入應用程式特定的值。  例如： `http://MyFirstAADApp`。
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* 完成註冊後，Azure AD 會為指派您的應用程式唯一用戶端識別碼 (**應用程式識別碼**)。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。
* 若要在 Azure 入口網站中尋找您的應用程式，請按一下 [應用程式註冊]  ，然後按一下 [檢視所有應用程式]  。
