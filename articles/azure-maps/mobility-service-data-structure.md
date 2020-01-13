---
title: Azure 地圖服務中的行動服務資料結構 |Microsoft Azure 對應
description: 在本文中，您將瞭解透過 Microsoft Azure Maps 行動服務所傳回的通用欄位和資料結構。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910729"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure 地圖服務行動服務中的資料結構

這篇文章介紹在[Azure 地圖服務行動服務](https://aka.ms/AzureMapsMobilityService)中 Metro 區域的概念，以及透過服務所傳回的一些常見欄位（當查詢公用傳輸停止和行時）。 我們建議您先閱讀這篇文章，再開始使用行動服務 Api。 我們將在下面討論這些常見的欄位。

## <a name="metro-area"></a>Metro 區域

行動服務資料會分割成支援的 metro 區域。 Metro 區域不會遵循城市界限，metro 區域可以包含多個城市，例如，密集已填入城市和其周圍城市;而國家/地區則可以是一個 metro 區域。 

`metroID` 是 metro 區域的識別碼，可用來呼叫[取得 Metro 區域資訊 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)來要求支援的傳輸類型，以及 metro 區域的其他詳細資料，例如運輸機構和作用中警示。 您可以使用 Azure 地圖服務取得 Metro API 來要求支援的 metro 區域和 metroIDs。 Metro 區域識別碼可能會變更。

**metroID：** 522**名稱：** 西雅圖-Tacoma-Bellevue

![西雅圖-metro-區域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止識別碼

傳輸停止可以透過兩種類型的識別碼來參考：[一般傳輸摘要規格（GFTS）](https://gtfs.org/)識別碼（稱為 stopKey）和 AZURE 地圖服務停止識別碼（稱為 stopId）。 當參照停止一段時間時，建議使用 Azure 地圖服務停止識別碼，因為此識別碼較穩定，而且只要實體停止存在，就不會變更。 GTFS 停止識別碼會更頻繁地更新，例如，如果 GTFS 提供者需要變更它或發行新的 GTFS 版本，雖然實體停止沒有任何變更。

若要開始，您可以使用「[取得鄰近的傳輸 API](https://aka.ms/AzureMapsMobilityNearbyTransit)」來要求附近的傳輸停止。

## <a name="line-groups-and-lines"></a>行群組和線條

行動服務會針對線條和行群組使用平行資料模型，以便更有效地處理繼承自[GTFS](https://gtfs.org/)路由和行程資料模型的變更。


### <a name="line-groups"></a>行群組

「線條群組」是一個實體，它會將邏輯上屬於相同群組的所有線條群組在一起。 通常，線條群組會包含兩行，一個從點 A 到 B，另一個從 B 到 A 的傳回，兩者都屬於相同的公用傳輸代理程式，且具有相同的行號。 不過，在某些情況下，線條群組可能會有兩行以上，或其中只有一行。


### <a name="lines"></a>線條

如上所述，每個線條群組都是由一組行所組成。 每一行通常都會描述方向，而每個線條群組都是由兩行組成。 不過，在某些情況下，會有更多行組成行群組，例如，有時會繞道到特定的鄰近地區，有時不會，而是在相同行號下的兩個案例中運作，而在其他情況下，則會有行 g分組是由單一行組成，例如具有單一方向的圓形。

若要開始，您可以使用「[取得運輸線」 API](https://aka.ms/AzureMapsMobilityTransitLine)來要求「行群組」，並于稍後向下切入至行。


## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

瞭解如何使用行動服務來要求即時資料：

> [!div class="nextstepaction"]
> [如何要求即時資料](how-to-request-real-time-data.md)

探索 Azure 地圖服務行動服務 API 檔

> [!div class="nextstepaction"]
> [行動服務 API 檔](https://aka.ms/AzureMapsMobilityService)
