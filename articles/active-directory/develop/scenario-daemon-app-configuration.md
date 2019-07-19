---
title: 呼叫 web Api 的 Daemon 應用程式 (應用程式設定)-Microsoft 身分識別平臺
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式 (應用程式設定)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277832"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的 Daemon 應用程式-程式碼設定

瞭解如何為可呼叫 web Api 的背景工作應用程式設定程式碼。

## <a name="msal-libraries-supporting-daemon-apps"></a>支援 daemon 應用程式的 MSAL 程式庫

支援 daemon 應用程式的 Microsoft 程式庫包括:

  MSAL 程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 建立 daemon 應用程式的支援平臺是 .NET Framework 和 .NET Core 平臺 (而不是 UWP、Xamarin 和 Xamarin, 因為這些平臺是用來建立公用用戶端應用程式)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進行中的開發-現已公開預覽
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進行中的開發-現已公開預覽

## <a name="configuration-of-the-authority"></a>設定授權單位

假設 daemon 應用程式不使用委派的許可權, 但應用程式許可權, 其*支援的帳戶類型*不能是*任何組織目錄中的帳戶, 也不能是個人 Microsoft 帳戶 (例如 Skype、Xbox、Outlook.com)* 。 事實上, 沒有租使用者系統管理員將同意授與 Microsoft 個人帳戶的 daemon 應用程式。 您必須選擇 [*我的組織中的帳戶*] 或*任何組織中的帳戶*。

因此, 在應用程式設定中指定的授權單位應該是租使用者 (指定租使用者識別碼或與您組織相關聯的功能變數名稱)。

如果您是 ISV, 而且想要提供多租使用者工具, 您可以使用`organizations`。 但請記住, 您也必須向客戶說明如何授與系統管理員同意。 如需詳細資訊, 請參閱[要求整個租](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)使用者的同意。 此外, 只有當用戶端認證是應用`organizations`程式密碼 (而非憑證) 時, 才會允許 MSAL 的限制。 請參閱[MSAL.NET bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>應用程式設定和具現化

在 MSAL 程式庫中, 用戶端認證 (密碼或憑證) 會當做機密用戶端應用程式結構的參數來傳遞。

> [!IMPORTANT]
> 即使您的應用程式是以服務方式執行的主控台應用程式, 如果它是背景程式應用程式, 則必須是機密用戶端應用程式。

### <a name="msalnet"></a>MSAL.NET

將[IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 套件新增至您的應用程式。

使用 MSAL.NET 命名空間

```CSharp
using Microsoft.Identity.Client;
```

Daemon 應用程式將由`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

以下是使用應用程式密碼來建立應用程式的程式碼:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

以下是用來建立具有憑證之應用程式的程式碼:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

最後, 機密用戶端應用程式也可以使用用戶端判斷提示來證明其身分識別, 而不是用戶端密碼或憑證。 [用戶端判斷](msal-net-client-assertions.md)提示中會詳細說明這個 advanced 案例


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
> [Daemon 應用程式-取得應用程式的權杖](./scenario-daemon-acquire-token.md)
