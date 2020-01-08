---
title: 遷移 Android 應用程式 |Microsoft Docs
description: 如何將 Android 應用程式從 Google Maps 遷移至 Microsoft Azure Maps 的教學課程。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481395"
---
# <a name="migrate-an-android-app-from-google-maps"></a>從 Google Maps 遷移 Android 應用程式

Azure 地圖服務 Android SDK 具有與 Web SDK 非常類似的 API 介面。 如果您已使用其中一個 Sdk 進行開發，則會有許多相同的概念、最佳作法和架構，而且您應該能夠輕鬆地將您的知識轉移到另一個。

Azure 地圖服務 Android SDK 支援 API 21 的最低 Android 版本： Android 5.0.0 （棒糖）。

JAVA 中提供的所有範例（但 Kotlin）也可以搭配 Azure 地圖服務 Android SDK 使用。

另請參閱[Azure 地圖服務 Android SDK](how-to-use-android-map-control-library.md)的操作指南，以取得使用此 SDK 進行開發的詳細資訊。

## <a name="load-a-map"></a>載入對應

使用 Google 或 Azure 地圖服務在 Android 應用程式中載入對應，是由許多相同的步驟所組成。 使用任一種 SDK 時，您必須：

- 取得 API 或訂用帳戶金鑰，以存取任一平臺。
- 將一些 XML 新增至活動，以指定應該呈現對應的位置，以及應該如何配置地圖。
- 將所有生命週期方法從包含地圖視圖的活動轉送到對應類別中的對應。 特別是，您必須在下列方法中轉送：
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 請等候對應準備就緒，再嘗試以程式設計方式進行存取。

**之前： Google Maps**

若要使用適用于 Android 的 Google Maps SDK 來顯示對應，將會執行下列步驟：

1.  請確定已安裝 Google Play 服務。
2.  將 Google Maps 服務的相依性新增至模組的**gradle**檔案： 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  在**google\_Maps\_API .xml**檔案的應用程式區段內新增 GOOGLE maps API 金鑰：
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  將地圖片段新增至主要活動：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  在**MainActivity**中，您將需要新增 GOOGLE Maps SDK 的匯入。 將所有生命週期方法從包含地圖視圖的活動轉送到對應類別中的對應。 您可以使用 `getMapAsync(OnMapReadyCallback)` 方法，從對應片段中抓取 `MapView` 實例。 `MapView` 會自動初始化 maps 系統和 view。 編輯**MainActivity** ，如下所示：

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

在應用程式中執行時，地圖控制項將會以下列方式載入。

<center>

![簡單的 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**之後： Azure 地圖服務**

若要使用適用于 Android 的 Azure 地圖服務 SDK 來顯示對應，必須執行下列步驟：

1. 開啟頂層**gradle**檔案，並將下列程式碼新增至 [**所有專案**]、[**存放庫**區塊] 區段：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新您的**app/gradle** ，並在其中新增下列程式碼：
    
    1. 請確定您專案的**minSdkVersion**是在 API 21 或更新版本。

    2. 將下列程式碼新增至 Android 區段：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新您的相依性區塊，並針對最新的 Azure 地圖服務 Android SDK 加入新的執行相依性程式程式碼：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure 地圖服務 Android SDK 會定期進行升級和加強。 您可以參閱[開始使用 Android 地圖控制項](how-to-use-android-map-control-library.md)檔，以取得最新的 Azure 地圖服務的執行版本號碼。 此外，您也可以將版本號碼從 "0.2" 設定為 "0 +"，讓它一律指向最新版本。
    
    4. 移至**工具列中的**[檔案]，然後按一下 [**同步處理專案與 Gradle**檔案]。
3. 將地圖片段新增至主要活動（res \> 版面配置 \> 活動\_main）：
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. 在**MainActivity**檔案中，您將需要：
    
    * 新增 Azure 地圖服務 SDK 的匯入
    * 設定您的 Azure 地圖服務驗證資訊
    * 取得**onCreate**方法中的地圖控制項實例

    使用 `setSubscriptionKey` 或 `setAadProperties` 方法，全域設定 `AzureMaps` 類別的驗證資訊，讓您不需要在每個視圖上新增驗證資訊。 

    地圖控制項包含自己的生命週期方法來管理 Android 的 OpenGL 生命週期，必須直接從包含活動呼叫。 為了讓您的應用程式正確地呼叫地圖控制項的生命週期方法，您必須覆寫活動中包含地圖控制項的下列生命週期方法，並呼叫個別的地圖控制項方法。 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    編輯**MainActivity** ，如下所示：
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

如果您執行應用程式，地圖控制項將會以下列方式載入。

<center>

![簡單 Azure 地圖服務](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

請注意，Azure 地圖服務控制項支援放大更多，並提供更多世界的觀點。

> [!TIP]
> 如果在 Windows 中使用 Android 模擬器，則對應可能不會因為 OpenGL 和軟體加速圖形轉譯而呈現。 以下是解決此問題的一些工作。 開啟 [AVD 管理員]，然後選取要編輯的虛擬裝置。 在 [**模擬的效能**] 區段中，將 [**圖形**] 選項設定為 [**硬體**]。

## <a name="localizing-the-map"></a>當地語系化對應

如果您的物件散佈在多個國家/地區，或說不同的語言，則當地語系化很重要。

**之前： Google Maps**

您可以在主要活動的 `onCreate` 方法中設定對應的語言，方法是在設定地圖的內容視圖之前加入下列程式碼。 以下會使用語言代碼 "fr" 將語言限制為法文。

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

以下是將語言設定為 "fr" 的 Google Maps 範例。

<center>

![Google Maps 當地語系化](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**之後： Azure 地圖服務**

Azure 地圖服務提供三種不同的方式來設定地圖的語言和地區視圖。 第一個選項是使用靜態 `setLanguage` 和全域 `setView` 方法，將語言和地區視圖資訊傳遞至 `AzureMaps` 類別。 這會在應用程式中載入的所有 Azure 地圖服務控制項之間設定預設語言和地區視圖。 以下會使用語言代碼 "fr-fr" 將語言限制為法文。

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

第二個選項是將語言和視圖資訊傳遞至地圖控制項 XML。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

第三個選項是使用 maps `setStyle` 方法，以程式設計方式設定地圖的語言和地區視圖。 這可以隨時執行，以變更地圖的語言和地區視圖。

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

以下是將語言設定為 "fr-fr" Azure 地圖服務的範例。

<center>

![Azure 地圖服務當地語系化](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

支援的語言和區域觀點的完整清單記載于[此處](supported-languages.md)。

## <a name="setting-the-map-view"></a>設定地圖視圖

您可以藉由呼叫適當的方法，以程式設計方式將 Azure 和 Google Maps 中的動態對應移至新的地理位置。 下列範例顯示如何讓地圖顯示衛星的航空影像、將地圖放在具有座標的位置上（緯度：35.0272、經度：-111.0225），並將 Google Maps 中的縮放層級變更為15。

> [!NOTE]
> Google Maps 會使用在維度中為256圖元的磚，而 Azure 地圖服務使用較大的512圖元磚。 這會減少 Azure 地圖服務載入與 Google 地圖相同的對應區域所需的網路要求數目。 不過，由於磚的金字塔在地圖控制項中的使用方式，Azure 地圖服務中較大的磚表示在 Google Maps 中達到與地圖相同的可見區域，因此在使用 Azure 地圖服務時，您必須將 Google Maps 中使用的縮放層級減去1。

**之前： Google Maps**

您可以使用 `moveCamera` 方法，以程式設計的方式移動 Google Maps 地圖控制項的相機，讓您指定地圖的中心和縮放層級。 `setMapType` 方法可以用來變更所顯示的對應類型。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps set view](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**之後： Azure 地圖服務**

如先前所述，若要在 Azure 地圖服務中達到相同的可查看區域，請將 Google Maps 中使用的縮放層級減一，在此案例中，使用的縮放層級為14。

初始地圖視圖可以在地圖控制項的 XML 屬性中設定。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

您可以使用對應 `setCamera` 和 `setStyle` 方法，以程式設計方式更新地圖視圖。

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure 地圖服務設定視圖](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**其他資源：**

- [支援的地圖樣式](supported-map-styles.md)

## <a name="adding-a-marker"></a>加入標記

點資料通常會使用地圖上的影像來呈現在地圖上。 這些影像通常稱為「標記」、「圖釘」、「釘選」或「符號」。 下列範例會將點資料轉譯為地圖上的標記（緯度：51.5、經度：-0.2）。

**之前： Google Maps**

使用 Google Maps 時，會使用 Maps `addMarker` 方法來新增標記。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps 標記](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，您可以先將資料加入資料來源，然後將資料來源連接至符號圖層，藉此在地圖上轉譯資料。 資料來源會優化地圖控制項中空間資料的管理，而符號圖層則會指定如何使用來呈現點資料做為影像和/或文字。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Azure 地圖服務標記](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>新增自訂標記

自訂影像可以用來代表地圖上的點。 下列範例使用自訂影像，在地圖上顯示點（緯度：51.5、經度：-0.2）並位移標記的位置，讓圖釘圖示的點與地圖上的正確位置對齊。

<center>

![黃色圖釘影像](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_圖釘</center>

在這兩個範例中，會將上述影像新增至應用程式資源的 [可繪製] 資料夾。

**之前： Google Maps**

使用 Google Maps 時，您可以透過標記的 [`icon`] 選項來載入自訂影像，以用於標記。 若要將影像點對齊座標，可以使用 [`anchor`] 選項。 錨點相對於影像的維度，在此案例中為0.2 單位寬，1個單位高。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Google Maps 自訂標記](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**之後： Azure 地圖服務**

Azure 地圖服務中的符號層也支援自訂映射，但必須先將映射載入至對應資源，並指派唯一識別碼。 符號圖層接著可以參考此識別碼。 使用 [`iconOffset`] 選項，符號可以位移對齊影像上的正確點。 請注意，圖示位移是以圖元為單位。 根據預設，位移是相對於影像的底部，但這可以使用 [`iconAnchor`] 選項來調整。 這個範例會將 [`iconAnchor`] 選項設定為 [`"center"`]，並使用 [圖示位移] 將影像向右移動五個圖元，並將 [15] 圖元對齊以配合圖釘影像的點。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure 地圖服務自訂標記](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>新增折線

線條是用來代表地圖上的一條線或路徑。 下列範例示範如何在地圖上建立虛線折線。

**之前： Google Maps**

使用 Google Maps 時，可以使用 `PolylineOptions` 類別來建立聚合線條，並使用 `addPolyline` 方法將其新增至地圖。 您可以使用 [`color`] 選項來設定筆觸色彩，筆劃寬度是使用 [寬度] 選項設定，而且可以使用 [`pattern`] 選項來設定筆劃虛線陣列。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps 的聚合線條](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，折線稱為 LineString 或 MultiLineString 物件。 這些物件可以加入至資料來源，並使用線條圖層來轉譯。 請注意，筆劃寬度和虛線陣列 "圖元" 單位會與中的 Azure 地圖服務 Web SDK 對齊，這兩個 Sdk 中使用相同的值會產生相同的結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure 地圖服務的折線](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>加入多邊形

多邊形是用來代表地圖上的區域。 下列範例示範如何根據地圖的中心座標，建立形成三角形的多邊形。

**之前： Google Maps**

使用 Google Maps 時，可以使用 `PolygonOptions` 類別來建立多邊形，並使用 `addPolygon` 方法將其新增至地圖。 您可以使用 [`fillColor`] 和 [`strokeColor`] 選項來設定填滿和筆觸色彩，筆劃寬度是使用 [`strokeWidth`] 選項所設定。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Maps 多邊形](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，可以將多邊形和 MultiPolygon 物件加入至資料來源，並使用圖層在地圖上轉譯。 多邊形的區域可以在多邊形圖層中呈現。 多邊形的外框可以使用線條圖層來呈現。 請注意，筆劃寬度和虛線陣列 "圖元" 單位會與中的 Azure 地圖服務 Web SDK 對齊，這兩個 Sdk 中使用相同的值會產生相同的結果。

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure 地圖服務多邊形](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>重迭磚圖層

圖格圖層（也稱為「Google 地圖」中的影像重迭）可讓您將已細分成較小磚影像的圖層影像，對齊地圖並排顯示系統。 這是覆迭圖層影像或非常大型資料集的常見方式。

下列範例會將氣象雷達圖圖層與愛荷華州州大學的愛荷華州環境 Mesonet 重迭。 磚的大小為256圖元。

**之前： Google Maps**

使用 Google Maps，磚圖層可以重迭在地圖頂端，使用 `TileOverlayOptions` 類別，並使用 `addTileLauer` 方法新增至地圖。 若要讓磚變成半透明，`transparency` 選項設定為0.2 或20% 透明。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Google Maps 磚圖層](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**之後： Azure 地圖服務**

在 Azure 地圖服務中，可以將圖格圖層新增至地圖，其方式與任何其他圖層大致相同。 具有 x、y 和 zoom 預留位置的格式化 URL;`{x}`、`{y}`、`{z}` 分別用來告訴圖層要存取磚的位置。 Azure 地圖服務中的圖格圖層也支援 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 預留位置。 若要讓磚圖層變成半透明，會使用不透明度值0.8。 請注意，不透明度和透明度（雖然類似）使用反向值。 若要在兩者之間進行轉換，只需從數位一減去其值即可。

> [!TIP]
> 在中，您可以輕鬆地將 Azure 地圖服務圖層轉譯為其他層級，包括基底地圖圖層。 通常最好是在地圖標籤下方轉譯磚圖層，使其易於閱讀。 `map.layers.add` 方法會接受第二個參數，也就是要在其中插入新圖層的圖層識別碼。 若要在地圖標籤下方插入磚圖層，可以使用下列程式碼： `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure 地圖服務圖格圖層](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>顯示交通流量

流量資料可以同時重迭 Azure 和 Google maps。

**之前： Google Maps**

使用 Google Maps，可以藉由將 true 傳遞至地圖的 `setTrafficEnabled` 方法，將流量資料重迭在地圖上方。

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps 流量](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**之後： Azure 地圖服務**

Azure 地圖服務提供數個不同的選項來顯示流量。 流量事件（例如路段和意外）可顯示為地圖上的圖示。 流量（色彩編碼道路）可以在地圖上重迭，而且可以根據正常的預期延遲或絕對延遲，將色彩修改為相對於張貼的速度限制。 Azure 地圖服務中的事件資料會每分鐘更新一次，並每隔兩分鐘傳送一個流量資料。

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure 地圖服務流量](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 地圖服務 Android SDK。

> [!div class="nextstepaction"]
> [如何使用 Android 地圖控制項](how-to-use-android-map-control-library.md)
