---
title: 從 Google Maps 遷移 web 服務 |Microsoft Docs
description: 有關如何將 web 服務從 Google Maps 遷移至 Microsoft Azure Maps 的教學課程。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480017"
---
# <a name="migrate-web-service-from-google-maps"></a>從 Google Maps 遷移 web 服務

Azure 和 Google Maps 都會透過 REST web 服務提供空間 Api 的存取權。 這些平臺的 API 介面會執行類似的功能，但會使用不同的命名慣例和回應物件。

下表提供的 Azure 地圖服務服務 Api，可為列出的 Google Maps 服務 Api 提供類似的功能。

| Google Maps 服務 API | Azure 地圖服務服務 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Directions              | [路由](https://docs.microsoft.com/rest/api/maps/route)                               |
| 距離矩陣         | [路由矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| 地理編碼               | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 放置搜尋           | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 放置自動完成      | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 靜態對應              | [轉譯](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| 時區               | [時區](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Azure 地圖服務目前無法使用下列服務 Api：

- Elevation
- 地理位置
- 放置詳細資料和相片。 Azure 地圖服務搜尋 API 中可用的電話號碼和網站 URL。
- 對應 Url
- 道路–速度限制資料可透過 Azure 地圖服務中的 route 和反向地理編碼 Api 取得。
- 靜態街道視圖

Azure 地圖服務有一些可能需要注意的額外 REST web 服務：

- [空間作業](https://docs.microsoft.com/rest/api/maps/spatial)：將複雜的空間計算和作業（例如地理柵欄）卸載至服務。
- [流量](https://docs.microsoft.com/rest/api/maps/traffic)：存取即時流量流程和事件資料。

## <a name="geocoding-addresses"></a>地理編碼位址

地理編碼是將位址（例如 "1 Microsoft 向，Redmond，WA"）轉換成座標的程式（例如經度：-122.1298、緯度：47.64005）。 然後，通常會使用座標來定位地圖上的標記或置中地圖。

Azure 地圖服務提供地理編碼位址的數種方法：

- [**自由格式位址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一位址字串（例如 "1 Microsoft 的方式，REDMOND，WA"）並立即處理要求。 如果您需要快速地理編碼個別位址，建議使用此選項。
- [**結構化位址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定單一位址的部分，例如街道名稱、城市、國家/地區和郵遞區號，然後立即處理要求。 如果您需要快速地理編碼個別位址，而且已將資料剖析成個別的位址部分，則建議使用此選項。
- [**Batch address 地理編碼**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：建立一個要求，其中包含最多10000個位址，並在一段時間內處理它們。 所有位址都會在伺服器上以平行方式地理編碼，而完成後，就可以下載完整的結果集。 這是地理編碼大型資料集的建議做法。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 結合了 address 地理編碼與感點的搜尋。 此 API 採用自由格式的字串，可以是位址、地點、地標、感興趣的點或感興趣的分類，並立即處理要求。 如果應用程式可讓使用者從相同的文字方塊搜尋位址或感的點，則建議使用此 API。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立一個要求，其中包含最多10000個位址、位置、地標或感興趣的點，並在一段時間內處理。 所有資料都會在伺服器上平行處理，而完成後，就可以下載完整的結果集。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數 | 可比較的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 和 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-城市/城鎮<br/>`municipalitySubdivision` –鄰近、子/超級城市<br/>`countrySubdivision`-州或省<br/>`countrySecondarySubdivision`-國家/地區<br/>`countryTertiarySubdivision` 地區<br/>`countryCode`-兩個字母的國家/地區代碼 |
| `key`                       | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                  | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `region`                    | `countrySet`                       |

如需如何使用搜尋服務的範例，請參閱[這裡](how-to-search-for-address.md)。 請務必參閱搜尋檔的[最佳作法](how-to-use-best-practices-for-search.md)。

> [!TIP]
> 在 [自動完成] 模式中，您可以將 `&amp;typeahead=true` 新增至要求 URL，以使用自由格式的位址地理編碼和模糊搜尋 Api。 這會告訴伺服器輸入文字可能是部分的，並且會進入預測模式。

## <a name="reverse-geocode-a-coordinate"></a>反向地理編碼座標

反向地理編碼是將地理座標（例如經度：-122.1298、緯度：47.64005）轉換成其近似位址的程式（例如 "1 Microsoft 的方式，Redmond，WA"）。

Azure 地圖服務提供數個反向地理編碼方法：

- [**位址反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定單一地理座標以取得其近似位址，並立即處理要求。
- [**交叉街道反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定單一地理座標以取得附近的交叉街道資訊（例如，第1個 & main）並立即處理要求。
- [**Batch 位址反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：建立包含最多10000座標的要求，並在一段時間內處理它們。 所有資料都會在伺服器上平行處理，而完成後，就可以下載完整的結果集。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數   | 可比較的 Azure 地圖服務 API 參數   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                  | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

請務必參閱搜尋檔的[最佳作法](how-to-use-best-practices-for-search.md)。

Azure 地圖服務反向地理編碼 API 有一些在 Google Maps 中無法使用的額外功能，可能有助於在遷移應用程式時進行整合：

- 取出速度限制資料。
- 取出道路使用資訊：當地道路、arterial、有限的存取、斜坡等等。
- 座標落在街道的旁邊。

## <a name="search-for-points-of-interest"></a>搜尋景點

您可以使用其位置搜尋 API，在 Google Maps 中搜尋感興趣的資料。 此 API 提供三種不同的方式來搜尋感利率的點：

- **從文字尋找位置：** 根據您的姓名、位址或電話號碼，搜尋相關的點。
- **鄰近搜尋**：搜尋某個位置特定距離內的感興趣點。
- **文字搜尋：** 使用包含相關點和位置資訊的自由格式文字來搜尋位置。 例如，「紐約的比薩餅」或「位於主要 st 附近的餐廳」。

Azure 地圖服務提供數個搜尋 Api 來取得重點：

- [**POI 搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：依名稱搜尋感興趣的點。 例如，"星巴克"。
- [**POI 類別搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：依類別搜尋感興趣的點。 例如，「餐廳」。
- [**鄰近搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜尋某個位置特定距離內的感興趣點。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 結合了 address 地理編碼與感點的搜尋。 此 API 採用自由格式的字串，可以是位址、地點、地標、感興趣的點或感興趣的分類，並立即處理要求。 如果應用程式可讓使用者從相同的文字方塊搜尋位址或感的點，則建議使用此 API。
- [**在幾何內搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜尋指定幾何（多邊形）內的感興趣點。
- [**沿著路線搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜尋沿著指定路由路徑的感興趣點。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立一個要求，其中包含最多10000個位址、位置、地標或感興趣的點，並在一段時間內處理。 所有資料都會在伺服器上平行處理，而完成後，就可以下載完整的結果集。

目前 Azure 地圖服務沒有可比較的 API 給 Google Maps 文字搜尋 API。

> [!TIP]
> POI 搜尋、POI 類別搜尋和模糊搜尋 Api 可以在自動完成模式中使用，方法是將 `&amp;typeahead=true` 新增至要求 URL。 這會告訴伺服器輸入文字可能是部分的，並且會進入預測模式。

請務必參閱搜尋檔的[最佳作法](how-to-use-best-practices-for-search.md)。

### <a name="find-place-from-text"></a>從文字尋找位置

若要依名稱或位址搜尋感興趣的點，可以使用 Azure 地圖服務[POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和[模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)api。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數 | 可比較的 Azure 地圖服務 API 參數 |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `locationbias`            | `lat`、`lon` 及 `radius`<br/>`topLeft` 和 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>鄰近搜尋

您可以使用[鄰近的搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)API，在 Azure 地圖服務中抓取鄰近的感興趣點。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數 | 可比較的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `keyword`                   | `categorySet` 和 `brandSet`        |
| `language`                  | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `location`                  | `lat` 和 `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` 和 `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` 和 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` 參閱[支援的搜尋類別](supported-search-categories.md)檔。   |

## <a name="calculate-routes-and-directions"></a>計算路線和路線

Azure 地圖服務可以用來計算路線和方向。 Azure 地圖服務有許多與 Google Maps 路由服務相同的功能，例如：

- 抵達和出發時間。
- 以即時和預測為基礎的流量路由。
- 不同的運輸模式;駕駛、走、腳踏車。

> [!NOTE]
> Azure 地圖服務要求所有之導航點都必須是座標。 必須先地理編碼位址。

Azure 地圖服務路由服務會提供下列 Api 來計算路由：

- [**計算路線**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：計算路線並立即處理要求。 此 API 同時支援 GET 和 POST 要求。 當您指定大量的之導航點，或使用許多路由選項來確保 URL 要求不會太長，而造成問題時，建議您使用 POST 要求。
- [**批次路由**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：建立包含最多1000個路由要求的要求，並在一段時間內處理它們。 所有資料都會在伺服器上平行處理，而完成後，就可以下載完整的結果集。
- [**行動服務**](https://docs.microsoft.com/rest/api/maps/mobility)：使用公開傳輸來計算路線和路線。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數    | 可比較的 Azure 地圖服務 API 參數  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` –以格式 `"lat0,lon0:lat1,lon1…."` 的座標  |
| `key`                          | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                     | `language` –請參閱[支援的語言](supported-languages.md)檔。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* –這是地理編碼相關的功能。 使用 Azure 地圖服務地理編碼 API 時，請使用*countrySet*參數。  |
| `traffic_model`               | *N/A* –只能指定流量資料是否應與*流量*參數搭配使用。 |
| `transit_mode`                | 請參閱[行動服務檔](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | 請參閱[行動服務檔](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制系統。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 根據預設，Azure 地圖服務的路由 API 只會傳回摘要（距離和時間）和路由路徑的座標。 使用 `instructionsType` 參數來取出輪流指示。 `routeRepresentation` 參數可以用來篩選出摘要和路由路徑。

Azure 地圖服務的路由 API 有許多在 Google Maps 中無法使用的其他功能，可能有助於在遷移應用程式時進行整合：

- 支援路由類型：最短、最快速、trilling，以及最具燃料效益。
- 支援額外的旅遊模式：匯流排、motorcycle、計程車、卡車和 van。
- 支援150之導航點。
- 在單一要求中計算多個行進時間;歷史流量、即時流量、無流量。
- 避免額外的道路類型：共乘道路、unpaved 道路、已使用的道路。
- 請指定要避免的自訂區域。
- 限制路由可以遞增的提升許可權。
- 以引擎規格為基礎的路由。 根據其剩餘的燃料/費用和引擎規格，計算燃燒或電動車輛的路線。
- 商業車輛路線參數支援;車輛維度、權數、axels 數目和貨物類型。
- 指定最大車輛速度。

此外，Azure 地圖服務中的 route 服務也支援[計算可路由的範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)（也稱為等時線），它會產生一個多邊形，其中涵蓋的區域可以在指定的時間量內，或在燃料/收費量的任何方向，從原點開始。

## <a name="retrieve-a-map-image"></a>取出地圖影像

Azure 地圖服務提供 API，可用來呈現已重迭資料的靜態地圖影像。 Azure 地圖服務[地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)轉譯 api 相當於 Google Maps 中的靜態地圖 API。

> [!NOTE]
> Azure 地圖服務需要中心、所有標記和路徑位置都是「經度，緯度」格式的座標，而 Google Maps 則使用「緯度、經度」格式。 必須先地理編碼位址。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數 | 可比較的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` –指定為 URL 路徑的一部分。 目前僅支援 PNG。 |
| `key`                       | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                  | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `maptype`                   | `layer` 和 `style` –請參閱[支援的地圖樣式](supported-map-styles.md)檔。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* –這是地理編碼相關的功能。 使用 Azure 地圖服務地理編碼 API 時，請使用 `countrySet` 參數。  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` 和 `height` –大小上限為8192x8192。 |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure 地圖服務使用具有圖格的磚系統，其大小為 Google Maps 中所使用地圖底圖的兩倍。 因此，相較于 Google 地圖，Azure 地圖服務中的 [縮放層級] 值會顯示一個比 Azure 地圖服務更接近的縮放層級。 在您要遷移的要求中降低縮放層級，以補償此項。

如需詳細資訊，請參閱[地圖影像呈現 API](how-to-render-custom-data.md)的操作指南。

除了能夠產生靜態地圖影像之外，Azure 地圖服務轉譯服務也能讓您直接存取點陣（PNG）和向量格式的地圖底圖：

- [**地圖磚**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)：抓取基底地圖的點陣（PNG）和向量磚（道路、邊界、背景）。
- [**地圖影像磚**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)：取出航空和衛星影像磚。

> [!TIP]
> 許多 Google Maps 應用程式，在幾年前，從互動式地圖體驗切換到靜態地圖影像是節省成本的方法。 在 Azure 地圖服務中，使用 Web SDK 中的互動式地圖控制項時，通常會更符合成本效益，因為它會根據地圖底圖負載收費。 Azure 地圖服務中的地圖磚很大，而且通常只需要一些時間重新建立與靜態地圖相同的地圖視圖，而且瀏覽器會自動快取地圖底圖。 因此，互動式地圖控制項在重現靜態地圖視圖時，通常只會產生一小部分的交易。 移動流覽和縮放會載入更多磚，不過，如果需要，地圖控制項中有一些選項可停用此行為。 互動式地圖控制項也提供比靜態地圖服務更多的視覺效果選項。

### <a name="marker-url-parameter-format-comparison"></a>標記 URL 參數格式比較

**之前： Google Maps**

在中，您可以使用 URL 中的 `markers` 參數，將「Google 地圖」標記新增至靜態地圖影像。 `markers` 參數採用樣式，以及要在地圖上以該樣式呈現的位置清單，如下所示：

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

您可以將其他 `markers` 參數新增至具有不同樣式和位置集的 URL，以使用其他樣式。

標記位置的指定格式為「緯度、經度」。

Google Maps 中的標記樣式是以 `optionName:value`的格式加入，並以管道（\|）字元分隔多個樣式，例如 "optionName1： value1\|optionName2： value2"。 請注意，選項名稱和值會以冒號（:) 分隔。 下列樣式選項名稱可用來在 Google 地圖中用來為標記樣式：

- `color` –預設標記圖示的色彩。 可以是24位十六進位色彩（`0xrrggbb`）或下列其中一個值;`black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white`。
- `label` –要顯示在圖示頂端的單一大寫英數位元。
- `size`-標記的大小。 可以是 `tiny`、`mid` 或 `small`。

您也可以使用下列樣式選項名稱，在 Google Maps 中使用自訂圖示：

- `anchor` –指定如何將圖示影像對齊座標。 可以是圖元（x，y）值或下列其中一個值;`top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft`或 `bottomright`。
- `icon` –指向圖示影像的 URL。

例如，在 Google Maps 中，您可以使用下列 URL 參數，將紅色的中間大小標記新增至地圖（經度：-110、緯度：45）：

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps 標記](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**之後： Azure 地圖服務**

在中，您也可以藉由在 URL 中指定 `pins` 參數，將 Azure 地圖服務標記新增至靜態地圖影像。 如同 Google Maps，可以在這個參數中指定樣式和位置清單，而且可以多次指定 `pins` 參數，以支援不同樣式的標記。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

您可以將其他 `pins` 參數新增至具有不同樣式和位置集的 URL，以使用其他樣式。

至於 pin 位置，Azure 地圖服務需要座標採用「經度緯度」格式，而 Google Maps 則使用「緯度、經度」格式。 另請注意，在 Azure 地圖服務中，有一個空格，而不是逗號分隔經度和緯度。

`iconType` 值會指定要建立的 pin 類型，而且可以有下列值：

- `default` –預設的釘選圖示。
- `none` –不會顯示任何圖示，只會轉譯標籤。
- `custom` –指定要使用的自訂圖示。 指向圖示影像的 URL 可以在 pin 位置資訊後面新增至 `pins` 參數的結尾。
- `{udid}` –儲存在 Azure 地圖服務資料儲存平臺中圖示的唯一資料識別碼（UDID）。

Azure 地圖服務中的 Pin 樣式是以 `optionNameValue`的格式加入，其中包含以分隔號（\|）字元（如此 `iconType|optionName1Value1|optionName2Value2`）分隔的多個樣式。 請注意，選項名稱和值不會分隔。 下列樣式選項名稱可用來在 Azure 地圖服務中使用樣式標記：

- `al` –指定標記的不透明度（Alpha）。 可以是介於0和1之間的數位。
- `an` –指定 pin 錨點。 以 "x y" 格式指定的 X 和 y 圖元值。
- `co` –釘選的色彩。 必須是24位的十六進位色彩： `000000` `FFFFFF`。
- `la` –指定標籤錨點。 以 "x y" 格式指定的 X 和 y 圖元值。
- `lc` –標籤的色彩。 必須是 24-但十六進位色彩： `000000` `FFFFFF`。
- `ls` –標籤的大小（以圖元為單位）。 可以是大於0的數位。
- `ro` –旋轉圖示的角度值。 可以是介於-360 和360之間的數位。
- `sc` –釘選圖示的小數值。 可以是大於0的數位。

系統會為每個 pin 位置指定標籤值，而不會有單一標籤值套用至位置清單中的所有標記。 標籤值可以是多個字元的字串，並以單引號括住，以確保它不會被誤認為為樣式或位置值。

例如，在 Azure 地圖服務中，新增紅色（`FF0000`）預設圖示，並將標籤「空間指標」置於下列位置（15 50）。您可以使用下列 URL 參數來完成座標（經度：-122.349300、緯度：47.620180）的圖示：

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure 地圖服務標記](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

下列範例會加入具有標籤值 ' 1 '、' 2 ' 和 ' 3 ' 的三個圖釘：

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure 地圖服務多個標記](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>路徑 URL 參數格式比較

**之前： Google Maps**

在中，您可以使用 URL 中的 `path` 參數，將 Google Maps 中的線條和多邊形加入至靜態地圖影像。 `path` 參數採用樣式，以及要在地圖上以該樣式呈現的位置清單，如下所示：

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

您可以將其他 `path` 參數新增至具有不同樣式和位置集的 URL，以使用其他樣式。

Google Maps 中的路徑位置會以 `latitude1,longitude1|latitude2,longitude2|…`的格式來指定。 路徑可以編碼或包含點的位址。

Google Maps 中的路徑樣式是以 `optionName:value`的格式加入，其中包含以分隔號（\|）字元分隔的多個樣式，例如這個 `optionName1:value1|optionName2:value2`。 請注意，選項名稱和值會以冒號（:) 分隔。 下列樣式選項名稱可用於在 Google Maps 中的路徑樣式：

- `color` –路徑或多邊形外框的色彩。 可以是24位十六進位色彩（`0xrrggbb`）、32位十六進位色彩（`0xrrggbbbaa`）或下列其中一個值;黑色、棕色、綠色、紫色、黃色、藍色、灰色、橙色、紅色、白色。
- `fillColor` –用來填滿路徑區域的色彩（多邊形）。 可以是24位十六進位色彩（`0xrrggbb`）、32位十六進位色彩（`0xrrggbbbaa`）或下列其中一個值;黑色、棕色、綠色、紫色、黃色、藍色、灰色、橙色、紅色、白色。
- `geodesic` –指出路徑是否應為地球彎曲之後的一行。
- `weight` –路徑行的粗細（以圖元為單位）。

例如，在 Google Maps 中，具有50% 不透明度和寬度4圖元的紅線可以加入座標（經度：-110，緯度：45和經度：-100，緯度：50）之間的地圖，並使用下列 URL 參數：

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps 的聚合線條](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**之後： Azure 地圖服務**

在中，您也可以藉由在 URL 中指定 `path` 參數，將 Azure 地圖服務行和多邊形新增至靜態地圖影像。 如同 Google Maps，可以在這個參數中指定樣式和位置清單，而且可以多次指定 `path` 參數，以使用不同的樣式來呈現多個圓形、線條和多邊形。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

當到達路徑位置時，Azure 地圖服務要求座標必須採用「經度緯度」格式，而 Google Maps 則使用「緯度、經度」格式。 另請注意，在 Azure 地圖服務中，有一個空格，而不是逗號分隔經度和緯度。 Azure 地圖服務不支援點的編碼路徑或位址。 較大的資料集可隨 GeoJSON 填滿到 Azure 地圖服務的資料儲存體 API，如[這裡](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)所述。

Azure 地圖服務中的路徑樣式是以 `optionNameValue`的格式加入，其中包含以分隔號（\|）字元（如此 `optionName1Value1|optionName2Value2`）分隔的多個樣式。 請注意，選項名稱和值不會分隔。 下列樣式選項名稱可用於 Azure 地圖服務中的路徑樣式：

- `fa`-呈現多邊形時使用的填滿色彩不透明度（Alpha）。 可以是介於0和1之間的數位。
- `fc`-用來呈現多邊形區域的填滿色彩。
- `la` –呈現線條和多邊形外框時所使用的線條色彩不透明度（Alpha）。 可以是介於0和1之間的數位。
- `lc` –用來呈現線條和多邊形外框的線條色彩。
- `lw` –線條的寬度（以圖元為單位）。
- `ra` –指定圓圈半徑，以量為單位。

例如，在 Azure 地圖服務中，具有50% 不透明度和寬度4圖元的紅線可以加入座標（經度：-110、緯度：45和經度：-100、緯度：50）之間的對應，並具有下列 URL 參數：

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure 地圖服務的折線](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>計算距離矩陣

Azure 地圖服務提供 API 來計算一組位置與距離矩陣之間的行進時間和距離。 「Azure 地圖服務距離矩陣」 API 可與 Google Maps 中的距離矩陣 API 比較;

- [**路由矩陣**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以非同步方式計算一組來源和目的地的旅遊時間和距離。 支援每個要求最多700個數據格（來源數目乘以目的地數目）。 在記住該條件約束的情況下，可能的矩陣維度範例包括：700x1、50x10、10x10、28x25、10x70。

> [!NOTE]
> 距離矩陣 API 的要求只能使用 POST 要求，並在要求主體中加上來源和目的地資訊。 此外，Azure 地圖服務要求所有的來源和目的地都必須是座標。 必須先地理編碼位址。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數      | 可比較的 Azure 地圖服務 API 參數  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` –在 POST 要求主體中指定為 GeoJSON。 |
| `key`                          | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。 |
| `language`                     | `language` –請參閱[支援的語言](supported-languages.md)檔。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` –在 POST 要求主體中指定為 GeoJSON。  |
| `region`                       | *N/A* –這是地理編碼相關的功能。 使用 Azure 地圖服務地理編碼 API 時，請使用 `countrySet` 參數。 |
| `traffic_model`                | *N/A* –只能指定流量資料是否應與 `traffic` 參數搭配使用。 |
| `transit_mode`                 | 目前不支援*以*傳輸為基礎的距離矩陣。  |
| `transit_routing_preference`   | 目前不支援*以*傳輸為基礎的距離矩陣。  |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制系統。 |

> [!TIP]
> 在 Azure 地圖服務距離矩陣 API 中，Azure 地圖服務路由 API （卡車路線規劃，引擎規格，避免 ...）中可用的所有 advanced routing 選項都是支援的。

## <a name="get-a-time-zone"></a>取得時區

Azure 地圖服務提供 API 來抓取座標所在的時區。 Azure 地圖服務時區 API 相當於 Google Maps 中的時區 API：

- [**時區（依座標**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)）：指定座標，並取得其所在時區的詳細資料。

下表會在 Azure 地圖服務中，交互參考 Google Maps API 參數與可比較的 API 參數。

| Google Maps API 參數 | 可比較的 Azure 地圖服務 API 參數   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` –另請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。       |
| `language`                  | `language` –請參閱[支援的語言](supported-languages.md)檔。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

此外，Azure 地圖服務平臺也會提供一些額外的時區 Api，以協助使用時區名稱和識別碼進行轉換：

- [**依識別碼的時區**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)：針對指定的 IANA 時區識別碼，傳回目前、歷程記錄和未來的時區資訊。
- [**時區列舉 iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：傳回 Iana 時區識別碼的完整清單。 IANA 服務的更新會在一天內反映在系統中。
- [**時區列舉視窗**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：傳回 Windows 時區識別碼的完整清單。
- [**時區 IANA 版本**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：傳回 Azure 地圖服務使用的目前 IANA 版本號碼。
- [**將時區時段設為 iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：傳回對應的 IANA 識別碼，並指定有效的 WINDOWS 時區識別碼。 針對單一 Windows 識別碼，可能會傳回多個 IANA 識別碼。

## <a name="client-libraries"></a>用戶端程式庫

Azure 地圖服務提供下列程式設計語言的用戶端程式庫：

- JavaScript、TypeScript、node.js-[檔](how-to-use-services-module.md)\| [NPM 套件](https://www.npmjs.com/package/azure-maps-rest)

適用于其他程式設計語言的開放原始碼用戶端程式庫：

- .NET Standard 2.0 – [GitHub 專案](https://github.com/perfahlen/AzureMapsRestServices)\| [NuGet 套件](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>其他資源

以下是 Azure 地圖服務 REST 服務上的一些額外檔和資源。

- [搜尋的最佳做法](how-to-use-best-practices-for-search.md)
- [搜尋位址](how-to-search-for-address.md)
- [Azure 地圖服務 REST 服務 API 參考檔](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 地圖服務 REST 服務。

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳作法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服務模組（Web SDK）](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)