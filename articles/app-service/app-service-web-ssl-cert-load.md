---
title: 在應用程式程式碼中使用用戶端 SSL 憑證 - Azure App Service | Microsoft Docs
description: 了解如何使用用戶端憑證來連線至需要這些憑證的遠端資源。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475118"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證

本操作說明指南示範如何在您的應用程式程式碼中使用公用或私用憑證。 使用案例的範例是應用程式要存取需要驗證憑證的外部服務。

若要使用憑證在您的程式碼中這個方法會使用 SSL 在應用程式服務中，這需要您的應用程式中的功能**基本**層或更高版本。 或者，您可以[納入您的應用程式存放庫中的憑證檔案](#load-certificate-from-file)，但它不是私用憑證的建議的作法。

當您讓 App Service 管理您的 SSL 憑證時，您可以分開維護憑證以及應用程式程式碼，並保護您的敏感性資料。

## <a name="upload-a-private-certificate"></a>上傳私人憑證

在之前上傳私人憑證，請確定[它符合所有需求](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)，不同之處在於它不需要設定伺服器驗證。

當您準備好上傳時時，請執行下列命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

複製憑證指紋，並查看[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="upload-a-public-certificate"></a>上傳公開憑證

中支援的公開憑證 *.cer*格式。 若要上傳公開憑證<a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，並瀏覽至您的應用程式。

按一下  **SSL 設定** > **公開憑證 (.cer)**  > **上傳公開憑證**從您的應用程式的左側導覽。

在 **名稱**，輸入憑證的名稱。 在  **CER 憑證檔案**，選取您的 CER 檔案。

按一下 [上傳]  。

![上傳公開憑證](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

一旦上傳憑證，複製憑證指紋，並查看[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="import-an-app-service-certificate"></a>匯入 App Service 憑證

請參閱[購買並設定 Azure App Service 的 SSL 憑證](web-sites-purchase-ssl-web-site.md)。

一旦匯入憑證，複製憑證指紋，並查看[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="make-the-certificate-accessible"></a>讓憑證可供存取

若要使用您的應用程式程式碼上傳或匯入的憑證，供其憑證指紋與`WEBSITE_LOAD_CERTIFICATES`應用程式設定，在中執行下列命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要存取您的憑證，請將值設定為`*`。

> [!NOTE]
> 這項設定會在指定的憑證[目前 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)大部分的定價層，但在網上**隔離**層 (也就是應用程式執行[App Service Environment](environment/intro.md))，它會放在憑證[本機 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)儲存。
>

![設定應用程式設定](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完成時，按一下 [儲存]  。

設定的憑證現在已準備好可供您的程式碼的。

## <a name="load-the-certificate-in-code"></a>載入程式碼中的憑證

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

如果您需要從您的應用程式的目錄載入憑證檔案，最好是使用上傳[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)，例如。 您應該保留機密的資料，例如出原始檔控制的私用憑證。

即使您直接在您的.NET 程式碼中載入檔案，媒體櫃仍會確認是否載入目前的使用者設定檔。 若要載入目前的使用者設定檔，設定`WEBSITE_LOAD_USER_PROFILE`使用下列命令中的應用程式設定<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

此設定會設定後，下列C#範例會載入憑證，稱為`mycert.pfx`從`certs`您的應用程式儲存機制的目錄。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
