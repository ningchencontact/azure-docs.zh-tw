---
title: App Service-重新導向至 App Service 的 URL 使用的 Azure 應用程式閘道進行疑難排解
description: 本文介绍如何排查将 Azure 应用程序网关与 Azure 应用服务配合使用时出现的重定向问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797784"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>針對搭配 App Service 應用程式閘道進行疑難排解

了解如何診斷和解決應用程式閘道與後端伺服器的應用程式服務發生問題。

## <a name="overview"></a>概觀

在本文中，您將學習如何疑難排解下列問題：

> [!div class="checklist"]
> * App Service 的 URL 重新導向時，取得公開在瀏覽器
> * App Service 的 ARRAffinity Cookie 的網域設為應用程式服務主機名稱 (example.azurewebsites.net) 而不是原始主機

在应用程序网关后端池中配置面向公众的应用服务时，如果在应用程序代码中配置了重定向，访问应用程序网关时你可能会看到，浏览器会直接将你重定向到应用服务 URL。

此问题的主要可能原因如下：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure AD 身份验证导致重定向。
- 在应用程序网关的 HTTP 设置中启用了“从后端地址中选取主机名”开关。
- 未将自定义域注册到应用服务。

此外，當您使用應用程式閘道背後的應用程式服務，而且您用來存取應用程式閘道的自訂網域，可能會看到由 App Service 設定 ARRAffinity cookie 的網域值將會執行"example.azurewebsites.net 」 網域名稱。 如果您想要在 cookie 網域您原始主機名稱，請遵循這篇文章中的解決方案。

## <a name="sample-configuration"></a>範例組態

- HTTP 侦听器：“基本”或“多站点”
- 后端地址池：App Service 方案
- HTTP 设置：已启用“从后端地址中选取主机名”
- 探测：已启用“从 HTTP 设置中选取主机名”

## <a name="cause"></a>原因

App Service 可以只能存取含有已設定的主機名稱，在自訂網域設定中，依預設，「 example.azurewebsites.net"，而且如果您想要存取您的 App Service 使用主機名稱未註冊在 App Service 中或使用應用程式閘道應用程式閘道的 FQDN，您需要覆寫應用程式服務的主機名稱的原始要求中的主機名稱。

为了在应用程序网关中实现此目的，我们在 HTTP 设置中使用了“从后端地址中选取主机名”开关；为了正常运行探测，我们在探测配置中使用了“从后端 HTTP 设置中选取主机名”。

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

因此，當 App Service 會重新導向，它會使用"example.azurewebsites.net"Location 標頭中的主機名稱而非原始的主機名稱除非另外設定。 可以查看下面的示例请求和响应标头。
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
在上面的示例中可以发现，响应标头包含重定向状态代码 301，location 标头包含应用服务的主机名而不是原始主机名“www.contoso.com”。

## <a name="solution"></a>解決方法

不在应用程序端使用重定向可以解决此问题，但是，如果无法做到这一点，则我们也必须将应用程序网关收到的同一主机标头传递给应用服务，而不要执行主机替代。

这样做后，应用服务会在指向应用程序网关而不是指向自身的同一原始主机标头中执行重定向（如果有）。

若要实现此目的，必须拥有一个自定义域并遵循下面所述的过程。

- 将该域注册到应用服务的自定义域列表。 为此，必须在自定义域中创建一个指向应用服务 FQDN 的 CNAME。 有关详细信息，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 这样做后，应用服务已准备好接受主机名“www.contoso.com”。 现在，请更改 DNS 中的 CNAME 条目，使其重新指向应用程序网关的 FQDN。 例如，"appgw.eastus.cloudapp.azure.com。 」

- 确保执行 DNS 查询时，域“www.contoso.com”解析为应用程序网关的 FQDN。

- 设置自定义探测以禁用“从后端 HTTP 设置中选取主机名”。 为此，可以在门户上取消选中探测设置中的相应复选框，或者在 PowerShell 中，不要在 Set-AzApplicationGatewayProbeConfig 命令中使用 -PickHostNameFromBackendHttpSettings 开关。 在 [探查的主機名稱] 欄位中，輸入您 App Service 的 FQDN"example.azurewebsites.net 」 從應用程式閘道傳送的探查要求將會執行此主機標頭中。

  > [!NOTE]
  > 执行下一步时，请确保自定义探测未关联到后端 HTTP 设置，因为此时 HTTP 设置中仍然启用了“从后端地址中选取主机名”开关。

- 设置应用程序网关的 HTTP 设置以禁用“从后端地址中选取主机名”。 为此，可以在门户上取消选中相应的复选框，或者在 PowerShell 中，不要在 Set-AzApplicationGatewayBackendHttpSettings 命令中使用 -PickHostNameFromBackendAddress 开关。

- 将自定义探测重新关联到后端 HTTP 设置，并验证后端的运行状况是否正常。

- 这样做后，应用程序网关应会将相同的主机名“www.contoso.com”转发到应用服务，并且同一个主机名上会发生重定向。 可以查看下面的示例请求和响应标头。

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
