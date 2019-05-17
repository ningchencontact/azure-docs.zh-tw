---
title: 具現化公用用戶端使用的應用程式的選項 (Microsoft Authentication Library for.NET) |Azure
description: 了解如何使用 Microsoft Authentication Library for.NET (MSAL.NET) 的設定選項，公開用戶端應用程式具現化。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 125bbf9aed54fb00f039aeffddd5cc1aad3360a6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544391"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>使用的組態選項使用 MSAL.NET，公用用戶端應用程式具現化

本文說明如何具現化[公開用戶端應用程式](msal-client-applications.md)使用 Microsoft Authentication Library for.NET (MSAL.NET)。  應用程式具現化的設定檔中定義的組態選項。

然後再初始化應用程式，您必須先[註冊](quickstart-register-app.md)它，讓您的應用程式可以與 Microsoft 身分識別平台整合。 註冊之後，您可能需要下列資訊 （這可以在 Azure 入口網站中找到）：

- 用戶端識別碼 （代表 GUID 的字串）
- （具名執行個體） 的身分識別提供者 URL 和您的應用程式的登入對象。 這兩個參數統稱為授權單位。
- 如果您正在撰寫企業營運應用程式僅供您的組織 （也命名單一租用戶的應用程式） 租用戶識別碼。
- 針對 web 應用程式，以及有時公用用戶端應用程式 （尤其當您的應用程式需要使用訊息代理程式），您必須也會將 redirectUri 身分識別提供者，請連絡後利用安全性權杖的應用程式。


.NET Core 主控台應用程式可能會有下列*appsettings.json*組態檔：

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

下列程式碼會讀取這個檔案，使用.NET 組態架構：

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

下列程式碼會建立您的應用程式中，然後再從設定檔使用的組態：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

