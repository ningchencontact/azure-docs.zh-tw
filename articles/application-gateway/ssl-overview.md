---
title: 在 Azure 應用程式閘道上啟用端對端 SSL
description: 本文將簡介應用程式閘道端對端 SSL 支援。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/12/2019
ms.author: victorh
ms.openlocfilehash: 16ba6b73dd0c64298f319d4b18750d753f166987
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849375"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>應用程式閘道端對端 SSL 的概觀

應用程式閘道支援在閘道上終止 SSL，之後流量通常會以未加密狀態流至後端伺服器。 這項功能可讓 Web 伺服器不必再負擔昂貴的加密和解密成本。 但对于某些客户而言，与后端服务器的未加密通信不是可以接受的选项。 此未加密的通訊可能是有安全性需要、合規性需求，或應用程式可能只接受安全連線。 對於這類應用程式，應用程式閘道可支援端對端 SSL 加密。

端對端 SSL 可讓您將機密資料以加密方式安全地傳輸到後端，同時又享有應用程式閘道所提供第 7 層負載平衡功能的好處。 部分功能為 cookie 型工作階段親和性、URL 型路由、支援根據站台或能注入 X-Forwarded-* 標頭的路由。

當設定為端對端 SSL 通訊模式時，應用程式閘道會在閘道上終止 SSL 工作階段，並解密使用者流量。 然後，它會套用所設定的規則來選取要將流量路由傳送到的適當後端集區執行個體。 應用程式閘道接著再起始連往後端伺服器的新 SSL 連線，並先使　用後端伺服器的公開金鑰憑證重新加密資料，再將要求傳輸至後端。 將通訊協定設定啟用端對端 SSL **BackendHTTPSetting**為 HTTPS，然後套用至後端集區。 已啟用端對端 SSL 之後端集區中的每個後端伺服器，都必須設有憑證以便能夠進行安全通訊。

SSL 原則適用於 frontend 和 backend 的流量。 後端，應用程式閘道會作為伺服器，並強制執行原則。 在後端，應用程式閘道會做為用戶端，以及 SSL 交握期間，將通訊協定/密碼資訊傳送為偏好瀏覽。

![端對端 SSL 案例][1]

在此範例中，使用端對端 SSL，將使用 TLS1.2 的要求路由傳送至 Pool1 中的後端伺服器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>端對端 SSL 和憑證白名單

應用程式閘道只會與已知的後端執行個體通訊，後者已將其憑證加入到應用程式閘道的允許清單。 若要啟用憑證允許清單，您必須將後端伺服器憑證的公開金鑰上傳至應用程式閘道 (不是根憑證)。 於是，只允許連接至已知和允許清單中的後端。 其餘的後端會導致閘道錯誤。 自我簽署憑證僅供測試之用，並不建議用於生產工作負載。 這類憑證必須如先前步驟所述，加入應用程式閘道的允許清單之中，才能使用。

> [!NOTE]
> 受信任的 Azure 服務 (例如 Azure App Service) 不需進行驗證憑證設定。

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU 的端對端 SSL

在「應用程式閘道 v2 SKU」中，「驗證憑證」已被淘汰且被「受信任的根憑證」取代。 它們的功能與「驗證憑證」類似，但有幾個主要差異：

- 憑證如果是由 CN 與 HTTP 後端設定中主機名稱相符的已知 CA 授權單位所簽署，則無須執行任何其他步驟，端對端 SSL 就能運作。 

   例如，如果後端憑證是由已知 CA 所簽發且 CN 為 contoso.com，而後端 HTTP 設定的主機欄位也設定為 contoso.com，便無須執行任何其他步驟。 您可以將後端 HTTP 設定通訊協定設定為 HTTPS，健康狀態探查和資料路徑就會都啟用 SSL。 如果您使用 Azure App Service 或其他 Azure web 服務作為後端，則這些也是以隱含方式信任和任何進一步的步驟所需的端對端 SSL。
- 如果憑證是自我簽署的，或由未知的媒介所簽署的，則若要在 v2 SKU 中啟用端對端 SSL，就必須定義受信任的根憑證。 「應用程式閘道」只會與符合下列條件的後端進行通訊：其伺服器憑證的根憑證符合與集區相關之後端 HTTP 設定中受信任根憑證清單內的其中一個憑證。
- 除了根憑證相符之外，「應用程式閘道」也會驗證後端 HTTP 設定中所指定的「主機」設定是否符合後端伺服器 SSL 憑證所出示的通用名稱 (CN)。 嘗試與後端建立 SSL 連線時，「應用程式閘道」會將「伺服器名稱指示」(SNI) 延伸模組設定為後端 HTTP 設定中所指定的「主機」。
- 如果選擇**從後端位址挑選主機名稱**，而不是從後端 HTTP 設定中的 [主機] 欄位挑選，則 SNI 標頭會一律設定為後端集區 FQDN，而後端伺服器 SSL 憑證上的 CN 必須符合其 FQDN。 在此案例中，不支援與 Ip 的後端集區成員。
- 根憑證是一個來自後端伺服器憑證的 Base64 編碼根憑證。

## <a name="next-steps"></a>後續步驟

了解端對端 SSL 之後，請移至[使用 PowerShell 以應用程式閘道設定端對端 SSL](application-gateway-end-to-end-ssl-powershell.md)，以使用端對端 SSL 來建立應用程式閘道。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
