---
title: App Service-重新導向至 App Service 的 URL 使用的 Azure 應用程式閘道進行疑難排解
description: 本文章提供有關如何疑難排解 Azure App Service 搭配使用 Azure 應用程式閘道時的重新導向問題的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715164"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>針對搭配 App Service 應用程式閘道進行疑難排解

了解如何診斷和解決應用程式閘道與後端伺服器的應用程式服務發生問題。

## <a name="overview"></a>總覽

在本文中，您將學習如何疑難排解下列問題：

> [!div class="checklist"]
> * App Service 的 URL 重新導向時，取得公開在瀏覽器
> * App Service 的 ARRAffinity Cookie 的網域設為應用程式服務主機名稱 (example.azurewebsites.net) 而不是原始主機

當您設定對外公開的應用程式閘道的後端集區中的 App Service，如果您有在您的應用程式程式碼中設定的重新導向，您可能會看到，當您在存取應用程式閘道時，您會被重新導向瀏覽器直接應用程式服務 URL。

此問題可能是由於下列主要原因：

- 您必須在您的 App Service 上設定的重新導向。 重新導向可以簡單，只要加入要求中包含結尾斜線。
- 您有 Azure AD 驗證，這會導致重新導向。
- 您已啟用的應用程式閘道的 HTTP 設定中的 「 挑選主機名稱從後端位址 」 參數。
- 您不需要您向您的 App Service 的自訂網域。

此外，當您使用應用程式閘道背後的應用程式服務，而且您用來存取應用程式閘道的自訂網域，可能會看到由 App Service 設定 ARRAffinity cookie 的網域值將會執行"example.azurewebsites.net 」 網域名稱。 如果您想要在 cookie 網域您原始主機名稱，請遵循這篇文章中的解決方案。

## <a name="sample-configuration"></a>範例組態

- HTTP 接聽程式：基本 」 或 「 多站台
- 後端位址集區：App Service 方案
- HTTP 設定：「 主機名稱從挑選後端位址 」 啟用
- 探查：「 主機名稱從挑選 HTTP 設定 」 啟用

## <a name="cause"></a>原因

App Service 可以只能存取含有已設定的主機名稱，在自訂網域設定中，依預設，「 example.azurewebsites.net"，而且如果您想要存取您的 App Service 使用主機名稱未註冊在 App Service 中或使用應用程式閘道應用程式閘道的 FQDN，您需要覆寫應用程式服務的主機名稱的原始要求中的主機名稱。

若要達成此目標應用程式閘道，我們使用 「 選取的主機名稱從後端位址 」 的交換器 HTTP 設定中和探查來處理，我們使用 [選取主機名稱從後端 HTTP 設定] 的探查組態中。

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

因此，當 App Service 會重新導向，它會使用"example.azurewebsites.net"Location 標頭中的主機名稱而非原始的主機名稱除非另外設定。 您可以檢查的範例要求和回應標頭下方。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
在上述範例中，您可以注意到，回應標頭具有狀態碼 301 重新導向，而且位置標頭中有 App Service 的主機名稱，而非原始的主機名稱"www.contoso.com"。

## <a name="solution"></a>方案

如果不可行，我們必須將應用程式閘道收到的相同主機標頭傳遞至以及應用程式服務而不是執行主機覆寫，可以解決此問題不需要在應用程式端，不過的 重新導向。

一旦這麼做，App Service 會重新導向 （如果有的話） 上執行的相同原始主機標頭會指向應用程式閘道而非其本身。

若要達到此目的，您必須擁有自訂網域，並遵循下面所述的程序。

- 註冊自訂網域清單的 App Service 網域。 這麼做，您必須有 CNAME 中自訂網域指向 App Service 的 FQDN。 如需詳細資訊，請參閱 <<c0> [ 將現有的自訂 DNS 名稱對應至 Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 完成之後，您的 App Service 是準備好接受主機名稱"www.contoso.com"。 現在，請變更您的 CNAME 項目，指向回到應用程式閘道的 FQDN 的 DNS 中。 例如，"appgw.eastus.cloudapp.azure.com。 」

- 請確定執行 DNS 查詢時，您的網域"www.contoso.com"解析為應用程式閘道的 FQDN。

- 設定您的自訂探查，以停用 [挑選主機名稱從後端 HTTP 設定]。 這可從入口網站中的探查設定的核取方塊取消核取，並在 PowerShell 中不使用-PickHostNameFromBackendHttpSettings 切換移入集 AzApplicationGatewayProbeConfig 命令。 在 [探查的主機名稱] 欄位中，輸入您 App Service 的 FQDN"example.azurewebsites.net 」 從應用程式閘道傳送的探查要求將會執行此主機標頭中。

  > [!NOTE]
  > 同時執行下一個步驟，請確定您的自訂探查沒有與後端 HTTP 設定，因為您的 HTTP 設定仍然有目前已啟用 「 挑選主機名稱從後端位址 」 參數。

- 設定您的應用程式閘道的 HTTP 設定，以停用 「 挑選主機名稱從後端位址 」。 做法是從入口網站取消核取此核取方塊，並在 PowerShell 中不使用-PickHostNameFromBackendAddress 組 AzApplicationGatewayBackendHttpSettings 命令中的切換。

- 建立後端 HTTP 設定自訂探查的關聯，並確認它是否狀況良好的後端健康狀態。

- 完成之後，應用程式閘道現在應該將相同的主機名稱"www.contoso.com"轉寄到 App Service，並重新導向將會發生在相同的主機名稱。 您可以檢查的範例要求和回應標頭下方。

若要實作現有的安裝程式中使用 PowerShell 先前所述的步驟，請遵循下列範例 PowerShell 指令碼。 請注意我們如何不使用-PickHostname 參數探查和 HTTP 設定組態中。

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
