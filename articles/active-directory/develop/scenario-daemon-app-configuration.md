---
title: 精靈應用程式呼叫 web Api （應用程式設定）-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api （應用程式組態）
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
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055761"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>精靈應用程式呼叫 web Api-程式碼組態

了解如何設定您的精靈應用程式呼叫 web Api 的程式碼。

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL 程式庫支援精靈應用程式

支援精靈應用程式的 Microsoft 程式庫是：

  MSAL 程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 若要建置精靈應用程式支援的平台是.NET Framework 和.NET Core 的平台 （不 UWP、 Xamarin.iOS 和為這些平台 Xamarin.Android 來建置公用用戶端應用程式）
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進度-公開預覽中的程式開發
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進度-公開預覽中的程式開發

## <a name="configuration-of-the-authority"></a>授權單位設定

假設精靈應用程式不使用委派的權限，但應用程式的權限，其*支援的帳戶類型*不能是*任何組織的目錄中的帳戶及個人 Microsoft 帳戶 （比方說，Skype、 Xbox、 Outlook.com)* 。 事實上，沒有任何租用戶系統管理員將 Microsoft 個人帳戶的精靈應用程式的同意授與。 您必須選擇*我的組織中的帳戶*或是*任何組織中的帳戶*。

因此在應用程式組態中指定的授權單位應該是租用戶 ed （指定租用戶識別碼或與您組織相關聯的網域名稱）。

如果您是 ISV，若要提供多租用戶的工具，您可以使用`organizations`。 但請記住，您也需要為您的客戶，說明如何授與系統管理員同意。 請參閱[要求對整個租用戶的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)如需詳細資訊。 也沒有目前限制在 MSAL，`organizations`用戶端認證是應用程式祕密 （而非憑證） 時，才允許。 請參閱[MSAL.NET bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>應用程式組態和具現化

在 MSAL 程式庫，用戶端認證 （密碼或憑證） 會傳遞做為參數機密用戶端應用程式建構。

> [!IMPORTANT]
> 即使您的應用程式是主控台應用程式的精靈應用程式後，才執行為服務時，它必須是機密用戶端應用程式。

### <a name="msalnet"></a>MSAL.NET

新增[Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 封裝加入您的應用程式。

使用 MSAL.NET 命名空間

```CSharp
using Microsoft.Identity.Client;
```

精靈應用程式將會看到由 `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

若要建置應用程式祕密的應用程式程式碼如下：

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

以下是可建置具有憑證的應用程式程式碼：

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [精靈應用程式-取得應用程式的權杖](./scenario-daemon-acquire-token.md)