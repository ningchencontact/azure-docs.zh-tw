---
title: 針對具有 App Service 的 Azure 應用程式閘道進行疑難排解-重新導向至 App Service 的 URL
description: 本文提供有關如何在搭配 Azure App Service 使用 Azure 應用程式閘道時, 針對重新導向問題進行疑難排解的資訊。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347874"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>針對應用程式閘道中的 App Service 問題進行疑難排解

瞭解如何診斷並解決使用應用程式閘道的應用程式伺服器做為後端目標時所遇到的問題

## <a name="overview"></a>總覽

在本文中, 您將瞭解如何針對下列問題進行疑難排解:

> [!div class="checklist"]
> * 當有重新導向時, 會在瀏覽器中公開 App Service 的 URL
> * App Service 的 ARRAffinity Cookie 網域設定為 App Service 主機名稱 (example.azurewebsites.net), 而不是原始主機

當後端應用程式傳送重新導向回應時, 您可能會想要將用戶端重新導向至不同于後端應用程式所指定的 URL。 例如, 您可能會想要在應用程式閘道後方裝載 app service 時執行此動作, 並要求用戶端重新導向至其相對路徑。 (例如, 從 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重新導向)。當 app service 傳送重新導向回應時, 它會在其回應的位置標頭中使用相同的主機名稱, 就像它從應用程式閘道收到的要求中的一樣。 因此, 用戶端會直接向 contoso.azurewebsites.net/path2 提出要求, 而不是透過應用程式閘道 (contoso.com/path2)。 不需要略過應用程式閘道。

此問題可能是由於下列主要原因而發生:

- 您已在 App Service 上設定重新導向。 重新導向可以簡單到將尾端斜線加入至要求。
- 您有 Azure AD 驗證會導致重新導向。

此外, 當您使用應用程式閘道後方的應用程式服務時, 與應用程式閘道 (example.com) 相關聯的功能變數名稱會與 App Service 的功能變數名稱 (例如 example.azurewebsites.net) 不同, 因為您會注意到App Service 所設定之 ARRAffinity cookie 的定義域值會包含「example.azurewebsites.net」功能變數名稱, 這不是您想要的名稱。 原始主機名稱 (example.com) 應該是 cookie 中的功能變數名稱值。

## <a name="sample-configuration"></a>範例組態

- HTTP 接聽程式:基本或多網站
- 後端位址集區:App Service
- HTTP 設定:已啟用 [從後端位址挑選主機名稱]
- 探查已啟用 [從 HTTP 設定挑選主機名稱]

## <a name="cause"></a>原因

因為 App Service 是多租使用者服務, 所以它會使用要求中的主機標頭, 將要求路由傳送至正確的端點。 不過, 應用程式服務的預設功能變數名稱 (*. azurewebsites.net (例如 contoso.azurewebsites.net)) 與應用程式閘道的功能變數名稱 (例如 contoso.com) 不同。 由於用戶端的原始要求具有應用程式閘道的功能變數名稱 (contoso.com) 做為主機名稱, 因此您必須設定應用程式閘道, 以便在將要求路由至時, 將原始要求中的主機名稱變更為應用程式服務的主機名稱app service 後端。  若要達到此目的, 請使用應用程式閘道的 HTTP 設定設定中的 [從後端位址挑選主機名稱] 和 [健康情況探查設定] 中的 [從後端 HTTP 設定挑選主機名稱] 參數。



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

因此, 當 App Service 進行重新導向時, 它會使用位置標頭中覆寫的主機名稱 "contoso.azurewebsites.net", 而不是原始主機名稱 "contoso.com", 除非另有設定。 您可以在下方查看範例要求和回應標頭。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
在上述範例中, 您可以注意到回應標頭的狀態碼為301以進行重新導向, 而 location 標頭具有 App Service 的主機名稱, 而不是原始主機名稱 "www.contoso.com"。

## <a name="solution-rewrite-the-location-header"></a>解決方案:重寫 Location 標頭

您必須將 location 標頭中的主機名稱設定為應用程式閘道的功能變數名稱。 若要這麼做, 請建立具有條件的[重寫規則](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers), 以評估回應中的位置標頭是否包含 azurewebsites.net, 並執行動作來重寫 location 標頭, 使其具有應用程式閘道的主機名稱。  請參閱[如何重寫 location 標頭的](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)指示。

> [!NOTE]
> HTTP 標頭重寫支援僅適用于應用程式閘道的[Standard_V2 和 WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 。 如果您使用 V1 SKU, 我們強烈建議您[從 v1 遷移至 V2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) , 以便能夠使用 V2 SKU 提供的重寫和其他[先進功能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>替代解決方案:使用 App service 的自訂網域, 而不是預設的功能變數名稱

如果您使用 V1 SKU, 您將無法重寫 location 標頭, 因為這項功能僅適用于 V2 SKU。 因此, 若要解決重新導向問題, 您也必須將應用程式閘道接收的相同主機名稱傳遞給 App Service, 而不是執行主機覆寫。

這麼做之後, App Service 將會在相同的原始主機標頭上執行重新導向 (如果有的話), 而這會指向應用程式閘道而不是它自己的。

若要達到此目的, 您必須擁有自訂網域, 並遵循下面所述的程式。

- 向 App Service 的自訂網域清單註冊網域。 為此, 您的自訂網域中必須有指向 App Service 的 FQDN 的 CNAME。 如需詳細資訊, 請參閱將[現有的自訂 DNS 名稱對應至 Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 完成後, 您的 App Service 已準備好接受主機名稱 "www.contoso.com"。 現在請變更 DNS 中的 CNAME 專案, 將其指向應用程式閘道的 FQDN。 例如, "appgw.eastus.cloudapp.azure.com"。

- 當您執行 DNS 查詢時, 請確定您的網域 "www.contoso.com" 解析為應用程式閘道的 FQDN。

- 將您的自訂探查設定為停用 [從後端 HTTP 設定挑選主機名稱]。 您可以從入口網站完成這項作業, 方法是在 [AzApplicationGatewayProbeConfig] 命令中不使用-PickHostNameFromBackendHttpSettings 參數, 取消核取探查設定和 PowerShell 中的核取方塊。 在探查的 [主機名稱] 欄位中, 輸入您 App Service 的 FQDN "example.azurewebsites.net", 因為從應用程式閘道傳送的探查要求將會在主機標頭中包含此項。

  > [!NOTE]
  > 執行下一個步驟時, 請確定您的自訂探查未與您的後端 HTTP 設定相關聯, 因為您的 HTTP 設定仍然會在此時啟用 [從後端位址挑選主機名稱] 切換。

- 設定您應用程式閘道的 HTTP 設定, 以停用 [從後端位址挑選主機名稱]。 透過在 New-azapplicationgatewaybackendHTTPsettings 設定設定命令中不使用-PickHostNameFromBackendAddress 參數, 取消核取此核取方塊, 即可從入口網站完成這項作業。

- 將自訂探查關聯回後端 HTTP 設定, 並確認後端健康情況良好。

- 完成此作業之後, 應用程式閘道現在應該將相同的主機名稱 "www.contoso.com" 轉送至 App Service, 然後重新導向將會發生在相同的主機名稱上。 您可以在下方查看範例要求和回應標頭。

若要使用適用于現有設定的 PowerShell 來執行上述步驟, 請遵循下面的範例 PowerShell 腳本。 請注意我們如何在探查和 HTTP 設定中使用-PickHostname 參數。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
