---
title: 在程式碼 Azure App Service 中使用 SSL 憑證 |Microsoft Docs
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
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271291"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>在您的程式碼中使用 SSL 憑證，Azure App Service

在您的應用程式程式碼中，您可以存取[新增至 App Service 的公用或私用憑證](configure-ssl-certificate.md)。 您的應用程式程式碼可能會做為用戶端，並存取需要憑證驗證的外部服務，或可能需要執行密碼編譯工作。 本操作指南會示範如何在您的應用程式代碼中使用公用或私用憑證。

在您的程式碼中使用憑證的這種方法會使用 App Service 中的 SSL 功能，這會要求您的應用程式必須在**基本**層或更高版本中。 如果您的應用程式處於**免費**或**共用**層，您可以[在應用程式儲存機制中包含憑證檔案](#load-certificate-from-file)。

當您讓 App Service 管理您的 SSL 憑證時，您可以分開維護憑證以及應用程式程式碼，並保護您的敏感性資料。

## <a name="prerequisites"></a>先決條件

若要遵循本操作說明指南：

- [建立 App Service 應用程式](/azure/app-service/)
- [將憑證新增至您的應用程式](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>尋找指紋

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務] >  **\<app-name>** 。

從應用程式的左側導覽中，選取 [ **TLS/SSL 設定**]，然後選取 [**私密金鑰憑證（.pfx）** ] 或 **[公開金鑰憑證（.cer）** ]。

尋找您要使用的憑證，並複製指紋。

![複製憑證指紋](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>讓憑證可供存取

若要存取應用程式程式碼中的憑證，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，以將其指紋新增至 `WEBSITE_LOAD_CERTIFICATES` 應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要讓所有憑證都可供存取，請將值設定為 `*`。

## <a name="load-certificate-in-windows-apps"></a>在 Windows 應用程式中載入憑證

`WEBSITE_LOAD_CERTIFICATES` 應用程式設定可讓您在 Windows 憑證存放區中的 Windows 託管應用程式存取指定的憑證，而此位置取決於[定價層](overview-hosting-plans.md)：

- **隔離**層-在[本機 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)中。 
- 所有其他層-在[目前的 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)中。

在C#程式碼中，您可以透過憑證指紋來存取憑證。 下列程式碼會載入指紋為 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的憑證。

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

在 JAVA 程式碼中，您可以使用 [主體一般名稱] 欄位存取 "Windows-MY" 存放區中的憑證（請參閱[公開金鑰憑證](https://en.wikipedia.org/wiki/Public_key_certificate)）。 下列程式碼顯示如何載入私密金鑰憑證：

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

針對不支援或未提供足夠的 Windows 憑證存放區支援的語言，請參閱[從檔案載入憑證](#load-certificate-from-file)。

## <a name="load-certificate-in-linux-apps"></a>在 Linux 應用程式中載入憑證

`WEBSITE_LOAD_CERTIFICATES` 應用程式設定會讓您的 Linux 裝載應用程式（包括自訂容器應用程式）可存取指定的憑證作為檔案。 這些檔案可在下列目錄中找到：

- 私人憑證-`/var/ssl/private` （`.p12` 檔案）
- 公開憑證-`/var/ssl/certs` （`.der` 檔案）

憑證檔案名為憑證指紋。 下列C#程式碼說明如何在 Linux 應用程式中載入公開憑證。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要瞭解如何從 node.js、PHP、Python、JAVA 或 Ruby 中的檔案載入 SSL 憑證，請參閱個別語言或 web 平臺的檔。

## <a name="load-certificate-from-file"></a>從檔案載入憑證

如果您需要載入您手動上傳的憑證檔案，最好是使用[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)上傳憑證，例如。 您應該將機密資料（例如私用憑證）保留在原始檔控制之外。

> [!NOTE]
> 即使您從檔案載入憑證，Windows 上的 ASP.NET 和 ASP.NET Core 也必須存取證書存儲。 若要在 Windows .NET 應用程式中載入憑證檔案，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中使用下列命令載入目前的使用者設定檔：
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 在您的程式碼中使用憑證的這種方法會使用 App Service 中的 SSL 功能，這會要求您的應用程式必須在**基本**層或更高版本中。

下列C#範例會從您應用程式中的相對路徑載入公開憑證：

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要瞭解如何從 node.js、PHP、Python、JAVA 或 Ruby 中的檔案載入 SSL 憑證，請參閱個別語言或 web 平臺的檔。

## <a name="more-resources"></a>其他資源

* [使用 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
