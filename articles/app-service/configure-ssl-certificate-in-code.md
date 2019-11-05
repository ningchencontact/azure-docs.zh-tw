---
title: 在應用程式代碼中使用 SSL 憑證-Azure App Service |Microsoft Docs
description: 了解如何使用用戶端憑證來連線至需要這些憑證的遠端資源。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513688"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證

您的 App Service 應用程式程式碼可能會作為用戶端，並存取需要憑證驗證的外部服務。 本操作指南會示範如何在您的應用程式代碼中使用公用或私用憑證。

在您的程式碼中使用憑證的這種方法會使用 App Service 中的 SSL 功能，這會要求您的應用程式必須在**基本**層或更高版本中。 或者，您可以將[憑證檔案包含在應用程式存放庫中](#load-certificate-from-file)，但不是私用憑證的建議做法。

當您讓 App Service 管理您的 SSL 憑證時，您可以分開維護憑證以及應用程式程式碼，並保護您的敏感性資料。

## <a name="prerequisites"></a>必要條件

若要遵循本操作說明指南：

- [建立 App Service 應用程式](/azure/app-service/)
- [將憑證新增至您的應用程式](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>尋找指紋

在  <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中，從左側功能表中選取 **應用程式服務** >  **\<應用程式名稱 >** 。

從應用程式的左側導覽中，選取 [ **TLS/SSL 設定**]，然後選取 [**私密金鑰憑證（.pfx）** ] 或 **[公開金鑰憑證（.cer）** ]。

尋找您要使用的憑證，並複製指紋。

![複製憑證指紋](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>載入憑證

若要在您的應用程式程式碼中使用憑證，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，以將其指紋新增至 `WEBSITE_LOAD_CERTIFICATES` 應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要讓所有憑證都可供存取，請將值設定為 `*`。

> [!NOTE]
> 這項設定會將指定的憑證放在[目前的 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存放區中，以用於大部分的定價層，但在**隔離**層（也就是在[App Service 環境](environment/intro.md)中執行應用程式）中，它會將憑證放在[本機 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存放區.
>

已設定的憑證現在已可供您的程式碼使用。

## <a name="load-the-certificate-in-code"></a>在程式碼中載入憑證

一旦憑證可供存取之後，您可以透過憑證指紋在 C# 程式碼中存取憑證。 下列程式碼會載入指紋為 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的憑證。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>從檔案載入憑證

例如，如果您需要從應用程式目錄載入憑證檔案，最好是使用[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)上傳它。 您應該將機密資料（例如私用憑證）保留在原始檔控制之外。

即使您是直接在 .NET 程式碼中載入檔案，程式庫仍會驗證是否已載入目前的使用者設定檔。 若要載入目前的使用者設定檔，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中使用下列命令來設定 `WEBSITE_LOAD_USER_PROFILE` 應用程式設定。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

設定此設定之後，下列C#範例會從應用程式儲存機制的 `certs` 目錄載入名為 `mycert.pfx` 的憑證。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>其他資源

* [使用 SSL 系結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
