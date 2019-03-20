---
title: 管理多租用戶應用程式，例如 App service web 應用程式與 Azure 應用程式閘道入口網站的流量
description: 本文指導如何設定 Azure App service web apps 上的現有或新的應用程式閘道的後端集區中的成員。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176203"
---
# <a name="configure-app-service-with-application-gateway"></a>透過應用程式閘道設定 App Service

應用程式閘道可讓您的 Azure App service webapp 或其他多租用戶的服務作為後端集區成員。 

在本文中，您將了解：

> [!div class="checklist"]
>
> - 建立後端集區並加入它的 App Service
> - 啟用 「 挑選 Hostname"參數建立 HTTP 設定和自訂探查

## <a name="prerequisites"></a>必要條件

- 應用程式閘道：如果您沒有現有的應用程式閘道，請參閱如何[建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 應用程式服務：如果您沒有現有的 App service，請參閱[App service 文件](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>將應用程式服務新增為後端集區

1. 在 Azure 入口網站中，開啟您的應用程式閘道的 [設定] 檢視。

2. 底下**後端集區**，按一下**新增**來建立新的後端集區。

3. 提供適合的名稱後, 端集區。 

4. 底下**目標**、 按一下下拉式清單中，然後選擇**應用程式服務**做為選項。

5. 正下方的下拉式清單**目標**隨即顯示下拉式清單，其中會包含您的應用程式服務的清單。 從這個下拉式清單中，選擇您想要新增為後端集區成員，然後按一下 新增應用程式服務。

   ![應用程式服務後端](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>建立 App service 的 HTTP 設定

1. 底下**Http-settings**，按一下**新增**若要建立新的 HTTP 設定。

2. 輸入 HTTP 設定的名稱，您可以啟用或停用 Cookie 型同質性，根據您的需求。

3. 選擇 HTTP 或 HTTPS 通訊協定，根據您的使用案例。 

4. 核取方塊**用於 App Service** ，它會開啟**挑選主機名稱，從後端位址建立探查**並**挑選主機名稱，從後端位址**選項。 此選項也會自動建立的探查，以啟用參數，並使其關聯到此 HTTP 設定。

5. 按一下 **確定**建立 HTTP 設定。

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>建立規則，以將繫結接聽程式、 後端集區和 HTTP 設定

1. 底下**規則**，按一下**基本**建立新的基本規則。

2. 提供適當的名稱，然後選取將會接受應用程式服務的連入要求的接聽程式。

3. 在 **後端集區**下拉式清單中，選擇 後端集區，您在上面建立。

4. 在  **HTTP 設定**下拉式清單中，選擇 設定您在上面建立的 HTTP。

5. 按一下 **確定**儲存此規則。

   ![規則](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>限制存取

在這些範例中部署的 Web 應用程式會使用可直接從網際網路存取的公用 IP 位址。 這在您了解新功能及嘗試新事物時，可協助進行疑難排解。 但是，如果您想要將一項功能部署到生產環境，則需要新增更多限制。

您可以限制存取 Web 應用程式的其中一個方法是使用 [Azure App Service 靜態 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，您可以限制 Web 應用程式，使它只會從應用程式閘道接收流量。 您可以使用應用程式服務 IP 限制功能，將應用程式閘道 VIP 列為具有存取權的唯一位址。

## <a name="next-steps"></a>後續步驟

若要深入了解應用程式服務，以及其他與應用程式閘道的多租用戶支援，請參閱[應用程式閘道的多租用戶服務支援](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

如果您的 App service 的回應會重新導向至 App service 的 URL，請參閱如何[疑難排解 重新導向至 App service 的 URL 問題](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。
