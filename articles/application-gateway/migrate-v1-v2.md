---
title: 移轉 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 為 v2
description: 這篇文章會示範如何從 v1 移轉 Azure 應用程式閘道 Web 應用程式防火牆，為 v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 0fd605d7d502970dccd37da1f3f70fdadb1094a1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550456"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>移轉 Azure 應用程式閘道和 Web 應用程式防火牆從 v1 為 v2

[Azure 應用程式閘道和 Web 應用程式防火牆 (WAF) v2](application-gateway-autoscaling-zone-redundant.md)現已推出，提供其他功能，例如自動調整和可用性區域備援。 不過，現有的 v1 閘道不自動升級為 v2。 如果您想要從 v1 移轉為 v2，請遵循這篇文章中的步驟。

在移轉時有兩個階段：

1. 移轉設定
2. 移轉用戶端流量

本文章涵蓋移轉設定。 用戶端流量移轉您的環境而有所不同。 不過，一些高階、 一般建議[提供](#migrate-client-traffic)。

## <a name="migration-overview"></a>移轉概觀

Azure PowerShell 指令碼，會進行下列作業：

* 在您指定的虛擬網路子網路中建立新的 Standard_v2 或 WAF_v2 閘道。
* 順暢地將複製到新建立的 Standard_V2 或 WAF_V2 閘道 v1 標準或 WAF 閘道相關聯的組態。

### <a name="caveatslimitations"></a>Caveats\Limitations

* 新的 v2 閘道有新的公用和私人 IP 位址。 您無法將現有的 v1 閘道順暢運作，v2 與相關聯的 IP 位址。 不過，您可以配置現有 （未配置） 公用或私人 IP 位址給新的 v2 閘道。
* 您必須提供另一個子網路 IP 位址空間 v1 閘道所在虛擬網路內。 指令碼無法建立任何已經 v1 閘道的現有子網路中的 v2 閘道。 不過，如果已經有現有的子網路可能仍然有效的 v2 閘道，會提供足夠的 IP 位址空間。
* 若要移轉的 SSL 組態，您必須指定 v1 閘道中使用的所有 SSL 憑證。
* 如果您擁有 V1 閘道啟用 FIPS 模式時，它不會移轉到新的 v2 閘道。 V2 中不支援 FIPS 模式。
* v2 不支援 IPv6，因此不會移轉已啟用 IPv6 v1 閘道。 如果您執行指令碼時，它可能不完整。
* 如果 v1 閘道的私人 IP 位址，則指令碼會建立公用 IP 位址和新的 v2 閘道的私人 IP 位址。 v2 閘道目前不支援只有私人 IP 位址。

## <a name="download-the-script"></a>下載指令碼

下載移轉指令碼，從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAppGWMigration)。

## <a name="use-the-script"></a>使用指令碼

有兩個選項，讓您根據您的本機 PowerShell 環境設定和喜好設定：

* 如果您沒有 Azure Az 模組安裝，或不介意 Azure Az 模組正在解除安裝時，最好的選擇是使用`Install-Script`執行指令碼的選項。
* 如果您要讓 Azure Az 模組時，最好是下載指令碼，並直接執行它。

若要判斷您是否已安裝的 Azure Az 模組，請執行`Get-InstalledModule -Name az`。 如果您沒有看到任何安裝 Az 模組，則您可以使用`Install-Script`方法。

### <a name="install-using-the-install-script-method"></a>安裝使用安裝指令碼方法

若要使用此選項，您不能安裝在您的電腦上的 Azure Az 模組。 如果安裝，則下列命令會顯示錯誤。 您可以解除安裝 Azure Az 模組，或使用其他選項來手動下載指令碼，並執行它。
  
使用下列命令來執行指令碼：

`Install-Script -Name AzureAppGWMigration`

此命令也會安裝必要的 Az 模組。  

### <a name="install-using-the-script-directly"></a>直接使用指令碼安裝

如果您沒有安裝某些 Azure Az 模組並不能將它們解除安裝 （或不想要將它們解除安裝），您可以手動下載指令碼使用**手動下載** 索引標籤中的指令碼下載連結。 指令碼會為原始 nupkg 檔案下載。 若要從這個 nupkg 檔案安裝指令碼，請參閱[手動封裝下載](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download)。

執行指令碼：

1. 使用`Connect-AzAccount`連接到 Azure。

1. 使用`Import-Module Az`Az 模組匯入。

1. 執行`Get-Help AzureAppGWMigration.ps1`檢查必要的參數：

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale
   ```

   指令碼的參數：
   * **resourceId: [String]:所需**-這是您現有的標準 v1 或 WAF v1 閘道的 Azure 資源識別碼。 若要尋找此字串值，請瀏覽至 Azure 入口網站、 選取您的應用程式閘道或 WAF 的資源，然後按一下**屬性**閘道的連結。 在該頁面上，位於資源識別碼。

     您也可以執行下列 Azure PowerShell 命令來取得資源識別碼：

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:所需**-這是新的子網路，其中包含新的 v2 閘道的 IP 位址空間已配置 （或想要配置）。 這必須以 CIDR 標記法指定。 例如: 10.0.0.0/24. 您不需要事先建立此子網路。 指令碼會建立它，如果不存在。
   * **appgwName: [String]:選擇性**。 這是您指定做為新的 Standard_v2 或 WAF_v2 閘道的名稱使用的字串。 如果未提供這個參數，現有的 v1 閘道的名稱將用於後置詞*並將 _v2*附加。
   * **sslCertificates: [PSApplicationGatewaySslCertificate]:選擇性**。  您建立來代表從 v1 閘道的 SSL 憑證的 PSApplicationGatewaySslCertificate 物件的逗號分隔的清單，都必須上傳至新的 v2 閘道。 針對每個您設定您的標準 v1 或 WAF v1 閘道的 SSL 憑證，您可以建立新的 PSApplicationGatewaySslCertificate 物件透過`New-AzApplicationGatewaySslCertificate`如下所示的命令。 您需要 SSL 憑證檔案和密碼的路徑。

       這個參數只是選擇性，如果您沒有為您的 v1 閘道或 WAF 設定的 HTTPS 接聽程式。 如果您有至少一個 HTTPS 接聽程式設定，您必須指定此參數。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      您可以傳入`$mySslCert1, $mySslCert2`（以逗號分隔） 在上述範例中為指令碼中的這個參數的值。
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]:選擇性**。 您建立來代表 PSApplicationGatewayTrustedRootCertificate 物件的逗號分隔的清單[受信任根憑證](ssl-overview.md)v2 閘道從後端執行個體的驗證。  

      若要建立 PSApplicationGatewayTrustedRootCertificate 物件的清單，請參閱[新增 AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)。
   * **privateIpAddress: [String]:選擇性**。 特定私人 IP 位址，您想要關聯到新的 v2 閘道。  這必須是從您配置給新的 v2 閘道相同的 VNet。 如果未指定，指令碼會為 v2 閘道配置私人 IP 位址。
    * **publicIpResourceId: [String]:選擇性**。 您想要配置給新的 v2 閘道您訂用帳戶中的公用 IP 位址 (標準 SKU) 資源的 resourceId。 如果未指定，指令碼會配置在相同的資源群組中的新公用 IP。 名稱為 v2 閘道的名稱，加*IP*附加。
   * **validateMigration: [切換]:選擇性**。 如果您想要執行某些基本設定的設定，比較驗證 v2 閘道建立並設定複本之後的指令碼，請使用此參數。 根據預設，會不進行任何驗證。
   * **enableAutoScale: [切換]:選擇性**。 如果您想要在建立之後，新的 v2 閘道上啟用自動調整的指令碼，請使用此參數。 根據預設，會停用自動調整。 您可以一律以手動方式啟用它之後新建立的 v2 閘道。

1. 執行指令碼使用適當的參數。 可能需要五到七分鐘才能完成。

    **範例**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>移轉用戶端流量

首先，再次檢查已成功建立具有正確的組態的 新的 v2 閘道的指令碼移轉透過從 v1 閘道。 您可以從 Azure 入口網站來確認。

此外，傳送以手動測試的少量透過 v2 閘道的流量。
  
以下是幾個案例，其中您目前的應用程式閘道 （標準） 可能會收到用戶端流量和我們的建議，每個：

* **與您的標準 v1 或 WAF v1 閘道相關聯的自訂 DNS 區域 (例如，contoso.com) （使用 A 記錄） 的前端 IP 位址所指向**。

    您可以更新您的 DNS 記錄指向與 Standard_v2 應用程式閘道相關聯的前端 IP 或 DNS 標籤。 根據您的 DNS 記錄上設定 TTL，可能需要一段時間移轉至新的 v2 閘道您所有的用戶端流量。
* **自訂 DNS 區域 (例如，contoso.com) 指向 DNS 標籤 (例如： *myappgw.eastus.cloudapp.azure.com*使用 CNAME 記錄) 與 v1 閘道相關聯**。

   您有兩個選擇：

  * 如果您使用您的應用程式閘道上的公用 IP 位址，您可以控制，以累加方式路由傳送到新的 v2 閘道的流量 （加權的流量路由方法） 使用流量管理員設定檔的細微移轉。

    您可以藉由新增的 v1 與 v2 應用程式閘道的 DNS 標籤[流量管理員設定檔](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)，所以流量管理員網域 （例如，您自訂 DNS 記錄 (例如 www.contoso.com) 和contoso.trafficmanager.net)。
  * 或者，您可以更新您的自訂網域的 DNS 記錄以指向新的 v2 應用程式閘道的 DNS 標籤。 根據您的 DNS 記錄上設定 TTL，可能需要一段時間移轉至新的 v2 閘道您所有的用戶端流量。
* **您的用戶端連接至前端應用程式閘道的 IP 位址**。

   更新您的用戶端使用新建立的 v2 應用程式閘道相關聯的 IP 位址。 我們建議您不要直接使用 IP 位址。 請考慮使用 DNS 名稱標籤 (例如 yourgateway.eastus.cloudapp.azure.com) 您可以自己自訂的 DNS 區域 (例如，contoso.com) 的 CNAME 的應用程式閘道相關聯。

## <a name="common-questions"></a>常見問題

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>是否有使用 Azure PowerShell 指令碼為 v2，從 v1 移轉設定任何限制？

是。 請參閱[注意事項/限制](#caveatslimitations)。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>適合這篇文章和 Azure PowerShell 指令碼以及應用程式閘道 WAF 產品嗎？ 

是。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>不會移轉至新建立的 v2 閘道我 v1 閘道的流量也切換 Azure PowerShell 指令碼？

沒有。 Azure PowerShell 指令碼只會移轉設定。 實際流量移轉是由您負責在您的控制項。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 指令碼建立新的 v2 閘道大小適用於處理所有目前由我 v1 閘道的流量？

Azure PowerShell 指令碼會建立新的 v2 閘道適當的大小，以處理現有的 v1 閘道上的流量。 根據預設，停用自動調整，但當您執行指令碼時，您可以啟用自動調整。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>我設定我的 v1 閘道，將記錄傳送至 Azure 儲存體。 指令碼是否複寫這項設定，以及 v2？

沒有。 指令碼不會複寫此組態的 v2。 您必須將記錄組態新增至已移轉的 v2 閘道的分開。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>我遇到一些與使用此指令碼的問題。 如何取得協助？
  
您可以傳送電子郵件給appgwmigrationsup@microsoft.com、 使用 Azure 支援，請開啟支援案例，或兩者皆可行。

## <a name="next-steps"></a>後續步驟

[深入了解應用程式閘道 v2](application-gateway-autoscaling-zone-redundant.md)
