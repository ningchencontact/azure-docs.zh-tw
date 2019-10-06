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
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981123"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證

本操作指南會示範如何在您的應用程式代碼中使用公用或私用憑證。 使用案例的範例是應用程式要存取需要驗證憑證的外部服務。

在您的程式碼中使用憑證的這種方法會使用 App Service 中的 SSL 功能，這會要求您的應用程式必須在**基本**層或更高版本中。 或者，您可以將[憑證檔案包含在應用程式存放庫中](#load-certificate-from-file)，但不是私用憑證的建議做法。

當您讓 App Service 管理您的 SSL 憑證時，您可以分開維護憑證以及應用程式程式碼，並保護您的敏感性資料。

## <a name="upload-a-private-certificate"></a>上傳私人憑證

上傳私人憑證之前，請確定[它符合所有需求](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)，但不需要針對伺服器驗證進行設定。

當您準備好上傳時，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令。

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

複製憑證指紋，並參閱[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="upload-a-public-certificate"></a>上傳公開憑證

*.Cer*格式支援公用憑證。 若要上傳公開憑證，請<a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，然後流覽至您的應用程式。

在應用程式的左側導覽中，按一下  **SSL 設定**  > **公開憑證（.cer）**  >  **上傳公開憑證**。

在 [**名稱**] 中，輸入憑證的名稱。 在 [ **.Cer 憑證**檔案] 中，選取您的 CER 檔案。

按一下 [上傳]。

![上傳公開憑證](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

一旦憑證上傳，請複製憑證指紋，並參閱[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="import-an-app-service-certificate"></a>匯入 App Service 憑證

請參閱[購買和設定 Azure App Service 的 SSL 憑證](web-sites-purchase-ssl-web-site.md)。

一旦匯入憑證，請複製憑證指紋，並參閱[讓憑證可供存取](#make-the-certificate-accessible)。

## <a name="make-the-certificate-accessible"></a>讓憑證可供存取

若要在您的應用程式程式碼中使用上傳或匯入的憑證，請在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，讓其指紋可透過 `WEBSITE_LOAD_CERTIFICATES` 應用程式設定來存取：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要讓所有憑證都可供存取，請將值設定為 `*`。

> [!NOTE]
> 此設定會將指定的憑證放在[目前的 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存放區中，以用於大部分的定價層，但如果您的應用程式是在**隔離**層上執行（也就是應用程式在[App Service 環境](environment/intro.md)中執行），您可能需要簽入[本機](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)請改為 Machine\My 存放區。
>

![設定應用程式設定](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完成時，按一下 [儲存]。

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

設定此設定之後，下列C#範例會從應用程式存放庫的 `certs` 目錄載入名為 `mycert.pfx` 的憑證。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
