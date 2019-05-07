---
title: 購買及設定 Azure 的 SSL 憑證 - App Service | Microsoft Docs
description: 了解如何購買 App Service 憑證並將它繫結至您的 App Service 應用程式
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: b561091eedb43e1c77f3c97951beeb92bfcf4751
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65202933"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>購買及設定 Azure App Service 的 SSL 憑證

本教學課程說明如何透過在 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 中建立 (購買) App Service 憑證，然後將它繫結至 App Service 應用程式，來保護您的 [App Service 應用程式](https://docs.microsoft.com/azure/app-service/)或[函數應用程式](https://docs.microsoft.com/azure/azure-functions/)。

> [!TIP]
> App Service 憑證可以用於任何 Azure 或非 Azure 服務，並不限於應用程式服務。 若要如此，您需要建立一個可在任何地方使用的 App Service 憑證本機 PFX 複本。 如需詳細資訊，請參閱[建立 App Service 憑證的本機 PFX 複本](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/)。
>

## <a name="prerequisites"></a>必要條件

若要遵循本操作說明指南：

- [建立 App Service 應用程式](/azure/app-service/)
- [將網域名稱對應至您的應用程式](app-service-web-tutorial-custom-domain.md)或[在 Azure 中購買並設定它](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>開始訂購憑證

在 <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service 憑證建立頁面</a>中開始訂購 App Service 憑證。

![建立憑證](./media/app-service-web-purchase-ssl-web-site/createssl.png)

使用下表來協助您設定憑證。 完成後，按一下 [建立]。

| 設定 | 描述 |
|-|-|
| 名稱 | App Service 憑證的易記名稱。 |
| 裸網域主機名稱 | 如果您在這裡指定根網域，就會取得憑證以「同時」保護根網域和 `www` 子網域。 若只要保護所有子網域，請在這裡指定子網域的完整網域名稱 (例如 `mysubdomain.contoso.com`)。 |
| 訂用帳戶 | 裝載 Web 應用程式的資料中心。 |
| 資源群組 | 包含憑證的資源群組。 您可以使用新的資源群組，或為您的 App Service 應用程式選取相同的資源群組。 |
| 憑證 SKU | 決定要建立的憑證類型：標準憑證或[萬用字元憑證](https://wikipedia.org/wiki/Wildcard_certificate)。 |
| 法律條款 | 按一下以確認您同意法律條款。 從 GoDaddy 取得憑證。 |

## <a name="store-in-azure-key-vault"></a>儲存在 Azure Key Vault 中

憑證購買程序完成後，您必須先完成一些其他的步驟，才能開始使用此憑證。 

選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後按一下 [憑證設定] > [步驟 1：存放區]。

![插入準備在 KV 中儲存的影像](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 是一項 Azure 服務，可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和祕密。 這是 App Service 憑證的儲存體選擇。

在 [Key Vault 狀態] 頁面中，按一下 [Key Vault 存放庫] 來建立新的保存庫或選擇現有的保存庫。 如果您選擇建立新的保存庫，請使用下表來協助您設定保存庫並按一下 [建立]。 了解如何在相同訂用帳戶和資源群組中建立新的 Key Vault。

| 設定 | 描述 |
|-|-|
| 名稱 | 包含英數字元和虛線的唯一名稱。 |
| 資源群組 | 建議選取相同的資源群組作為您的 App Service 憑證。 |
| 位置 | 選取與 App Service 應用程式相同的位置。 |
| 定價層 | 如需詳細資訊，請參閱 [Azure Key Vault 定價詳細資料](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 存取原則| 定義應用程式以及允許的保存庫資源存取權。 您可以稍後設定它，並遵循[將金鑰保存庫的存取權授與數個應用程式](../key-vault/key-vault-group-permissions-for-apps.md)中的步驟。 |
| 虛擬網路存取 | 限制某些 Azure 虛擬網路的保存庫存取權。 您可以稍後設定它，並遵循[設定 Azure Key Vault 防火牆和虛擬網路](../key-vault/key-vault-network-security.md)中的步驟。 |

選取保存庫之後，關閉 [Key Vault 存放庫] 頁面。 [存放區] 選項應顯示綠色核取記號，表示成功。 讓頁面保持開啟，以供下一個步驟使用。

## <a name="verify-domain-ownership"></a>確認網域擁有權

從您在上一個步驟中使用的相同 [憑證設定] 頁面，按一下 [步驟 2：驗證]。

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

選取 [App Service 驗證]。 您已經將網域對應至您的 Web 應用程式 (請參閱[必要條件](#prerequisites))，因此它已經過驗證。 只要按一下 [確認] 即可完成此步驟。 按一下 [重新整理] 按鈕，直到「憑證已經過網域驗證」訊息出現為止。

> [!NOTE]
> 支援的網域驗證方法有四種： 
> 
> - **App Service** - 當網域已經對應至相同訂用帳戶中的 App Service 應用程式時最方便的選項。 它會利用 App Service 應用程式已驗證網域擁有權的這個事實。
> - **網域** - 驗證[您購自 Azure 的 App Service 網域](manage-custom-dns-buy-domain.md)。 Azure 會自動為您新增驗證 TXT 記錄並完成程序。
> - **郵件** - 將電子郵件傳送給網域管理員來驗證網域。 當您選取此選項時，系統會提供指示。
> - **手動** - 使用 HTML 網頁 (僅限**標準**憑證) 或 DNS TXT 記錄驗證網域。 當您選取此選項時，系統會提供指示。

## <a name="bind-certificate-to-app"></a>將憑證繫結至應用程式

從 **[Azure 入口網站](https://portal.azure.com/)** 的左側功能表中，選取 [應用程式服務] > **\<your_ app>**。

從您應用程式的左側導覽中，選取 [SSL 設定] > [私人憑證 (.pfx)] > [匯入 App Service 憑證]。

![插入匯入憑證的影像](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

選取您剛才購買的憑證。

現在憑證已匯入，您需要將它繫結至您應用程式中對應的網域名稱。 選取 [繫結] > [新增 SSL 繫結]。 

![插入匯入憑證的影像](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

使用下表來協助您在 [SSL 繫結] 對話方塊中設定繫結，然後按一下 [新增繫結]。

| 設定 | 描述 |
|-|-|
| 主機名稱 | 要新增 SSL 繫結的網域名稱。 |
| 私人憑證指紋 | 要繫結的憑證。 |
| SSL 類型 | <ul><li>**SNI SSL** - 可能會新增多個以 SNI 為基礎的 SSL 繫結。 此選項可允許多個 SSL 憑證保護同一個 IP 位址上的多個網域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（可以在[服务器名称指示](https://wikipedia.org/wiki/Server_Name_Indication)中了解更全面的浏览器支持信息）。</li><li>**以 IP 為基礎的 SSL**：可能只會新增一個以 IP 為基礎的 SSL 繫結。 此選項只允許一個 SSL 憑證保護專用的公用 IP 位址。 設定繫結之後，請依照[為 IP SSL 重新對應 A 記錄](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl)中的步驟執行。 </li></ul> |

## <a name="verify-https-access"></a>驗證 HTTPS 存取

使用 `HTTPS://<domain_name>` 而非 `HTTP://<domain_name>` 來造訪您的應用程式，確認已正確設定憑證。

## <a name="rekey-certificate"></a>重設憑證的金鑰

如果您認為您的憑證的私用金鑰受到危害，您可以重設您的憑證。 選取中的憑證[App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面，然後選取**重設金鑰和同步處理**從左側導覽。

按一下 **重設**啟動程序。 此程序需要 1 - 10 分鐘才能完成。

![插入重設 SSL 金鑰的影像](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

重設憑證的金鑰，會以憑證授權單位發行的新憑證變更憑證。

重設金鑰作業完成後，按一下**同步**。同步處理作業會自動更新 App Service 中的憑證的主機名稱繫結，而不會造成任何停機時間，以您的應用程式。

> [!NOTE]
> 如果您不按一下**同步**，App Service 會自動同步您的憑證在 48 小時內。

## <a name="renew-certificate"></a>更新憑證

若要隨時開啟憑證的自動更新，請選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後按一下左側導覽中的 [自動更新設定]。

選取 [開啟]，然後按一下 [儲存]。 如果您已經開啟自動更新，憑證可以在過期前的 60 天開始自動更新。

![自動更新憑證](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

若要改為手動更新憑證，請按一下 [手動更新]。 您可以在過期前的 60 天要求手動更新憑證。

更新作業完成後，按一下**同步**。同步處理作業會自動更新 App Service 中的憑證的主機名稱繫結，而不會造成任何停機時間，以您的應用程式。

> [!NOTE]
> 如果您不按一下**同步**，App Service 會自動同步您的憑證在 48 小時內。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>其他資源

* [強制使用 HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [強制使用 TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [在 Azure App Service 中的應用程式程式碼中使用 SSL 憑證](app-service-web-ssl-cert-load.md)
* [常見問題集：App Service 憑證](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
