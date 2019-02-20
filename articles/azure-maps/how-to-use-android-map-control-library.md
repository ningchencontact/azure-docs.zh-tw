---
title: 如何在 Azure 地圖服務中使用 Android 地圖控制項 | Microsoft Docs
description: 在 Azure 地圖服務中使用 Android 地圖控制項。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3f7579324e1218cc2e2c3594889db776da6e529
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119032"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>如何使用 Azure 地圖服務 Android SDK

Azure 地圖服務 Android SDK 是適用於 Android 的向量地圖庫。 本文會引導您完成安裝 Azure 地圖服務 Android SDK、載入地圖並於其上放置釘選圖示的程序。

## <a name="prerequisites-to-get-started"></a>開始使用的必要條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶 

若要遵循本指南中的步驟，您必須先查看[管理帳戶和金鑰](how-to-manage-account-keys.md)來建立及管理具有 S1 定價層的訂用帳戶。

### <a name="download-android-studio"></a>下載 Android Studio

您可以從 Google 免費下載 [Android Studio](https://developer.android.com/studio/)。 若要安裝 Azure 地圖服務 Android SDK，您必須先下載 Android Studio，並建立具有空白活動的專案。

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

您必須建立具有空白活動的新專案。 請遵循下列步驟來建立新的 Azure Container 專案：

1. 在 [選擇專案] 底下，核取 [電話和平板電腦] 作為應用程式會執行所在的板型規格。
2. 按一下板型規格底下的 [空白活動]，然後按 [下一步]。
3. 在 [設定專案] 底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。 這是 Azure 地圖服務 Android SDK 所支援的最低版本。
4. 接受預設值 `Activity Name` 和 `Layout Name`，然後按一下 [完成]。

請參閱 [Android Studio 文件](https://developer.android.com/studio/intro/)，以取得更多如何安裝 Android Studio 並建立新專案的說明。

![建立新專案](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>設定虛擬裝置

Android Studio 可讓您在電腦上設定虛擬 Android 裝置。 這可協助您在開發時測試您的應用程式。 若要設定虛擬裝置，請按一下專案畫面右上方的 [Android 虛擬裝置 (AVD) 管理員] 圖示。 然後按一下 [建立虛擬裝置] 按鈕。 您也可以在工具列中透過 [工具] > [Android] > [AVD 管理員] 來移至管理員。 從 [電話] 目錄中選取 [Nexus 5X]，然後按 [下一步]。

請至 [Android Studio 文件](https://developer.android.com/studio/run/managing-avds) 深入了解如何設定 AVD。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>安裝 Azure 地圖服務 Android SDK

在開始建置應用程式之前，請先遵循下列步驟來安裝 Azure 地圖服務 Android SDK。 

1. 將下列內容新增至 **allprojects** (**build.gradle** 檔案中的存放庫區塊)。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle**，並於其中新增下列內容：

    1. 將下列內容新增至 Android 區塊：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 更新相依性區塊，並於其中新增下列內容：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 將下列內容新增至 'AndroidManifest.xml' 來設定權限

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 編輯 **res > layout > activity_main.xml**，讓它看起來像下面的 XML：
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. 編輯 **MainActivity.java** 以建立地圖檢視活動類別。 在編輯過後，其看起來應該會像下列類別：

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
        
        static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>匯入類別

完成上述步驟之後，您非常有可能會從 Android Studio 收到警告，內容是關於程式碼中的一些文字。 若要處理此警告，您必須匯入一些會在 `MainActivity.java` 中參考的類別。

您可以藉由按下 `Alt`+`Enter` (Mac 上則為 `Option`+`Return`) 來自動匯入這些類別。 

按一下 [執行「應用程式」] 按鈕 (Mac 上則為 `Control`+`R`) 來建置您的應用程式。

![按一下 [執行]](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 需要幾秒鐘的時間來建置應用程式。 建置完成後，您就可以在模擬的 Android 裝置中測試您的應用程式。 您會看到類似以下的地圖。

![Android 地圖](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>在地圖上新增標記

若要在地圖上新增標記，請將 `mapView.getMapAsync()` 函式新增至 `MainActivity.java`。 最終的 `MainActivity.java` 看起來應該會像下面這樣：

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

重新執行您的應用程式，您應該就會在地圖上看到類似下圖的標記。

![Android 地圖釘選圖示](./media/how-to-use-android-map-control-library/android-map-pin.png)