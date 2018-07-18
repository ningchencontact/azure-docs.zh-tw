---
title: Azure 地圖服務概觀 | Microsoft Docs
description: Azure 地圖服務簡介
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 239134e7665b80ef78b6a3df12c14156bff9ae29
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317053"
---
# <a name="an-introduction-to-azure-maps"></a>Azure 地圖服務簡介
Azure 地圖服務是地理空間服務的組合，內含地圖、搜尋、路線規劃、路況和時區的服務 API。 此服務組合可讓您使用熟悉的工具，快速開發和調整解決方案，進而將位置資訊整合到 Azure 解決方案中。 Azure 地圖服務為來自各個產業的開發人員提供功能強大的地理空間功能，另納入重要的最新地圖資料，可提供網路和行動應用程式地理空間內容。 Azure 地圖服務是一組 REST API，可進行網頁型 JavaScript 控管並讓開發作業更加輕鬆、有彈性且可在多個媒介中轉移。 

以下影片將介紹 Azure 地圖服務：

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure 地圖服務包含五項主要服務，可支持需要地理內容的 Azure 應用程式。 以下將詳細說明每項服務。

**轉譯服務**是針對開發人員設計的服務，可建立以地圖服務為主的 Web 和行動裝置應用程式。 此服務會使用高品質點陣圖形影像 (提供 19 個縮放層級)，或可完全自訂的向量格式地圖影像。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

**路線規劃服務**包含強固的真實世界基礎結構幾何計算與多個傳輸模式方向。 服務可讓開發人員跨幾種旅行模式 (例如汽車、貨車、自行車或步行) 計算方向。 服務也可以考量輸入，例如交通情況、重量限制，或危險材料運輸。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

**搜尋服務**是針對開發人員而設計的服務，可搜尋地址、位置、依名稱或類別的商務清單，以及其他地理資訊。 搜尋服務也可以根據經緯度進行地址和交叉路口的[反向地理編碼](https://en.wikipedia.org/wiki/Reverse_geocoding)。 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

**時區服務**可讓您使用其中一組經緯度或 [IANA 識別碼](http://www.iana.org/)，查詢目前、過去及未來的時區資訊。 時區服務也可將 Microsoft Windows 時區識別碼轉換為 IANA 時區、擷取 UTC 的時區位移，以及取得各自時區中的目前時間。 時區服務查詢的典型 JSON 回應如以下範例所示：

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

**交通服務**是針對開發人員設計的一套 Web 服務，可建立需要路況的 Web 和行動裝置應用程式。 服務可提供兩種資料類型：
* 交通流量 - 針對路網中的所有重要道路提供即時觀察速度和行進時間。 
* 交通事故 - 提供有關路網中交通阻塞和交通事故的精確檢視。

![Azure 地圖服務流量](media/about-azure-maps/Introduction_Traffic.png)

Azure 地圖是為了行動性而建置，可以增強跨平台應用程式的功能，因為此程式設計模型無從驗證並透過 REST API 支援 JSON 輸出。 此外，Azure 地圖服務使用簡單的程式設計模型提供方便的 JavaScript 地圖控制項，以便快速輕鬆開發 Web 和行動裝置應用程式。 

Azure 地圖服務使用以金鑰為基礎的驗證配置，所以存取服務時需瀏覽至 [Azure 入口網站](http://portal.azure.com)並建立 Azure 地圖服務帳戶。 您的帳戶隨附預先為您產生的兩個金鑰。 在 Azure 地圖服務的要求中使用任一個金鑰，開始將這些位置功能直接整合到您的應用程式中。

## <a name="unsupported-regions"></a>不支援的區域
部分國家/地區目前無法使用 Azure 地圖服務 API。 請查看您目前的 IP 位址，並確認 IP 位址的位置不在下列未受支援的國家/地區中：

* 阿根廷
* 中國
* 印度
* 摩洛哥
* 巴基斯坦
* 南韓

## <a name="next-steps"></a>後續步驟

您現在已概略了解 Azure 地圖服務。 下一步是試用示範服務的範例 App。

> [!div class="nextstepaction"]
> [啟動示範互動式搜尋地圖](quick-demo-map-app.md)
