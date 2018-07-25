---
title: Azure 地圖服務概觀 | Microsoft Docs
description: Azure 地圖服務簡介
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 93fe8dc3f8ff991cd6c48923d9e2073e4e93f1ad
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040842"
---
# <a name="what-is-azure-maps"></a>什麼是 Azure 地圖服務？
Azure 地圖服務是地理空間服務的集合，以最新的地圖資料作為後盾，因此您可以為 Web 和行動裝置應用程式提供準確的地理內容。 此服務包含 REST API 來轉譯地圖、搜尋景點、規劃景點路線、交通狀況、時區及位置服務的 IP。 您可以搭配熟悉的工具使用這些 API，快速開發和調整解決方案，進而將位置資訊整合到 Azure 解決方案中。 此服務與 REST API 搭配運作時，會提供 Web 式 JavaScript 控制項，讓開發作業更加輕鬆、有彈性且可在多個媒介中轉移。 

以下影片會深入說明 Azure 地圖服務：

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="services-in-azure-maps"></a>Azure 地圖服務中的服務

Azure 地圖服務是由下列六個服務組成，可以為 Azure 應用程式提供地理內容。 

### <a name="render-service"></a>轉譯服務

轉譯服務是針對開發人員設計的服務，可建立以地圖服務為主的 Web 和行動裝置應用程式。 此服務會使用高品質點陣圖形影像 (提供 19 個縮放層級)，或可完全自訂的向量格式地圖影像。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

轉譯服務現在提供具預覽功能的 API，可讓開發人員使用衛星影像。 如需詳細資訊，請參閱 [Azure 地圖服務轉譯 API](https://docs.microsoft.com/rest/api/maps/render)。


### <a name="route-service"></a>路線規劃服務 

路線規劃服務包含真實世界基礎結構的強固幾何計算，與多種交通模式的路線。 服務可讓開發人員跨幾種旅行模式 (例如汽車、貨車、自行車或步行) 計算方向。 服務也可以考量輸入，例如交通情況、重量限制，或危險材料運輸。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

路線規劃服務現在提供進階功能預覽，例如批次處理多個路線要求、起點與目的地之間移動時間和距離的對照表，並且根據您的時間或燃料需求，尋找可以移動的路線或距離。 如需路線規劃功能的詳細資訊，請參閱 [Azure 地圖服務路線規劃 API](https://docs.microsoft.com/rest/api/maps/route)。


### <a name="search-service"></a>搜尋服務

搜尋服務是針對開發人員而設計的服務，可搜尋地址、位置、依名稱或類別的企業清單，以及其他地理資訊。 搜尋服務也可以根據經度和緯度進行地址與交叉路口的[反向地理編碼](https://en.wikipedia.org/wiki/Reverse_geocoding)。 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

搜尋服務也提供進階功能，例如沿路線搜尋、在較廣泛的區域內搜尋、批次處理一組搜尋要求，以及搜尋較大的區域而不是位置點。 批次處理和區域搜尋的 API 目前處於預覽狀態。 如需搜尋功能的詳細資訊，請參閱 [Azure 地圖服務搜尋 API](https://docs.microsoft.com/rest/api/maps/search) 頁面。


### <a name="time-zone-service"></a>時區服務

時區服務可讓您使用其中經緯度或 [IANA 識別碼](http://www.iana.org/)，查詢目前、過去及未來的時區資訊。 時區服務也可將 Microsoft Windows 時區識別碼轉換為 IANA 時區、擷取 UTC 的時區位移，以及取得各自時區中的目前時間。 時區服務查詢的典型 JSON 回應如以下範例所示：

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

如需這項服務的詳細資訊，請瀏覽 [Azure 地圖服務時區 API](https://docs.microsoft.com/rest/api/maps/timezone) 頁面。

### <a name="traffic-service"></a>交通服務

交通服務是針對開發人員設計的一套 Web 服務，可建立需要路況的 Web 和行動裝置應用程式。 服務可提供兩種資料類型：
    * 交通流量 - 針對路網中的所有重要道路提供即時觀察速度和行進時間。 
    * 交通事故 - 提供有關路網中交通阻塞和交通事故的精確檢視。

![Azure 地圖服務流量](media/about-azure-maps/Introduction_Traffic.png)

如需詳細資訊，請瀏覽 [Azure 地圖服務交通 API](https://docs.microsoft.com/rest/api/maps/traffic) 頁面。

### <a name="ip-to-location"></a>IP 到地點

「IP 到地點」是預覽服務，可讓您針對指定 IP 位址擷取兩個字母的國家/地區代碼。 這項服務可協助您量身訂做您的應用程式，以符合特殊地緣政治條件約束，以及藉由根據地理位置變更應用程式的內容，來強化使用者體驗。 


## <a name="programming-model"></a>程式設計模型

Azure 地圖服務是針對行動性而建置，可以為跨平台的應用程式提供服務。 它使用與語言無關的程式設計模型，並且透過 [REST API](https://docs.microsoft.com/rest/api/maps/) 支援 JSON 輸出。 

此外，Azure 地圖服務使用簡單的程式設計模型提供方便的 [JavaScript 地圖控制項](https://docs.microsoft.com/javascript/api/azure-maps-javascript/?view=azure-iot-typescript-latest)，以便快速輕鬆開發 Web 和行動裝置應用程式。 


## <a name="usage"></a>使用量

存取地圖服務時須瀏覽至 [Azure 入口網站](http://portal.azure.com)，並建立 Azure 地圖服務帳戶。 

Azure 地圖服務會使用金鑰型驗證結構描述。 您的帳戶隨附預先為您產生的兩個金鑰。 在 Azure 地圖服務的要求中使用任一個金鑰，開始將這些位置功能直接整合到您的應用程式中。

## <a name="supported-regions"></a>支援區域
Azure 地圖服務 API 目前可在下列以外的所有國家/地區中使用： 

* 阿根廷
* 中國
* 印度
* 摩洛哥
* 巴基斯坦
* 南韓

請查看您目前的 IP 位址，並確認 IP 位址的位置不在上述未受支援國家/地區中。

## <a name="next-steps"></a>後續步驟

- 如需 Azure 地圖服務新功能的詳細資訊： 
    - [路線對照表、等時路線規劃、IP 查閱等等](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)。 
- 繼續試用示範服務的範例應用程式
    - [啟動示範互動式搜尋地圖](quick-demo-map-app.md)
