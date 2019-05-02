---
title: 新增對應中 Azure 地圖服務的 Android 符號圖層 |Microsoft Docs
description: 如何將符號加入至對應，使用 Azure 地圖服務的 Android SDK
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871073"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>將符號圖層加入至對應，使用 Azure 地圖服務的 Android SDK

這篇文章會示範如何將點從資料來源的資料呈現為符號圖層上使用 Azure 地圖服務的 Android SDK 的對應。

## <a name="prerequisites"></a>必要條件

若要完全遵循這篇文章中的步驟，您需要安裝[Azure 地圖服務的 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)載入對應。

## <a name="add-a-symbol-layer"></a>新增符號圖層

若要使用的符號圖層的地圖上新增標記，請遵循下列步驟：

1. 編輯**res** > **版面配置** > **activity_main.xml**讓它看起來類似以下 XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. 複製下列程式碼片段**onCreate()** 方法的程式`MainActivity.java`類別。

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    上述程式碼片段會先取得 Azure 地圖服務地圖控制項執行個體使用**onReady()** 回呼方法。 然後它會建立資料來源物件使用**DataSource**類別，並將它加入至地圖。 接著它會加入**功能**包含點的幾何，給它。 紅色標記影像接著會設定成為符號的圖示。 A**符號層**使用文字或圖示來呈現點為基礎的資料，包裝資料來源中為地圖上的符號。 接著會建立符號層和資料來源傳遞給它轉譯，並接著會新增至地圖圖層。
    
    新增上述程式碼片段之後您`MainActivity.java`看起來應該像下面這樣：
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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
    
此時，如果執行您的應用程式，您應該會看到標記在地圖上，如下所示：

<center>

![Android 的地圖釘選](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>後續步驟

若要將更多產品加入至您的對應，請參閱：

> [!div class="nextstepaction"]
> [將圖形新增至 Android 的對應](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)