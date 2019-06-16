---
title: Azure 地圖服務中的行動服務資料結構 |Microsoft Docs
description: Azure Maps 行動服務中的資料結構
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735552"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 行動服務中的資料結構

這篇文章介紹 Metro 區域中的概念[Azure Maps 行動服務](https://aka.ms/AzureMapsMobilityService)一些共通的欄位傳回並透過服務中，被查詢時公開的傳輸會停止程式行。 我們建議您閱讀本文之前先開始使用行動服務 Api。 我們會討論這些共通的欄位下方。

## <a name="metro-area"></a>Metro 區域

行動服務的資料會分割成支援輕軌。 輕軌未遵循縣 （市） 界限，metro 區域可以包含多個城市，例如密集填入的縣 （市） 和其周圍的城市;和國家/地區可以是一個 metro 的區域。 

`metroID` Metro 區域的識別碼，可以用來呼叫[取得 Metro 區域資訊 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)要求支援傳輸類型和 metro 的領域，例如傳輸機構和作用中警示的其他詳細資料。 您可以使用 Azure 地圖服務取得 Metro API 來要求支援的輕軌和 metroIDs。 Metro 區域識別碼會有所變更。

**metroID:** 522**名稱：** Seattle-Tacoma-Bellevue

![Seattle metro 區域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止識別碼

傳輸停駐點可以兩種類型的識別碼，由參考[一般傳輸摘要規格 (GFTS)](https://gtfs.org/)識別碼 （又稱為 stopKey） 和 Azure 地圖服務會停止 （又稱為 stopId） 的識別碼。 當參考停駐點，經過一段時間，建議您若要使用 Azure 地圖服務停止識別碼，為此識別碼是更穩定且不會有可能變更，只要存在的實體的停駐點。 GTFS 停止識別碼會經常更新，比方說，萬一 GTFS 提供者必須加以變更或新 GTFS 版本發行時，雖然實體停止已沒有變更。

若要開始，您可以要求附近的傳輸會停止使用[取得附近的傳輸 API](https://aka.ms/AzureMapsMobilityNearbyTransit)。

## <a name="line-groups-and-lines"></a>折線圖和線條

行動服務會平行處理資料模型使用的行和列群組，以進一步處理的變更繼承自[GTFS](https://gtfs.org/)路由和車程資料模型。


### <a name="line-groups"></a>折線圖

折線圖群組是實體中，以分組在一起在邏輯上是相同群組的一部分的所有行。 通常是折線圖群組將包含兩行，一個會從 A 到 B，以及其他從 B 點傳回至 A，這兩個屬於相同的大眾運輸機構和具有相同的行號。 不過，可能會發生列群組有兩個以上的線條或只有單一行內的情況。


### <a name="lines"></a>程式行

如前所述，每一列群組被組成一組線條。 通常每一行描述方向和每一列群組的組成兩行。 不過有的情況下在哪一個多行組成列群組，例如有一條線有時繞道透過特定網路上的芳鄰，有時則否，而且在這兩種情況下相同的行號，還有其他的情況下，在其中一條線 g群組及全球被組成某一行，例如弧線要以單一方向。

若要開始，您可以使用要求折線圖[取得傳輸列 API](https://aka.ms/AzureMapsMobilityTransitLine)和更新版本的向下切入至行。


## <a name="next-steps"></a>後續步驟

了解如何要求使用行動服務的傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

了解如何使用行動服務的即時資料的要求：

> [!div class="nextstepaction"]
> [如何要求即時資料](how-to-request-real-time-data.md)

探索 Azure Maps 行動服務 API 文件

> [!div class="nextstepaction"]
> [行動服務 API 文件](https://aka.ms/AzureMapsMobilityService)
