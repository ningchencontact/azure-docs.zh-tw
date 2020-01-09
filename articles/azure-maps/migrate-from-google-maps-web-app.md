---
title: 從 Google Maps 遷移 web 應用程式 |Microsoft Docs
description: 有關如何將 web 應用程式從 Google Maps 遷移至 Microsoft Azure Maps 的教學課程。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562165"
---
# <a name="migrate-a-web-app-from-google-maps"></a>從 Google Maps 遷移 web 應用程式

大部分使用 Google Maps 的 web 應用程式都是使用 Google Maps V3 JavaScript SDK。 Azure 地圖服務 Web SDK 是適合用來遷移至的 Azure SDK。 Azure 地圖服務 Web SDK 可讓您以自己的內容和圖像，自訂顯示在 Web 或行動應用程式中的互動式地圖。 此控制項使用 WebGL，可讓您以高效能轉譯大型資料集。 使用 JavaScript 或 TypeScript 以此 SDK 進行開發。

如果您要遷移現有的 web 應用程式，請檢查它是否使用開放原始碼地圖控制項程式庫，例如 Cesium、Leaflet 和 OpenLayers。 如果是，而且您不想使用 Azure 地圖服務 Web SDK，則另一個用於遷移應用程式的選項是繼續使用開放原始碼地圖控制項，並將它連接到 Azure 地圖服務圖格服務（[道路磚](https://docs.microsoft.com/rest/api/maps/render/getmaptile)\|[衛星磚](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)）。 以下是如何在一些常用的開放原始碼地圖控制項程式庫中使用 Azure 地圖服務的詳細資料。

- Cesium-web 的3D 地圖控制項。 程式[代碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS)\|[檔](https://cesiumjs.org/)
- Leaflet –適用于 web 的輕量2D 地圖控制項。 程式[代碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS)\|[檔](https://leafletjs.com/)
- OpenLayers-支援投射之 web 的2D 地圖控制項。 程式[代碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers)\|[檔](https://openlayers.org/)

## <a name="key-features-support"></a>主要功能支援

下表列出 Google Maps V3 JavaScript SDK 中的主要 API 功能，以及 Azure 地圖服務 Web SDK 中類似 API 的支援。

| Google Maps 功能     | Azure 地圖服務 Web SDK 支援 |
|-------------------------|:--------------------------:|
| 標記                 | ✓                          |
| 標記群集       | ✓                          |
| 折線 & 多邊形    | ✓                          |
| 資料層             | ✓                          |
| 地面重迭         | ✓                          |
| 熱度圖               | ✓                          |
| 圖格圖層             | ✓                          |
| KML 層               | ✓                          |
| 繪圖工具           | ✓                          |
| Geocoder 服務        | ✓                          |
| 路線服務      | ✓                          |
| 距離矩陣服務 | ✓                          |
| 提高許可權服務       | 已規劃                    |

## <a name="notable-differences-in-the-web-sdks"></a>Web Sdk 中的顯著差異

以下是 Google Maps 與 Azure 地圖服務 Web Sdk 之間的一些主要差異，以瞭解：

- 除了提供用來存取 Azure 地圖服務 Web SDK 的主控端點之外，如果慣用，NPM 套件也可用於將 Web SDK 內嵌至應用程式。 如需詳細資訊，請參閱此[檔](how-to-use-map-control.md)。 此套件也包含 TypeScript 定義。
- 在 Azure 地圖服務中建立 Map 類別的實例之後，您的程式碼應該會等待對應 `ready` 或 `load` 事件引發，然後再與對應進行互動。 這可確保所有對應資源都已載入，並可供存取。
- 這兩種平臺都會針對基底地圖使用類似的磚系統，不過，在 Azure 地圖服務中的磚是維度中的512圖元時，Google 地圖中的圖格會是256圖元。 因此，若要取得 Azure 地圖服務中的相同地圖視圖作為 Google Maps，必須在 Azure 地圖服務中減去 Google Maps 中使用的縮放層級。
- Google 地圖中的座標稱為「緯度、經度」，而 Azure 地圖服務使用「經度，緯度」。 這會與標準 `[x, y]` 一致，後面接著大部分的 GIS 平臺。
- Azure 地圖服務 Web SDK 中的圖形是以 GeoJSON 架構為基礎。 協助程式類別是透過 [的[*資料*命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)] 來公開。 另外還有一種「[*塔」。Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)類別，可以用來包裝 GeoJSON 物件，並以資料可系結的方式輕鬆更新和維護。
- Azure 地圖服務中的座標會定義為位置物件，可使用 `[longitude, latitude]` 或新的（經度、緯度）格式指定為簡單數位陣列。
    > [!TIP]
    > Position 類別具有靜態 helper 方法，可用於匯入「緯度，經度」格式的座標。 [FromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)方法通常會取代 Google Maps 程式碼中的 `new google.maps.LatLng` 方法。
- 並不會在新增至地圖的每個圖形上指定樣式資訊，而是 Azure 地圖服務分隔資料中的樣式。 資料會儲存在資料來源中，並連接到 Azure 地圖服務程式碼用來呈現資料的轉譯層。 這種方法可提供增強的效能優勢。 此外，許多層級都支援資料驅動樣式，可將商務邏輯加入至圖層樣式選項，以根據圖形中定義的屬性來變更圖層內個別圖形的呈現方式。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK 並存範例

以下是每個平臺的程式碼範例集合，其中涵蓋了常見的使用案例，可協助您將 web 應用程式從 Google Maps V3 JavaScript SDK 遷移至 Azure 地圖服務 Web SDK。 與 web 應用程式相關的程式碼範例是以 JavaScript 提供;不過，Azure 地圖服務也會透過[NPM 模組](how-to-use-map-control.md)，提供 TypeScript 定義做為額外的選項。

### <a name="load-a-map"></a>載入對應

在這兩個 SDK 中載入對應都會遵循相同的一組步驟：

- 新增對應 SDK 的參考。
- 將 `div` 標記新增至頁面的主體，以作為地圖的預留位置。
- 建立可在頁面載入時呼叫的 JavaScript 函式。
- 建立個別 map 類別的實例。

**一些主要差異**

- Google maps 需要在 API 的腳本參考中指定帳戶金鑰。 Azure 地圖服務的驗證認證會指定為 map 類別的選項。 這可以是訂用帳戶金鑰或 Azure Active Directory 資訊。
- Google Maps 會在 API 的腳本參考中採用回呼函式，用來呼叫初始化函數以載入對應。 使用 Azure 地圖服務應該使用頁面的 onload 事件。
- 參考將在其中轉譯對應的 `div` 元素時，Azure 地圖服務中的 `Map` 類別只需要 `id` 值，而 Google Maps 需要 `HTMLElement` 物件。
- Azure 地圖服務中的座標會定義為位置物件，可使用 `[longitude, latitude]`格式指定為簡單數位陣列。
- Azure 地圖服務的縮放層級比 Google Maps 範例低一層級，原因是在不同平臺之間並排系統大小的差異。
- 根據預設，Azure 地圖服務不會將任何導覽控制項加入至地圖畫布，例如 [縮放按鈕] 和 [地圖樣式] 按鈕。 不過，有一些控制項可以用來加入地圖樣式選擇器、縮放按鈕、羅盤或旋轉控制項，以及「音調」控制項。
- 會在 Azure 地圖服務中加入事件處理常式，以監視對應實例的 `ready` 事件。 當對應完成載入 WebGL 內容和所需的所有資源時，就會引發這種情況。 您可以在此事件處理常式中加入任何 post 載入代碼。

下列範例示範如何載入基本地圖，例如以紐約座標（經度：-73.985、緯度：40.747）為中心，而在 Google Maps 中為縮放層級12。

**之前： Google Maps**

下列程式碼範例示範如何顯示置中的 Google 地圖，並將其縮放至某個位置。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示對應，如下圖所示：

<center>

![簡單的 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**之後： Azure 地圖服務**

下列程式碼示範如何在 Azure 地圖服務中載入具有相同視圖的對應，以及地圖樣式控制項和縮放按鈕。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示對應，如下圖所示：

<center>

![簡單 Azure 地圖服務](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

如需如何在 web 應用程式中設定及使用 Azure 地圖服務地圖控制項的詳細檔，請參閱[這裡](how-to-use-map-control.md)。

> [!NOTE]
> 不同于 Google Maps，Azure 地圖服務不需要在載入對應時指定初始置中和縮放層級。 如果在載入對應時未提供這項資訊，地圖將會嘗試判斷使用者所在的城市，並在該處將地圖置中並縮放。

**其他資源：**

- Azure 地圖服務也會提供用來旋轉和推銷地圖視圖的導覽控制項，如[這裡](map-add-controls.md)所述。

### <a name="localizing-the-map"></a>當地語系化對應

如果您的物件散佈在多個國家/地區，或說不同的語言，則當地語系化很重要。

**之前： Google Maps**

若要將 Google Maps 當地語系化，請將語言和地區參數新增至

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

以下是將語言設定為 "fr-fr" 的 Google Maps 範例。

<center>

![Google Maps 當地語系化](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**之後： Azure 地圖服務**

Azure 地圖服務提供兩種不同的方式來設定地圖的語言和地區視圖。 第一個選項是將這項資訊新增至全域的*塔*命名空間，這會導致應用程式中的所有地圖控制項實例預設為這些設定。 以下會將語言設定為法文（"fr-fr"），並將地區視圖設為「自動」：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二個選項是在載入對應時，將此資訊傳入對應選項，如下所示：

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> 有了 Azure 地圖服務，就可以使用不同的語言和區域設定，在相同的頁面上載入多個對應實例。 此外，在對應中載入之後，也可以更新這些設定。 如需 Azure 地圖服務支援語言的詳細清單，請參閱[這裡](supported-languages.md)。

以下是語言設為 "fr" 且使用者區域設定為 "fr-fr" 的 Azure 地圖服務範例。

<center>

![Azure 地圖服務當地語系化](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>設定地圖視圖

您可以透過在 JavaScript 中呼叫適當的函式，以程式設計方式將 Azure 和 Google Maps 中的動態對應移至新的地理位置。 下列範例示範如何讓地圖顯示衛星的航空影像，並將地圖放在具有座標的位置上（經度：-111.0225、緯度：35.0272），並在 Google 地圖中將縮放層級變更為15。

> [!NOTE]
> Google Maps 會使用在維度中為256圖元的磚，而 Azure 地圖服務使用較大的512圖元磚。 這會減少 Azure 地圖服務載入與 Google 地圖相同的對應區域所需的網路要求數目。 不過，由於磚金字塔在地圖控制項中的顯示方式，Azure 地圖服務中較大的磚表示在 Google Maps 中達到與地圖相同的可見區域時，您必須在使用 Azure 地圖服務時，將 Google Maps 中使用的縮放層級減一。

**之前： Google Maps**

您可以使用 `setOptions` 方法，以程式設計的方式移動 Google Maps 地圖控制項，讓您指定地圖的中心和縮放層級。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps set view](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，您可以使用對應的 `setCamera` 方法以程式設計方式變更地圖位置，並使用 `setStyle` 方法變更地圖樣式。 請注意，Azure 地圖服務中的座標是「經度，緯度」格式，而縮放層級值則減去一。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure 地圖服務設定視圖](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**其他資源：**

- [選擇地圖樣式](choose-map-style.md)
- [支援的地圖樣式](supported-map-styles.md)

### <a name="adding-a-marker"></a>加入標記

在 Azure 地圖服務有多種方式可在地圖上轉譯點資料;

- **HTML 標籤**–使用傳統 DOM 元素轉譯點。 HTML 標籤支援拖曳。
- **符號圖層**–使用 WebGL 內容中的圖示和/或文字呈現點。
- 反升**層**–將點呈現為地圖上的圓圈。 圓圈半徑可以根據資料中的屬性進行調整。

符號和反升層都會在 WebGL 內容中轉譯，而且能夠在地圖上轉譯非常大的點集合。 這些圖層需要將資料儲存在資料來源中。 在 `ready` 事件引發之後，應該將資料來源和呈現層新增至對應。 HTML 標籤會轉譯為頁面中的 DOM 元素，而且不會使用資料來源。 頁面所擁有的 DOM 元素越多，頁面就會變慢。 如果在地圖上轉譯一百個以上的點，建議改用其中一個轉譯圖層。

下列範例會將標記新增至地圖（經度：-0.2、緯度：51.5），並將數位10重迭為標籤。

**之前： Google Maps**

使用 Google Maps 時，標記會使用 `google.maps.Marker` 類別來新增至地圖，並藉由指定地圖做為其中一個選項。

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps 標記](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**之後：使用 HTML 標籤 Azure 地圖服務**

在 Azure 地圖服務中，HTML 標籤可用來顯示地圖上的某個點，建議只用于只需要在地圖上顯示少量點的應用程式。 若要使用 HTML 標籤，只需建立 `atlas.HtmlMarker` 類別的實例、設定文字和位置選項，然後使用 `map.markers.add` 方法將標記加入至地圖。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務 HTML 標籤](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**After：使用符號層 Azure 地圖服務**

使用符號圖層時，資料必須加入至資料來源，以及連接至圖層的資料來源。 此外，在 `ready` 事件引發之後，應該將資料來源和圖層新增至地圖。 若要在符號上方轉譯唯一的文字值，文字資訊必須儲存為資料點的屬性，而且該屬性會在圖層的 [`textField`] 選項中參考。 相較于使用 HTML 標籤，這是比較多的工作，但提供了許多效能優勢。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務符號層](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**其他資源：**

- [建立資料來源](create-data-source-web-sdk.md)
- [新增符號圖層](map-add-pin.md)
- [新增氣泡圖層](map-add-bubble-layer.md)
- [叢集點資料](clustering-point-data-web-sdk.md)
- [新增 HTML 標籤](map-add-custom-html.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
- [符號圖層圖示選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [符號圖層文字選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 標籤類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 標籤選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>新增自訂標記

自訂影像可以用來代表地圖上的點。 下列範例使用自訂影像，在地圖上顯示點（緯度：51.5、經度：-0.2）並位移標記的位置，讓圖釘圖示的點與地圖上的正確位置對齊。

<center>

![黃色圖釘影像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_圖釘</center>

**之前： Google Maps**

在 Google Maps 中，會藉由指定包含影像 `url` 的 `Icon` 物件來建立自訂標記，`anchor` 點將圖釘影像的點對齊地圖上的座標。 Google Maps 中的錨點值，相對於影像的左上角。

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps 自訂標記](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**之後：使用 HTML 標籤 Azure 地圖服務**

若要在中自訂 HTML 標籤 Azure 地圖服務 HTML `string` 或 `HTMLElement` 可以傳遞至標記的 `htmlContent` 選項。 在 Azure 地圖服務中，`anchor` 選項是用來使用九個定義的參考點之一，指定相對於位置座標之標記的相對位置;[置中]、[頂端]、[底端]、[左]、[右]、[左上角]、[右上角]、[右下方]。 根據預設，內容會錨定到 html 內容的正下方。 為了讓您更輕鬆地從 Google Maps 遷移程式碼，請將 `anchor` 設定為「左上方」，然後使用 [`pixelOffset`] 選項搭配 Google Maps 中使用的相同位移。 Azure 地圖服務中的位移會以相對於 Google 地圖的方向移動，因此請將它們乘以減一。

> [!TIP]
> 新增 `pointer-events:none` 做為 html 內容的樣式，以停用 Microsoft Edge 中會顯示不需要圖示的預設拖曳行為。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure 地圖服務自訂 HTML 標籤](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**After：使用符號層 Azure 地圖服務**

Azure 地圖服務中的符號層也支援自訂映射，但必須先將映射載入至對應資源，並指派唯一識別碼。 符號圖層接著可以參考此識別碼。 您可以使用 [`offset`] 選項圖示來位移符號，以對齊影像上的正確點。 在 Azure 地圖服務中，`anchor` 選項可用來指定符號相對於位置座標的相對位置，其方式是使用九個定義參考點的其中一個：[置中]、[頂端]、[底端]、[左]、[右]、[左上角]、[右上角]、[右下方]。 根據預設，內容會錨定到 html 內容的正下方。 為了讓您更輕鬆地從 Google Maps 遷移程式碼，請將 `anchor` 設定為「左上方」，然後使用 [`offset`] 選項搭配 Google Maps 中使用的相同位移。 Azure 地圖服務中的位移會以相對於 Google 地圖的方向移動，因此請將它們乘以減一。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務自訂圖示符號層](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> 若要建立點的先進自訂轉譯，請同時使用多個呈現層。 例如，如果您想要讓多個圖釘在不同的彩色圓形上具有相同的圖示，而不是針對每個色彩在反升圖層上方重迭符號圖層，並讓它們參考相同的資料來源，而不是建立一堆影像。 這會比建立更有效率，而且地圖會維護一堆不同的影像。

**其他資源：**

- [建立資料來源](create-data-source-web-sdk.md)
- [新增符號圖層](map-add-pin.md)
- [新增 HTML 標籤](map-add-custom-html.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
- [符號圖層圖示選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [符號圖層文字選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 標籤類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 標籤選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>新增折線

線條是用來代表地圖上的一條線或路徑。 下列範例示範如何在地圖上建立虛線折線。

**之前： Google Maps**

在 Google Maps 中，聚合類別會採用一組選項。 座標陣列會在聚合線條的 `path` 選項中傳遞。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps 的聚合線條](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，折線稱為 LineString 或 MultiLineString 物件。 這些物件可以加入至資料來源，並使用線條圖層來轉譯。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure 地圖服務的折線](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**其他資源：**

- [將線條新增至地圖](map-add-line-layer.md)
- [線條圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>加入多邊形

多邊形是用來代表地圖上的區域。 Azure 地圖服務和 Google 地圖服務為多邊形提供非常類似的支援。 下列範例示範如何根據地圖的中心座標，建立形成三角形的多邊形。

**之前： Google Maps**

在 Google Maps 中，多邊形類別會採用一組選項。 座標陣列會在多邊形的 `paths` 選項中傳遞。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps 多邊形](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，可以將多邊形和 MultiPolygon 物件加入至資料來源，並使用圖層在地圖上轉譯。 多邊形的區域可以在多邊形圖層中呈現。 多邊形的外框可以使用線條圖層來呈現。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure 地圖服務多邊形](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**其他資源：**

- [將多邊形新增至地圖](map-add-shape.md)
- [將圓形新增至地圖](map-add-shape.md#add-a-circle-to-the-map)
- [多邊形圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [線條圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>顯示資訊視窗

實體的其他資訊可以在地圖上顯示為 Google Maps 中的 `google.maps.InfoWindow` 類別，Azure 地圖服務這可以使用 `atlas.Popup` 類別來達成。 下列範例會將標記新增至地圖，然後按一下 [顯示資訊視窗/快顯]。

**之前： Google Maps**

使用 Google Maps 時，會使用 `google.maps.InfoWindow` 的函式來建立資訊視窗。

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps 快顯](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務可以使用快顯視窗來顯示位置的其他資訊。 HTML `string` 或 `HTMLElement` 物件可以傳遞至快顯的 `content` 選項。 如有需要，您可以單獨顯示任何圖形的快顯功能表，因此需要指定 `position` 值。 若要顯示快顯視窗，請呼叫 `open` 方法，並傳入要在其中顯示快顯的 `map`。

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure 地圖服務快捷方式](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 若要使用符號、反升、線條或多邊形圖層來執行相同的動作，只要將圖層傳遞至 maps 事件程式碼，而不是標記即可。

**其他資源：**

- [新增快顯](map-add-popup.md)
- [具有媒體內容的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [圖形上的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [重複使用具有多個 Pin 的快顯視窗](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [快顯視窗選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>匯入 GeoJSON 檔案

Google Maps 支援透過 `google.maps.Data` 類別來載入和動態設定 GeoJSON 資料的樣式。 這個類別的功能會與 Azure 地圖服務的資料驅動樣式進行比對。 其中一個主要差異在於，使用 Google Maps 時，您可以指定回呼函式和商務邏輯來設定它在 UI 執行緒中個別處理之每項功能的樣式。 在 Azure 地圖服務中，層支援指定資料驅動運算式做為樣式選項。 這些運算式會在轉譯時于個別的執行緒上處理，並提供更高的轉譯效能，並可更快速地轉譯較大的資料集。

下列範例會從 USGS 載入過去七天內所有地震的 GeoJSON 摘要，並將它們轉譯為地圖上的縮放圓形。 每個圓形的色彩和小數位數是根據每個地震的大小，儲存在資料集內每項功能的 `"mag"` 屬性中。 如果量值大於或等於5，則圓形會是紅色，如果大於或等於3但小於五，則圓形會是橙色（如果小於三），圓圈會是綠色。 每個圓形的半徑會是大小乘以0.1 的指數。

**之前： Google Maps**

在 Google Maps 中，可以在 `map.data.setStyle` 方法中指定單一回呼函式，這會用來透過 `map.data.loadGeoJson` 方法，將商務邏輯套用至從 GeoJSON 摘要載入的每項功能。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**之後： Azure 地圖服務**

GeoJSON 是 Azure 地圖服務中的基本資料類型，而且可以使用 `datasource.importFromUrl` 方法輕鬆地匯入資料來源中。 反升層會根據資料來源中的功能屬性，提供用來轉譯縮放圓形的功能。 商務邏輯會轉換成運算式，並傳遞至樣式選項，而不是使用回呼函數。 運算式會定義商務邏輯的運作方式，以便將其傳遞至另一個執行緒，並根據功能資料進行評估。 您可以將多個資料來源和層級新增至 Azure 地圖服務，每個都有不同的商務邏輯，因此可讓多個資料集以不同的方式轉譯在地圖上。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務 GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**其他資源：**

- [新增符號圖層](map-add-pin.md)
- [新增氣泡圖層](map-add-bubble-layer.md)
- [叢集點資料](clustering-point-data-web-sdk.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>標記群集

視覺化地圖上的許多資料點時，點會彼此重迭，地圖看起來很雜亂，並且變得難以查看和使用。 點資料的叢集可以用來改善此使用者體驗，也能改善效能。 群集點資料是結合彼此接近的點資料，並將它們以單一叢集資料點表示在地圖上的程式。 當使用者放大地圖時，叢集會分解成個別的資料點。

下列範例會從過去一周載入地震資料的 GeoJSON 摘要，並將其新增至地圖。 根據所包含的點數，將叢集轉譯為縮放和彩色圓形。

> [!NOTE]
> 有數種不同的演算法用於標記群集。 Google 和 Azure 地圖服務會使用稍微不同的演算法。 因此，在叢集中的點分佈有時可能會有所不同。

**之前： Google Maps**

在中，您可以藉由在 MarkerClusterer 程式庫中載入來叢集化 Google Maps 標記。 叢集圖示僅限於影像，其名稱為一到五，並裝載于相同的目錄中。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 叢集](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，資料是由資料來源加入和管理。 圖層會連接到資料來源，並在其中呈現資料。 Azure 地圖服務中的 `DataSource` 類別提供數個群集選項。

- `cluster` –告訴資料來源對叢集點資料。
- `clusterRadius`-叢集點在一起的 radius （以圖元為單位）。
- `clusterMaxZoom`-發生群集的最大縮放層級。 如果您放大以上的內容，所有點都會轉譯為符號。
- `clusterProperties`-定義自訂屬性，其會針對每個叢集內的所有點使用運算式來計算，並將其新增至每個叢集點的屬性。

啟用叢集時，資料來源會將叢集和叢集資料點傳送到圖層進行轉譯。 資料來源能夠叢集化數十萬個資料點。 叢集資料點具有下列屬性：

| 屬性名稱             | 類型    | 說明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指出功能是否代表叢集。 |
| `cluster_id`              | string  | 可搭配 DataSource `getClusterExpansionZoom`、`getClusterChildren`和 `getClusterLeaves` 方法使用之叢集的唯一識別碼。 |
| `point_count`             | number  | 叢集包含的點數。  |
| `point_count_abbreviated` | string  | 縮寫 `point_count` 值的字串（如果長度很長）。 （例如，4000變成4K）  |

`DataSource` 類別具有下列 helper 函式，可使用 `cluster_id`來存取叢集的其他相關資訊。

| 方法 | 傳回類型 | 說明 |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | 承諾&lt;陣列&lt;功能&lt;Geometry，任何&gt; \| Shape&gt;&gt; | 在下一個縮放層級上，抓取給定叢集的子系。 這些子系可以是圖案和 subclusters 的組合。 Subclusters 將會是屬性符合 ClusteredProperties 的功能。 |
| `getClusterExpansionZoom(clusterId: number)` | 承諾&lt;數目&gt; | 計算叢集將開始擴充或中斷的縮放層級。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | 承諾&lt;陣列&lt;功能&lt;Geometry，任何&gt; \| Shape&gt;&gt; | 抓取叢集中的所有點。 設定 `limit` 以傳回點的子集，並使用 `offset` 來逐頁查看點。 |

在地圖上轉譯叢集資料時，使用兩個或更多層通常是最簡單的方式。 下列範例會使用三個層級，這是一個反升層，可根據群集大小繪製縮放的彩色圓形、一個符號圖層，以文字呈現叢集大小，而第二個符號層用於轉譯叢集點。 有許多其他方式可在叢集[點資料](clustering-point-data-web-sdk.md)檔的 Azure 地圖服務中，呈現叢集資料。

您可以使用 `DataSource` 類別上的 `importDataFromUrl` 函式，在 Azure 地圖服務中直接匯入 GeoJSON 資料。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務叢集](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**其他資源：**

- [新增符號圖層](map-add-pin.md)
- [新增氣泡圖層](map-add-bubble-layer.md)
- [叢集點資料](clustering-point-data-web-sdk.md)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>新增熱度圖

熱度圖 (也稱為點密度圖) 是一種資料視覺效果，用來以色彩範圍表示資料密度。 這些圖通常用來顯示地圖上的資料「作用點」，而且十分適合用來轉譯大型的點資料集。

下列範例會從 USGS 載入過去一個月所有地震的 GeoJSON 摘要，並將其轉譯為加權熱度圖，其中 `"mag"` 屬性會用來作為權數。

**之前： Google Maps**

在 Google Maps 中，若要建立熱度圖，必須藉由將 `&libraries=visualization` 新增至 API 腳本 URL 來載入「視覺效果」程式庫。 Google Maps 中的熱度圖層不支援直接 GeoJSON 資料，而是必須先將資料下載並轉換成加權資料點的陣列。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google 地圖熱度圖](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，將 GeoJSON 資料載入至資料來源，並將資料來源連接至熱度圖層。 將用於權數的屬性可以使用運算式傳遞至 `weight` 選項。 您可以使用 `DataSource` 類別上的 `importDataFromUrl` 函式，在 Azure 地圖服務中直接匯入 GeoJSON 資料。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務熱度圖](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**其他資源：**

- [新增熱度圖圖層](map-add-heat-map-layer.md)
- [熱度圖層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [熱度圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>重迭磚圖層

圖格圖層（也稱為「Google 地圖」中的影像重迭）可讓您將已分割的大型影像，重迭成與地圖磚系統對齊的小型影像。 這是用來覆迭大型影像或非常大型資料集的常見方式。

下列範例會將氣象雷達圖圖層與愛荷華州州大學的愛荷華州環境 Mesonet 重迭。

**之前： Google Maps**

在 Google Maps 中，您可以使用 `google.maps.ImageMapType` 類別來建立圖格圖層。

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps 磚圖層](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，可以將圖格圖層新增至地圖，其方式與任何其他圖層大致相同。 具有 x、y、縮放預留位置的格式化 URL;`{x}`、`{y}`、`{z}` 分別用來告訴圖層要存取磚的位置。 Azure 地圖服務圖格圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。

> [!TIP]
> 在中，您可以輕鬆地將 Azure 地圖服務圖層轉譯為其他層級，包括基底地圖圖層。 通常最好是在地圖標籤下方轉譯磚圖層，使其易於閱讀。 `map.layers.add` 方法會接受第二個參數，也就是要在其中插入新圖層的圖層識別碼。 若要在地圖標籤下方插入磚圖層，可以使用下列程式碼： `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure 地圖服務圖格圖層](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> 您可以使用對應的 [`transformRequest`] 選項來捕捉磚要求。 這可讓您視需要修改或新增要求的標頭。

**其他資源：**

- [加入圖格圖層](map-add-tile-layer.md)
- [磚圖層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [圖格圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>顯示交通流量

流量資料可以同時重迭 Azure 和 Google maps。

**之前： Google Maps**

在 Google Maps 中，流量資料可以使用流量層來重迭地圖。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps 流量](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**之後： Azure 地圖服務**

Azure 地圖服務提供數個不同的選項來顯示流量。 流量事件（例如路段和意外）可顯示為地圖上的圖示。 流量（色彩編碼道路）可以在地圖上重迭，而且可以根據正常的預期延遲或絕對延遲，將色彩修改為相對於張貼的速度限制。 Azure 地圖服務中的事件資料會每分鐘更新一次，並每隔兩分鐘傳送一個流量資料。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure 地圖服務流量](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

如果您按一下 Azure 地圖服務中的其中一個流量圖示，則會在快顯視窗中顯示其他資訊。

<center>

![Azure 地圖服務流量事件](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**其他資源：**

- [在地圖上顯示路況](map-show-traffic.md)
- [流量重迭選項](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>新增地面重迭

Azure 和 Google maps 都支援在地圖上覆迭 georeferenced 影像，讓它們在您移動和縮放地圖時移動和縮放。 在 Google Maps 中，這些稱為「地面重迭」，而在 Azure 地圖服務它們稱為「影像圖層」。 這些非常適合用來建立樓層計畫、覆迭舊地圖或無人機的影像。

**之前： Google Maps**

在 Google Maps 中建立地面重迭時，您需要指定要重迭之影像的 URL，以及將影像系結至地圖上的周框方塊。 這個範例會在地圖上，[從1922重迭紐華克 New Jersey](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的地圖影像。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

在瀏覽器中執行此程式碼會顯示對應，如下圖所示：

<center>

![Google 地圖影像重迭](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，您可以使用 `atlas.layer.ImageLayer` 類別來重迭 georeferenced 影像。 此類別需要影像的 URL 和影像四個角落的一組座標。 映射必須裝載于相同的網域上，或已啟用 CORs。

> [!TIP]
> 如果您只有北、南部、東、west 和旋轉資訊，而不是影像每個角落的座標，則可以使用靜態[`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-)方法。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure 地圖服務影像重迭](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**其他資源：**

- [覆迭影像](map-add-image-layer.md)
- [影像層類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>其他程式碼範例

以下是一些與 Google Maps 遷移相關的其他程式碼範例：

- [繪圖工具](map-add-drawing-toolbar.md)
- [將地圖限制為兩個手指移動](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [限制 Scroll 滾輪 Zoom](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [建立全螢幕控制項](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**服務：**

- [使用 Azure 地圖服務 services 模組](how-to-use-services-module.md)
- [搜尋興趣點](map-search-location.md)
- [從座標取得資訊（反向地理編碼）](map-get-information-from-coordinate.md)
- [顯示從甲地到乙地的指示](map-route.md)
- [使用 JQuery UI 搜尋自動建議](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Azure 地圖服務 Web SDK 類別對應的 Google Maps V3

下列附錄提供 Google Maps V3 中最常使用的類別到其 Azure 地圖服務 Web SDK 對等專案的交互參照對應。

### <a name="core-classes"></a>核心類別

| Google 地圖   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.功能表](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [塔. 資料。位置](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.寬](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>重迭類別

| Google 地圖  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[塔. 資料點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.Bubblelayeroptions 查看](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [塔. 資料. 多邊形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | 請參閱[將圓形新增至地圖](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>服務類別

Azure 地圖服務 Web SDK 包含可以個別載入的[服務模組](how-to-use-services-module.md)。 此模組會使用 Web API 包裝 Azure 地圖服務 REST 服務，並可在 JavaScript、TypeScript 和 node.js 應用程式中使用。

| Google 地圖 | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>程式庫

程式庫會將其他功能新增至地圖。 其中有許多都是在 Azure 地圖服務的核心 SDK 中。 以下是一些用來取代這些 Google Maps 程式庫的對等類別

| Google 地圖           | Azure Maps   |
|-----------------------|--------------|
| 繪製程式庫       | [繪圖工具模組](set-drawing-options.md) |
| 幾何程式庫      | [塔. 數學](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 視覺效果程式庫 | [熱度圖層](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 地圖服務 Web SDK。

> [!div class="nextstepaction"]
> [如何使用地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [如何使用服務模組](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [如何使用繪圖工具模組](set-drawing-options.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

