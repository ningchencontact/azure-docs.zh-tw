---
title: 在 Azure 前端服務中的後端和後端集區 |Microsoft Docs
description: 這篇文章描述了哪些後端和後端集區在 Front 門組態。
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
ms.openlocfilehash: 2372f49c7280ee5c817f3d2f98cc80a196dae5f5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879194"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Azure Front Door Service 中的後端和後端集區
本文說明有關如何對應您的應用程式部署與 Azure 前端服務的概念。 它也會說明應用程式後端各地的大門組態中的不同詞彙。

## <a name="backends"></a>後端
後端是在區域中的應用程式的部署執行個體相等。 前端服務會支援 Azure 和非 Azure 後端，因此區域不只限於 Azure 區域。 此外，它可以是您的內部部署資料中心或其他雲端中的應用程式執行個體。

前端服務後端參考主機名稱或您的應用程式，它可以處理用戶端要求的公用 IP。 後端不應該與您的資料庫層、 儲存層和等等混淆。 後端應視為應用程式後端的公用端點。 當您新增前端的後端集區中的後端時，您也必須新增下列：

- **後端主機類型**。 您想要新增的資源類型。 前端服務會支援自動尋找您的應用程式後端從 app service、 雲端服務或儲存體。 如果您想要不同的資源，在 Azure 或甚至非 Azure 後端中，選取**自訂主機**。

    >[!IMPORTANT]
    >在設定期間，如果後端從前端環境無法存取，不驗證 Api。 請確定前端可以連線到您的後端。

- **訂用帳戶和後端的主機名稱**。 如果您尚未選取**自訂主機**後端的主控件類型，選取您的後端在 UI 中，選擇適當的訂用帳戶及對應的後端主機名稱。

- **後端主機標頭**。 主機標頭值傳送至每個要求的後端。 如需詳細資訊，請參閱 <<c0> [ 後端主機標頭](#hostheader)。

- **優先順序**。 當您想要針對所有流量使用主要服務後端，請將優先順序指派給您不同的後端中。 此外，如果無法使用主要或備份的後端時，才提供備份。 如需詳細資訊，請參閱 <<c0> [ 優先順序](front-door-routing-methods.md#priority)。

- **權數**。 將權數指派給您不同的後端，將流量分散到一組後端而言，平均分配還是根據加權的係數中。 如需詳細資訊，請參閱 <<c0> [ 加權](front-door-routing-methods.md#weighted)。

### <a name = "hostheader"></a>後端主機標頭

由大門轉送至後端的要求包含後端用來擷取在目標的資源的主機標頭欄位。 此欄位的值通常來自後端 URI，並且具有主機和連接埠。

例如，將為 www 提出的要求\.contoso.com 會有主機標頭 www\.contoso.com。 如果您使用 Azure 入口網站來設定您的後端時，此欄位的預設值是後端的主機名稱。 如果您的後端是 contoso westus.azurewebsites.net，在 Azure 入口網站中後, 端主機標頭的自動填入值將是 contoso westus.azurewebsites.net。 不過，如果您使用 Azure Resource Manager 範本或其他方法，而不需要明確設定此欄位，前端服務都將主機標頭的值傳送的連入的主機名稱。 如果已要求為 www\.contoso.com，且您的後端中有空白的標頭欄位的 contoso westus.azurewebsites.net，前端服務會將主機標頭設定為 www\.contoso.com。

大部分的應用程式後端 （Azure Web Apps、 Blob 儲存體和雲端服務） 要求主機標頭，來比對後端的定義域。 不過，前端主機路由傳送至您的後端會使用不同的主機名稱，例如 www\.contoso.azurefd.net。

如果您的後端需要的主機標頭，若要符合的後端主機名稱，請確認後端主機標頭中包含主機名稱後端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>設定後端的後端主機標頭

若要設定**後端主機標頭**欄位在 [後端集區] 區段中的後端：

1. 開啟大門資源，然後選取 後端，以設定的後端集區。

2. 如果您還沒有這麼做，或編輯現有的群組，請將後端。

3. 將後端主機標頭欄位設定為某個自訂值，或將它保留空白。 連入要求的主機名稱將用於主機標頭值。

## <a name="backend-pools"></a>後端集區
後端集區在 Front 門服務是指一組會收到其應用程式的類似流量的後端。 換句話說，它可以是您的應用程式執行個體的邏輯群組接收相同的流量和回應與預期的行為在世界各地。 這些後端會部署在不同的區域，或在相同區域內。 所有的後端可以在主動/主動部署模式，或什麼定義為主動/被動組態中。

後端集區定義不同的後端應如何評估透過健康情況探查。 它也會定義如何負載平衡它們之間發生。

### <a name="health-probes"></a>健康狀態探查
前端服務會定期的 HTTP/HTTPS 探查要求傳送至每個您設定的後端。 探查要求判斷載入每個後端的健康情況與鄰近性之間取得平衡您的使用者要求。 後端集區的健全狀況探查設定會定義我們如何輪詢的應用程式後端健康狀態。 下列設定可供負載平衡組態：

- **路徑**。 使用後端集區中的所有後端的探查要求的 URL。 比方說，如果您的後端的其中一個是 contoso westus.azurewebsites.net，路徑設定為 /probe/test.aspx 然後前端服務環境中，假設通訊協定已設為 HTTP，將會傳送健康情況探查要求為 http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx。

- **通訊協定**。 定義要從前端服務的健全狀況探查要求傳送到您的後端，使用 HTTP 或 HTTPS 通訊協定。

- **間隔 （秒）**。 定義您的後端或每個前端環境會傳送探查間隔健康情況探查的頻率。

    >[!NOTE]
    >針對更快速的容錯移轉，將間隔設定為較低的值。 越低的值，較高的健全狀況探查量收到您的後端。 例如，若間隔全域設定為使用 90 大門環境或 Pop 的 30 秒，每個後端會收到有關於每秒的 3 到 5 個探查要求。

如需詳細資訊，請參閱 <<c0> [ 健康情況探查](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>負載平衡設定
後端集區的負載平衡設定會定義我們如何評估健康情況探查。 這些設定會決定是否狀況良好或狀況不良的後端。 它們也查看如何在後端集區中的不同後端之間的負載平衡流量。 下列設定可供負載平衡組態：

- **取樣大小**。 識別我們需要考量後端健康狀態評估的健全狀況探查的幾個範例。

- **成功的取樣大小**。 定義如先前所述，取樣大小的成功呼叫後端的狀況良好所需的樣本數。 例如，假設大門健全狀況探查間隔為 30 秒，取樣大小是 5 秒，成功的取樣大小為 3 秒。 每當我們評估健康情況探查您的後端，我們探討的最後五個範例超過 150 秒 (5 x 30)。 若要宣告為狀況良好的後端需要至少三個的成功探查。

- **延遲敏感度 （額外的延遲）**。 定義是否要將要求傳送到後端延遲度量敏感度範圍內，或將要求轉送至最接近的後端的大門。

如需詳細資訊，請參閱 <<c0> [ 延遲最少基礎的路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [前端的運作方式](front-door-routing-architecture.md)