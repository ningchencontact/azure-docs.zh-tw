---
title: Azure Front Door Service - 後端和後端集區 | Microsoft Docs
description: 本文可協助您了解後端和後端集區在 Front Door 設定中的作用為何。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123686"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Azure Front Door Service 中的後端和後端集區
本文針對如何用 Front Door 對應應用程式的部署而說明各個相關的概念。 我們也將說明不同的術語，以在 Front 各地的應用程式後端的媒體櫃門組態的意義。

## <a name="backend-pool"></a>後端集區
Front Door 中的後端集區指的是一組對等後端，可接收其應用程式的相同類型流量。 換句話說，它是您在世界各地的應用程式執行個體邏輯群組，可以接收相同的流量，而且可以回應預期行為。 這些後端通常會部署至不同的區域或相同的區域內。 此外，這些後端全部可以處於主動-主動部署模式，或者也可以定義為主動/被動設定。

後端集區也會定義應該如何透過健康狀態探查評估不同後端的健康狀態，以及相應地定義後端之間應該如何進行負載平衡。

### <a name="health-probes"></a>健康狀態探查
Front Door 會將定期 HTTP/HTTPS 探查要求傳送至您的每個已設定後端，以判斷每個後端的鄰近性和健康狀態來為終端使用者要求進行負載平衡。 後端集區的健康狀態探查設定定義如何輪詢您應用程式後端的健康狀態。 下列設定適用於設定和負載平衡：

1. **路徑**：其中探查要求會被傳送到後端集區中的所有後端的 URL 路徑。 例如，如果您的其中一個後端是 `contoso-westus.azurewebsites.net`，並且路徑設定為 `/probe/test.aspx`，則 Front Door 環境會在假設通訊協定設定為 HTTP 的情況下將健康狀態探查要求傳送至 http://contoso-westus.azurewebsites.net/probe/test.aspx。 
2. **通訊協定**：定義是否會透過 HTTP 或 HTTPS 通訊協定傳送從前端到您的後端健康情況探查要求。
3. **間隔 （秒）**:此欄位會定義健全狀況探查的頻率，到後端，也就是將在其中每個前端環境將會傳送探查的間隔。 注意：如果您需要更快速的容錯移轉，則請將此欄位設定為較低的值。 不過，值設得越低，後端所收到的健康狀態探查就越多。 若要了解 Front Door 在您後端所產生的探查數量，請查看範例。 假設間隔設定為 30 秒，且全域大約有 90 個 Front Door 環境或 POP。 則您的每個後端大約每秒都會收到 3-5 個探查要求。

如需詳細資料，請參閱[健康狀態探查](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>負載平衡設定
後端集區的負載平衡設定會定義出應如何評估健康狀態探查的結果，從而決定後端的狀況為良好或不良，同時也會定義後端集區中各後端間的流量應如何進行負載平衡。 下列設定適用於設定和負載平衡：

1. **取樣大小**:這個屬性會識別我們需要考量後端健康狀態評估的健全狀況探查的幾個範例。
2. **成功的取樣大小**:這個屬性會定義，取樣大小的如前文所述，多少範例我們需要呼叫為狀況良好的後端的成功檢查。 
</br>例如，假設針對 Front Door，您已將健康狀態探查「間隔」設定為 30 秒、將「樣本大小」設定為 '5'，並將「成功樣本大小」設定為 '3'。 則表示每次評估後端的健康狀態探查時，我們都會查看最後五個跨越最後 150 秒 (=5*30 秒) 的樣本，除非其中有 3 個以上的探查成功，否則此後端的狀況就會宣告為不良。 假設只有兩個探查成功，此後端就會標示為狀況不良。 下次執行評估時，如果發現最後五個探查中有三個成功，便會再次將此後端標示為狀況良好。
3. **延遲敏感度 （額外的延遲）**:延遲敏感度欄位定義是否要將要求傳送到延遲度量，或將要求轉送到最接近的後端的敏感度範圍內的後端的大門。 若要深入了解，請參閱 Front Door 的[最小延遲路由方法](front-door-routing-methods.md#latency)。

## <a name="backend"></a>後端
後端等同於區域中應用程式的部署執行個體。 Front Door 同時支援 Azure 和非 Azure 後端，因此該區域不是只能使用 Azure 區域，也可以是內部部署資料中心或是某個其他雲端中的應用程式執行個體。

在 Front Doors 的內容中，後端指的是應用程式的主機名稱或公用 IP，能用來處理用戶端的要求。 因此，不應該將後端與您的資料庫層或儲存層等混淆，而是應該當成您應用程式後端的公用端點予以檢視。

當您在 Front Door 的後端集區中新增後端時，必須填寫下列詳細資料：

1. **後端主機類型**:您想要新增的資源類型。 Front Door 支援從應用程式服務、雲端服務或儲存體自動探索您的應用程式後端。 若要使用 Azure 或甚至非 Azure 後端中的其他資源，請選取 [自訂主機]。 注意：設定期間，API 不會驗證是否可以從 Front Door 環境存取後端，您必須自行確認 Front Door 是否能連線至您的後端。 
2. **訂用帳戶和後端的主機名稱**:如果您未選取後端主機的 「 自訂主機 」 類型，則需要範圍下運作，並選擇適當的訂用帳戶及對應的後端主機名稱，從使用者介面中選取您的後端。
3. **後端主機標頭**:主機標頭值傳送至每個要求的後端。 如需詳細資料，請參閱[後端主機標頭](#hostheader)。
4. **優先順序**：如果您想要針對所有流量使用某個主要服務後端，則可以將優先順序指派給不同的後端，並提供備用方案，以防發生主要或備份後端無法使用的情況。 深入了解[優先順序](front-door-routing-methods.md#priority)。
5. **權數**:當您想要平均還是根據權數係數將流量分散到一組後端時，可以將權數指派給不同的後端。 深入了解[權數](front-door-routing-methods.md#weighted)。


### <a name = "hostheader"></a>後端主機標頭

Front Door 轉送至後端的要求具有主機標頭欄位，後端會使用它來擷取目標資源。 此欄位的值通常來自後端 URI，並且具有主機和連接埠。 例如，針對 `www.contoso.com` 提出的要求將會有主機標頭 `www.contoso.com`。 如果您要使用 Azure 入口網站設定後端，則填入此欄位的預設值就是後端的主機名稱。 例如，如果您的後端是 `contoso-westus.azurewebsites.net`，則在 Azure 入口網站中，後端主機標頭的自動填入值會是 `contoso-westus.azurewebsites.net`。 
</br>不過，如果您使用 Resource Manager 範本或其他機制，而且未明確地設定此欄位，則 Front Door 會傳送連入主機名稱作為主機標頭的值。 例如，如果針對 `www.contoso.com` 提出要求，而且您的後端為後端主機標頭欄位空白的 `contoso-westus.azurewebsites.net`，則 Front Door 會將主機標頭設定為 `www.contoso.com`。

大部分的應用程式後端 (例如 Web Apps、Blob 儲存體和雲端服務) 都需要主機標頭符合後端的網域。 不過，前端主機路由傳送至您的後端會有不同的主機名稱，例如 www\.contoso.azurefd.net。 如果您要設定的後端需要主機標頭符合後端主機名稱，則應該確定「後端主機標頭」也有後端的主機名稱。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>設定後端的後端主機標頭
可以在後端集區區段中設定後端的「後端主機標頭」欄位。

1. 開啟 Front Door 資源，然後按一下要設定後端的後端集區。

2. 如果您尚未新增任何後端，或編輯現有後端，則請新增後端。 「後端主機標頭」欄位可以設定為自訂值或空白，表示連入要求的主機名稱將會當成主機標頭值使用。



## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。