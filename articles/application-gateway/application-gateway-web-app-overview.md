---
title: 多租用戶後端，例如 Azure App service，以 Azure 應用程式閘道的概觀
description: 本頁面提供多租用戶後端的應用程式閘道支援概觀。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224493"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>應用程式閘道支援的多租用戶後結束，例如 App service

多租用戶中的 web 伺服器的架構設計，在相同的 web 伺服器執行個體上執行多個網站。 主機名稱用來區別不同的應用程式裝載。 根據預設，應用程式閘道不會變更來自用戶端的連入 HTTP 主機標頭，而會將標頭原封不動地傳送回後端。 這個適用於後端集區成員，例如 Nic、 虛擬機器擴展集，公用 IP 位址、 內部 IP 位址和 FQDN，這些並不依賴特定主機標頭或 SNI 擴充功能來解析為正確的端點。 不過，有許多 Azure App service web 應用程式等 Azure API 管理的服務，是在本質上的多租用戶，並依賴特定主機標頭或 SNI 擴充功能來解析為正確的端點。 通常，應用程式，接著是應用程式閘道相關聯的 DNS 名稱，DNS 名稱與不同的後端服務的網域名稱。 因此，應用程式閘道所收到的原始要求中的主機標頭不是後端服務的主機名稱相同。 因為這個緣故，除非後端應用程式閘道的要求中的主機標頭會變更為主機名稱後, 端服務的多租用戶後端不能解析為正確的端點的要求。 

應用程式閘道提供功能，可讓使用者根據後端的主機名稱的要求中的 HTTP 主機標頭會覆寫。 這項功能可支援多租用戶後端，例如 Azure App service web apps 和 API 管理。 這項功能是適用於 v1 和 v2 標準和 WAF Sku。 

![主機覆寫](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 這不適用於 Azure App service 環境 (ASE) 因為 ASE 是不同於 Azure 應用程式服務，也就是多租用戶資源的專用的資源。

## <a name="override-host-header-in-the-request"></a>覆寫在要求中的主機標頭

讓您指定主機覆寫定義於[http-settings](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)而且會在規則建立期間套用至任何後端集區。 支援覆寫主機標頭和 SNI 擴充功能的多租用戶後端的下列兩種方法：

- 在 HTTP 設定中明確輸入為固定值設定的主機名稱的能力。 這項功能可確保主機標頭會覆寫此值適用於特定的 HTTP 設定會套用其中後端集區的所有流量。 使用端對端 SSL 時，這個覆寫的主機名稱會使用於 SNI 擴充功能。 這項功能可促成以下案例：其中的後端集區伺服器陣列預期會有不同於連入客戶主機標頭的主機標頭。

- 從後端集區成員的 IP 或 FQDN 衍生主機名稱的功能。 HTTP 設定也會提供一個選項來動態選擇從後端集區成員的 FQDN 的主機名稱，如果設定衍生主機名稱，從個別的後端集區成員的選項。 使用端對端 SSL 時，此主機名稱衍生自 FQDN 且使用於 SNI 擴充功能。 這項功能可促成以下案例：其中的後端集區可以有兩個或多個多租用戶 PaaS 服務，例如 Azure Web 應用程式，而且每個成員的要求主機標頭包含衍生自其 FQDN 的主機名稱。 如需實作此案例中，我們會使用呼叫的 HTTP 設定中的交換器[挑選後端位址中的主機名稱](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address)這將會以動態方式覆寫後端集區中所述的原始要求中的主機標頭。  例如，如果您的後端集區 FQDN 包含 「 contoso11.azurewebsites.net"和"contoso22.azurewebsites.net 」，即 contoso.com 原始要求的主機標頭將會覆寫 contoso11.azurewebsites.net 或 contoso22.azurewebsites.net當要求傳送至適當的後端伺服器。 

  ![Web 應用程式案例](./media/application-gateway-web-app-overview/scenario.png)

使用這項功能，客戶可將 HTTP 設定中的選項和自訂探查指定為適當的組態。 這項設定會接著使用規則繫結至接聽程式和後端集區。

## <a name="special-considerations"></a>特殊考量

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL 終止和端對端 SSL，與多租用戶的服務

多租用戶的服務支援 SSL 終止和端對端 SSL 加密。 在應用程式閘道的 SSL 終止、 SSL 憑證會繼續必須新增至應用程式閘道接聽程式。 不過，如果端對端 SSL，受信任的 Azure 服務，例如 Azure App service web 應用程式不需要加入允許清單中的應用程式閘道後端。 因此，不是需要加入任何驗證憑證。 

![端對端 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

請注意，圖中，不需要將驗證憑證時，會選取應用程式服務作為後端。

### <a name="health-probe"></a>健全狀況探查

覆寫中的主機標頭**http-settings**只會影響且其路由的要求。 它不會影響健康情況探查行為。 若要讓端對端功能運作，必須修改探查和 HTTP 設定，以反映正確的組態。 除了提供探查組態中指定的主機標頭的功能，自訂探查也支援從目前設定的 HTTP 設定衍生主機標頭的功能。 在探查組態中使用 `PickHostNameFromBackendHttpSettings` 參數即可指定此組態。

### <a name="redirection-to-app-services-url-scenario"></a>重新導向至 App Service 的 URL 案例

可以是來自應用程式服務的回應中的主機名稱可能會將導向使用者瀏覽器案例 *。 azurewebsites.net 主機名稱，而不是應用程式閘道相關聯的網域。 可能會發生此問題時：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure AD 身份验证导致重定向。

若要解決這種情況下，請參閱[疑難排解 重新導向至 App service 的 URL 問題](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。

## <a name="next-steps"></a>後續步驟

了解如何設定用的多租用戶應用程式，例如 Azure App service web 應用程式的應用程式閘道後端集區成員身分瀏覽[與應用程式閘道設定 App Service web 應用程式](https://docs.microsoft.com/azure/application-gateway/create-web-app)
