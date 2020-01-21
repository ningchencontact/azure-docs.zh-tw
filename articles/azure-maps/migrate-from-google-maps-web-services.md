---
title: 教學課程：從 Google Maps 遷移 Web 服務 | Microsoft Azure 地圖服務
description: 如何將 Web 服務從 Google Maps 遷移至 Microsoft Azure 地圖服務。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910748"
---
# <a name="migrate-web-service-from-google-maps"></a>從 Google Maps 遷移 Web 服務

Azure 地圖服務和 Google Maps 都提供透過 REST Web 服務來存取空間 API 的功能。 這些平台的 API 介面所執行的功能很類似，但所使用的命名慣例和回應物件則不同。

下表所提供 Azure 地圖服務的服務 API，會提供與所列 Google Maps 服務 API 類似的功能。

| Google Maps 服務 API | Azure 地圖服務的服務 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 方向              | [路由](https://docs.microsoft.com/rest/api/maps/route)                               |
| 距離矩陣         | [路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| 地理編碼               | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 地點搜尋           | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 地點自動完成      | [搜尋](https://docs.microsoft.com/rest/api/maps/search)                             |
| 靜態地圖              | [轉譯](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| 時區               | [時區](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Azure 地圖服務目前無法使用下列服務 API：

- Elevation
- 地理位置
- 地點的詳細資料和相片。 電話號碼和網站 URL 則可於 Azure 地圖服務的搜尋 API 中取得。
- 地圖 URL
- 道路 – 速限資料可透過 Azure 地圖服務中的路線和反向地理編碼 API 來取得。
- 靜態街道檢視

Azure 地圖服務有一些可能讓您感興趣的額外 REST Web 服務：

- [空間作業](https://docs.microsoft.com/rest/api/maps/spatial)：將複雜的空間計算和作業 (例如地理柵欄) 卸載至某個服務。
- [交通](https://docs.microsoft.com/rest/api/maps/traffic)：存取即時交通流量和事件資料。

## <a name="geocoding-addresses"></a>地理編碼地址

地理編碼程序可將地址 (例如 "1 Microsoft way, Redmond, WA") 轉換成座標 (例如「經度：-122.1298、緯度：47.64005」)。 然後，座標通常會用來在地圖上定位標記或置中地圖。

Azure 地圖服務提供數種用來地理編碼地址的方法：

- [**自由格式的地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定單一地址字串 (例如 "1 Microsoft way, Redmond, WA") 並立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，建議使用此方法。
- [**結構化的地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定單一地址的各個部分 (例如，街道名稱、城市、國家/地區和郵遞區號)，然後立即處理要求。 如果您需要快速地進行個別地址的地理編碼程序，而且已將資料剖析成個別的地址部分，則建議使用此方法。
- [**批次地址地理編碼**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：建立最多包含 10,000 個地址的要求，並讓這些地址進行一段時間的處理。 所有地址會在伺服器上以平行方式進行地理編碼，並可於完成後下載完整的結果集。 若要對大型資料集進行地理編碼，則建議使用此做法。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 和 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 市/鎮<br/>`municipalitySubdivision` – 鄰近地區、副城市/超級城市<br/>`countrySubdivision` - 州或省<br/>`countrySecondarySubdivision` - 郡/縣<br/>`countryTertiarySubdivision` - 行政區<br/>`countryCode` - 兩個字母的國家/地區代碼 |
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `region`                    | `countrySet`                       |

如需如何使用搜尋服務的範例，請參閱[這裡](how-to-search-for-address.md)。 請務必檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)文件。

> [!TIP]
> 在自動完成模式中，您可以將 `&amp;typeahead=true` 新增至要求 URL，以使用自由格式的地址地理編碼和模糊搜尋 API。 這會告訴伺服器輸入文字可能僅有部分，並因此進入預測模式。

## <a name="reverse-geocode-a-coordinate"></a>對座標進行反向地理編碼

反向地理編碼程序可將地理座標 (例如「經度：-122.1298、緯度：47.64005) 轉換成大致的地址 (例如 "1 Microsoft way, Redmond, WA")。

Azure 地圖服務提供數種反向地理編碼方法：

- [**地址反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定單一地理座標以取得其大致地址，並立即處理要求。
- [**十字路口反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定單一地理座標以取得附近的十字路口資訊 (例如，第 1 街和主街)，並立即處理要求。
- [**批次地址反向地理編碼器**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：建立最多包含 10,000 個座標的要求，並讓這些地址進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數   | 類似的 Azure 地圖服務 API 參數   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

請務必檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)文件。

Azure 地圖服務反向地理編碼 API 會有一些 Google Maps 並未提供的額外功能，可能有助於遷移應用程式時的整合：

- 擷取速限資料。
- 擷取道路使用資訊：支線、幹線、限制進入、斜坡等等。
- 座標落在街道哪一側。

## <a name="search-for-points-of-interest"></a>搜尋景點

您可以使用地點搜尋 API，在 Google Maps 中搜尋景點資料。 此 API 提供了三種不同的方式供您搜尋景點：

- **從文字尋找地點：** 根據景點名稱、地址或電話號碼來搜尋景點。
- **鄰近搜尋**：搜尋位於某位置一定距離內的景點。
- **文字搜尋：** 使用包含景點和位置資訊的自由格式文字來搜尋地點。 例如，「紐約的披薩」或「主街附近的餐廳」。

Azure 地圖服務會提供數個景點搜尋 API：

- [**POI 搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：依名稱搜尋景點。 例如，「星巴克」。
- [**POI 類別搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：依類別搜尋景點。 例如，「餐廳」。
- [**鄰近搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜尋位於某位置一定距離內的景點。
- [**模糊搜尋**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 會結合地址地理編碼與景點搜尋。 此 API 會採用自由格式的字串 (內容可以是地址、地點、地標、景點或景點類別)，並立即處理要求。 如果應用程式可供使用者從相同文字方塊中搜尋地址或景點，則建議使用此 API。
- [**在幾何範圍內搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜尋指定幾何範圍 (多邊形) 內的景點。
- [**沿路線搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜尋指定路線路徑沿途的景點。
- [**模糊批次搜尋**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：建立最多包含 10,000 個地址、地點、地標或景點的要求，並讓這些項目進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。

Azure 地圖服務目前沒有與 Google Maps 文字搜尋 API 類似的 API。

> [!TIP]
> POI 搜尋、POI 類別搜尋和模糊搜尋 API 可在自動完成模式中使用，方法是將 `&amp;typeahead=true` 新增至要求 URL。 這會告訴伺服器輸入文字可能僅有部分，並因此進入預測模式。

請務必檢閱[搜尋的最佳做法](how-to-use-best-practices-for-search.md)文件。

### <a name="find-place-from-text"></a>從文字尋找地點

若要依名稱或地址搜尋景點，您可以使用 Azure 地圖服務的 [POI 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和[模糊搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數 |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `locationbias`            | `lat`、`lon` 及 `radius`<br/>`topLeft` 和 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>鄰近搜尋

您可以使用[鄰近搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API，在 Azure 地圖服務中擷取鄰近的景點。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `keyword`                   | `categorySet` 和 `brandSet`        |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `location`                  | `lat` 和 `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` 和 `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` 和 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` 請參閱[支援的搜尋類別](supported-search-categories.md)文件。   |

## <a name="calculate-routes-and-directions"></a>計算路線和方線

Azure 地圖服務可用來計算路線和方向。 Azure 地圖服務有許多與 Google Maps 路線服務相同的功能，例如：

- 抵達時間和出發時間。
- 即時型和預測型交通路線。
- 不同運輸模式；開車、走路、騎腳踏車。

> [!NOTE]
> Azure 地圖服務要求所有導航點都必須是座標。 地址必須先進行地理編碼。

Azure 地圖服務路線服務會提供下列 API 來計算路線：

- [**計算路線**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：計算路線並立即處理要求。 此 API 同時支援 GET 和 POST 要求。 當您指定大量導航點，或使用許多路線選項來確保 URL 要求不會太長而造成問題時，建議您使用 POST 要求。
- [**批次路線**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：建立最多包含 1,000 個路線要求的要求，並讓這些地址進行一段時間的處理。 所有資料會在伺服器上以平行方式處理，並可於完成後下載完整的結果集。
- [**行動服務**](https://docs.microsoft.com/rest/api/maps/mobility)：使用大眾運輸系統來計算路線和方向。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數    | 類似的 Azure 地圖服務 API 參數  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – 格式為 `"lat0,lon0:lat1,lon1…."` 的座標  |
| `key`                          | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                     | `language` – 請參閱[支援的語言](supported-languages.md)文件。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – 這是與地理編碼有關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 countrySet  參數。  |
| `traffic_model`               | *N/A* – 只能指定是否應該讓交通資料與 traffic  參數搭配使用。 |
| `transit_mode`                | 請參閱[行動服務文件](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | 請參閱[行動服務文件](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制單位。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 根據預設，Azure 地圖服務的路線 API 只會傳回路線路徑的摘要 (距離和時間) 和座標。 使用 `instructionsType` 參數可擷取轉向建議指示。 `routeRepresentation` 參數可用來篩選出摘要和路線路徑。

Azure 地圖服務路線 API 會有許多 Google Maps 並未提供的額外功能，可能有助於遷移應用程式時的整合：

- 支援路線類型：最短、最快、trilling，以及最省油。
- 支援額外的行進模式：巴士、摩托車、計程車、卡車和貨車。
- 支援 150 個導航點。
- 在單一要求中計算多個行進時間；過往交通情況、即時交通情況、無交通情況。
- 避免額外的道路類型：共乘道路、沒有鋪柏油的道路、已使用的道路。
- 指定要避開的自訂區域。
- 限制路線的最高上升海拔。
- 以引擎規格為基礎的路線。 根據剩餘的燃料/電量和引擎規格，計算油車或電動車的路線。
- 支援商用車路線參數；車輛尺寸、重量、輪軸數和貨物類型。
- 指定最大車速。

除此之外，Azure 地圖服務中的路線服務還支援[計算可安排路線的範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange) (也稱為等時線)，其會產生一個多邊形，範圍涵蓋可在指定時間長度內或油量/電量內，從起點以任何方向行進到的區域。

## <a name="retrieve-a-map-image"></a>擷取地圖影像

Azure 地圖服務會提供 API 來呈現已覆蓋資料的靜態地圖影像。 Azure 地圖服務的[地圖影像呈現](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API 相當於 Google Maps 中的靜態地圖 API。

> [!NOTE]
> Azure 地圖服務會要求中心點、所有標記和路徑位置都是採用「經度,緯度」格式的座標，而 Google Maps 則使用「緯度,經度」格式。 地址必須先進行地理編碼。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – 指定為 URL 路徑的一部分。 目前僅支援 PNG。 |
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `maptype`                   | `layer` 和 `style` – 請參閱[支援的地圖樣式](supported-map-styles.md)文件。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – 這是與地理編碼有關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 `countrySet` 參數。  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` 和 `height` – 最高可達 8192x8192 大小。 |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure 地圖服務所使用的地圖底圖系統，其地圖底圖大小是 Google Maps 所用地圖底圖的兩倍。 因此，Azure 地圖服務中的縮放層級值會比 Google Maps 中的更近一個縮放層級。 請在您要遷移的要求中降低縮放層級，以補償這一差距。

如需詳細資訊，請參閱[關於地圖影像呈現 API 的操作指南](how-to-render-custom-data.md)。

除了能夠產生靜態地圖影像之外，Azure 地圖服務的呈現服務也能讓您直接存取點陣 (PNG) 和向量格式的地圖底圖：

- [**地圖底圖**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)：擷取基底地圖 (道路、邊界、背景) 的點陣 (PNG) 和向量地圖底圖。
- [**地圖影像地圖底圖**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)：擷取空照圖影像和衛星影像的地圖底圖。

> [!TIP]
> 幾年前為了節省成本，有許多 Google Maps 應用程式從互動式地圖體驗切換成靜態地圖影像。 Azure 地圖服務會根據地圖底圖的載入量來收費，因此在 Web SDK 中使用互動式地圖控制項時，往往會更符合成本效益。 Azure 地圖服務中的地圖底圖很大，往往只需要一會兒就能重新建立與靜態地圖相同的地圖檢視，而且瀏覽器會自動快取地圖底圖。 因此，互動式地圖控制項在重現靜態地圖檢視時，往往只會產生一小部分的交易。 平移和縮放會載入更多地圖底圖，不過如有需要，地圖控制項中有選項可供停用此行為。 互動式地圖控制項也會提供比靜態地圖服務多更多的視覺效果選項。

### <a name="marker-url-parameter-format-comparison"></a>標記 URL 參數格式比較

**之前：Google Maps**

在 Google Maps 中，您可以在 URL 中使用 `markers` 參數將標記新增至靜態地圖影像。 `markers` 參數會取得樣式，以及要在地圖上以該樣式呈現的位置清單，如下所示：

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

您可以將其他 `markers` 參數新增至具有不同樣式和一組位置的 URL，從而使用其他樣式。

標記位置的指定格式為「緯度,經度」。

Google Maps 中的標記樣式會以 `optionName:value` 格式來新增，如有多個樣式，則以縱線字元 (\|) 來分隔，例如 "optionName1:value1\|optionName2:value2"。 請注意，選項名稱和值則以冒號 (:) 分隔。 在 Google Maps 中，下列樣式選項名稱可用來設定標記樣式：

- `color` – 預設標記圖示的色彩。 可以是 24 位元的十六進位色彩 (`0xrrggbb`) 或下列其中一個值；`black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white`。
- `label` – 要顯示在圖示頂端的單一大寫英數位元。
- `size` - 標記的大小。 可以是 `tiny`、`mid` 或 `small`。

在 Google Maps 中，您也可以使用下列樣式選項名稱來使用自訂圖示：

- `anchor` – 指定如何將圖示影像對齊座標。 可以是像素 (x,y) 值或下列其中一個值；`top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft` 或 `bottomright`。
- `icon` – 指向圖示影像的 URL。

例如，在 Google Maps 中，您可以使用下列 URL 參數將紅色的中型標記新增地圖上的座標 (經度：-110、緯度：45)：

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps 標記](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您也可以藉由在 URL 中指定 `pins` 參數，將標記新增至靜態地圖影像。 如同 Google Maps，您也可以在這個參數中指定樣式和位置清單，而且可以多次指定 `pins` 參數以支援具有不同樣式的標記。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

您可以將其他 `pins` 參數新增至具有不同樣式和一組位置的 URL，從而使用其他樣式。

關於圖釘位置，Azure 地圖服務會要求座標採用「經度 緯度」格式，而 Google Maps 則使用「緯度,經度」格式。 另請注意，在 Azure 地圖服務中，經度和緯度會用空格而不是逗號來分隔。

`iconType` 值會指定所要建立的圖釘類型，並可具有下列值：

- `default` – 預設的圖釘圖示。
- `none` – 不會顯示任何圖示，只會呈現標籤。
- `custom` - 指定要使用自訂圖示。 指向圖示影像的 URL 可在圖釘位置資訊後面新增至 `pins` 參數的結尾。
- `{udid}` – 儲存在 Azure 地圖服務資料儲存體平台中的圖示所具有的唯一資料識別碼 (UDID)。

Azure 地圖服務中的圖釘樣式會以 `optionNameValue` 格式來新增，如有多個樣式，則以縱線字元 (\|) 來分隔，例如 `iconType|optionName1Value1|optionName2Value2`。 請注意，選項名稱和值不會分隔。 在 Azure 地圖服務中，下列樣式選項名稱可用來設定標記樣式：

- `al` – 指定標記的不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
- `an` – 指定圖釘錨點。 以 "x y" 格式指定的 x 和 y 像素值。
- `co` – 圖釘的色彩。 必須是 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
- `la` – 指定標籤錨點。 以 "x y" 格式指定的 x 和 y 像素值。
- `lc` – 標籤的色彩。 必須是 24 位元的十六進位色彩：`000000` 到 `FFFFFF`。
- `ls` – 標籤的大小 (以像素為單位)。 可以是大於 0 的數字。
- `ro` – 用來旋轉圖示的值 (以度為單位)。 可以是 -360 到 360 之間的數字。
- `sc` – 圖釘圖示的縮放值。 可以是大於 0 的數字。

系統會為每個圖釘位置都指定標籤值，而不是將單一標籤值套用至位置清單中的所有標記。 標籤值可以是有多個字元的字串，並以單引號括住，以確保系統不會將其誤認為樣式或位置值。

例如，在 Azure 地圖服務中，若要新增紅色 (`FF0000`) 預設圖示，其標籤為「太空針塔」，位於 (15 50) 下面，則座標 (經度：-122.349300、緯度：47.620180) 處的圖示可使用下列 URL 參數來實現：

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure 地圖服務的標記](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

下列範例會使用標籤值 '1'、'2' 和 '3' 來新增三個圖釘：

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure 地圖服務的多個標記](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>路徑 URL 參數格式比較

**之前：Google Maps**

在 Google Maps 中，您可以在 URL 中使用 `path` 參數將線條和多邊形新增至靜態地圖影像。 `path` 參數會取得樣式，以及要在地圖上以該樣式呈現的位置清單，如下所示：

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

您可以將其他 `path` 參數新增至具有不同樣式和一組位置的 URL，從而使用其他樣式。

Google Maps 中的路徑位置會以 `latitude1,longitude1|latitude2,longitude2|…` 格式來指定。 路徑可加以編碼，或包含位置點的地址。

Google Maps 中的路徑樣式會以 `optionName:value` 格式來新增，如有多個樣式，則以縱線字元 (\|) 來分隔，例如 `optionName1:value1|optionName2:value2`。 請注意，選項名稱和值則以冒號 (:) 分隔。 在 Google Maps 中，下列樣式選項名稱可用來設定路徑樣式：

- `color` – 路徑或多邊形外框的色彩。 可以是 24 位元的十六進位色彩 (`0xrrggbb`)、32 位元的十六進位色彩 (`0xrrggbbbaa`) 或下列其中一個值；黑色、棕色、綠色、紫色、黃色、藍色、灰色、橘色、紅色、白色。
- `fillColor` – 用來填滿路徑區域的色彩 (多邊形)。 可以是 24 位元的十六進位色彩 (`0xrrggbb`)、32 位元的十六進位色彩 (`0xrrggbbbaa`) 或下列其中一個值；黑色、棕色、綠色、紫色、黃色、藍色、灰色、橘色、紅色、白色。
- `geodesic` – 指出路徑是否應該是遵循地球曲率的線條。
- `weight` – 路徑線條的粗細 (以像素為單位)。

例如，在 Google Maps 中，不透明度為 50% 且粗細為 4 個像素的紅色線條可使用下列 URL 參數新增至地圖上的座標 (經度：-110, 緯度：45) 和 (經度：-100, 緯度：50) 之間：

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps 的聚合線條](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**之後：Azure 地圖服務**

在 Azure 地圖服務中，您也可以藉由在 URL 中指定 `path` 參數，將線條和多邊形新增至靜態地圖影像。 如同 Google Maps，您也可以在這個參數中指定樣式和位置清單，而且可以多次指定 `path` 參數以呈現多個具有不同樣式的圓形、線條和多邊形。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

關於路徑位置，Azure 地圖服務會要求座標採用「經度 緯度」格式，而 Google Maps 則使用「緯度,經度」格式。 另請注意，在 Azure 地圖服務中，經度和緯度會用空格而不是逗號來分隔。 Azure 地圖服務不支援對位置點使用經過編碼的路徑或地址。 較大的資料集可以透過 GeoJSON 檔案的形式上傳至 Azure 地圖服務的資料儲存體 API 中，如[這裡](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)所述。

Azure 地圖服務中的路徑樣式會以 `optionNameValue` 格式來新增，如有多個樣式，則以縱線字元 (\|) 來分隔，例如 `optionName1Value1|optionName2Value2`。 請注意，選項名稱和值不會分隔。 在 Azure 地圖服務中，下列樣式選項名稱可用來設定路徑樣式：

- `fa` - 在呈現多邊形時所使用的填滿色彩不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
- `fc` - 用來呈現多邊形區域的填滿色彩。
- `la` – 在繪製線條和多邊形外框時所使用的線條色彩不透明度 (Alpha)。 可以是 0 到 1 之間的數字。
- `lc` – 用來呈現線條和多邊形外框的線條色彩。
- `lw` – 線條的寬度 (以像素為單位)。
- `ra` – 指定圓形半徑 (以公尺為單位)。

例如，在 Azure 地圖服務中，不透明度為 50% 且粗細為 4 個像素的紅色線條可使用下列 URL 參數新增至地圖上的座標 (經度：-110, 緯度：45) 和 (經度：-100, 緯度：50) 之間：

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure 地圖服務的聚合線條](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>計算距離矩陣

Azure 地圖服務提供了 API 供您以距離矩陣的形式計算一組位置之間的行進時間和距離。 Azure 地圖服務的距離矩陣 API 與 Google Maps 中的距離矩陣 API 類似；

- [**路線矩陣**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以非同步方式計算一組起點和終點的行進時間和距離。 每個要求最多可支援 700 個資料格 (起點數目乘以終點數目)。 在考慮到該條件約束的情況下，可能的矩陣維度範例如下：700x1、50x10、10x10、28x25、10x70。

> [!NOTE]
> 針對距離矩陣 API 的要求只能使用 POST 要求來提出，而且要在要求主體中附上起點和終點資訊。 此外，Azure 地圖服務會要求所有起點和終點都必須是座標。 地址必須先進行地理編碼。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數      | 類似的 Azure 地圖服務 API 參數  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – 在 POST 要求主體中指定為 GeoJSON。 |
| `key`                          | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。 |
| `language`                     | `language` – 請參閱[支援的語言](supported-languages.md)文件。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – 在 POST 要求主體中指定為 GeoJSON。  |
| `region`                       | *N/A* – 這是與地理編碼有關的功能。 在使用 Azure 地圖服務的地理編碼 API 時，請使用 `countrySet` 參數。 |
| `traffic_model`                | *N/A* – 只能指定是否應該讓交通資料與 `traffic` 參數搭配使用。 |
| `transit_mode`                 | *N/A* - 目前不支援以運輸系統為基礎的距離矩陣。  |
| `transit_routing_preference`   | *N/A* - 目前不支援以運輸系統為基礎的距離矩陣。  |
| `units`                        | *N/A* – Azure 地圖服務只會使用公制單位。 |

> [!TIP]
> Azure 地圖服務路線 API 中可用的所有進階路線選項 (卡車路線、引擎規格、避開)，Azure 地圖服務距離矩陣 API 也都會支援。

## <a name="get-a-time-zone"></a>取得時區

Azure 地圖服務提供了 API 供您擷取座標所在的時區。 Azure 地圖服務的時區 API 類似於 Google Maps 中的時區 API：

- [**時區 (依座標)** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)：指定座標並取得其所在時區的詳細資料。

下表會交互參照 Google Maps API 參數與 Azure 地圖服務中類似的 API 參數。

| Google Maps 的 API 參數 | 類似的 Azure 地圖服務 API 參數   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另請參閱[向 Azure 地圖服務驗證](azure-maps-authentication.md)文件。       |
| `language`                  | `language` – 請參閱[支援的語言](supported-languages.md)文件。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

除此之外，Azure 地圖服務平台也提供了一些額外的時區 API，以協助使用時區名稱和識別碼進行轉換：

- [**時區 (依識別碼)** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)：針對指定的 IANA 時區識別碼，傳回目前、以往和未來的時區資訊。
- [**時區列舉 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：傳回 IANA 時區識別碼的完整清單。 IANA 服務的更新會在一天內就反映在系統中。
- [**時區列舉 Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：傳回 Windows 時區識別碼的完整清單。
- [**時區 IANA 版本**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：傳回 Azure 地圖服務所用的目前 IANA 版本號碼。
- [**Windows 轉 IANA 時區**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：在給定有效 Windows 時區識別碼的情況下，傳回對應的 IANA 識別碼。 單一 Windows 識別碼可能會傳回多個 IANA 識別碼。

## <a name="client-libraries"></a>用戶端程式庫

Azure 地圖服務針對下列程式設計語言提供了用戶端程式庫：

- JavaScript、TypeScript、Node.js – [文件](how-to-use-services-module.md) \| [NPM 套件](https://www.npmjs.com/package/azure-maps-rest)

適用於其他程式設計語言的開放原始碼用戶端程式庫：

- .NET Standard 2.0 – [GitHub 專案](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 套件](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>其他資源

以下是關於 Azure 地圖服務 REST 服務的一些額外文件和資源。

- [搜尋的最佳做法](how-to-use-best-practices-for-search.md)
- [搜尋地址](how-to-search-for-address.md)
- [Azure 地圖服務 REST 服務 API 參考文件](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務的 REST 服務。

> [!div class="nextstepaction"]
> [使用搜尋服務的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服務模組 (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)