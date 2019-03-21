---
title: 如何使用 Azure 地圖服務中的 Android 的地圖控制項 |Microsoft Docs
description: Android 中的地圖控制項 Azure 地圖服務。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010662"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>如何使用 Azure 地圖服務的 Android SDK

Azure 地圖服務的 Android SDK 是適用於 Android 的向量地圖庫。 這篇文章會引導您完成安裝 Azure 地圖服務的 Android SDK、 載入對應，以及將 pin 放在地圖上的處理程序。

## <a name="prerequisites"></a>必要條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

若要完成這篇文章中的程序，您必須先[建立 Azure 地圖服務帳戶](how-to-manage-account-keys.md)S1 定價層中。

### <a name="download-android-studio"></a>下載 Android Studio

您需要下載 Android Studio，並建立專案，而空的活動，才能安裝 Azure 地圖服務的 Android SDK。 您可以[下載 Android Studio](https://developer.android.com/studio/)從 Google 免費。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

首先，您必須使用空的活動建立新的專案。 完成這些步驟來建立 Android Studio 專案：

1. 底下**選擇您的專案**，選取**手機和平板電腦**。 您的應用程式將執行此表單係數。
2. 在 [**手機和平板電腦**索引標籤上，選取**空的活動**，然後選取**下一步]**。
3. 在 [設定專案] 底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。 這是支援 Azure 地圖服務的 Android SDK 的最早版本。
4. 接受預設值`Activity Name`並`Layout Name`，然後選取**完成**。

請參閱[Android Studio 文件](https://developer.android.com/studio/intro/)取得更多協助安裝 Android Studio，並建立新的專案。

![建立專案](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>設定虛擬裝置

Android Studio 可讓您在電腦上設定虛擬 Android 裝置。 如此一來，可協助您測試您的應用程式，在開發期間。 若要設定虛擬裝置，您專案的畫面，右上角選取 [Android 虛擬裝置 (AVD) 管理員] 圖示，然後按**建立虛擬裝置**。 您也可以藉由選取來取得 AVD Manager**工具** > **Android** > **AVD Manager**從工具列。 在 [**手機**類別目錄中，選取**Nexus 5 X**，然後選取**下一步]**。

您可以深入了解設定在 AVD [Android Studio 文件](https://developer.android.com/studio/run/managing-avds)。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure 地圖服務的 Android SDK 安裝

建置您的應用程式的下一個步驟是安裝 Azure 地圖服務的 Android SDK。 完成這些步驟來安裝 SDK:

1. 將下列程式碼加入**所有專案**，**存放庫**封鎖您**build.gradle**檔案。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新您**app/build.gradle**並加入下列程式碼：

    1. Android 的區塊中加入下列程式碼：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 更新您的相依性的區塊，並加入下列程式碼：

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 藉由新增下列 XML 來設定權限您**AndroidManifest.xml**檔案：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 編輯**res** > **版面配置** > **activity_main.xml**讓它看起來像這個 XML:
    
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

5. 編輯 **MainActivity.java** 以建立地圖檢視活動類別。 您在編輯之後，它看起來應該像此類別：

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

完成上述步驟之後，您可能將一些程式碼的相關取得 Android Studio 的警告。 若要解決這些警告，匯入中所參考的類別`MainActivity.java`。

您自動選取 Alt + Enter （選項 + 在 Mac 上的返回） 匯入這些類別。

下列圖形 （或按下控制項 + R，在 Mac 上），來建置您的應用程式中所示，請選取 [執行] 按鈕。

![按一下 [執行]](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio 中將會需要幾秒鐘的時間來建置應用程式。 建置完成之後，您可以在模擬的 Android 裝置來測試您的應用程式。 您應該會看到與下列類似的對應：

![Android 地圖](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>在地圖上新增標記

若要將標記加入至您的地圖，新增`mapView.getMapAsync()`函式以`MainActivity.java`。 最終`MainActivity.java`程式碼應該看起來像這樣：

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

執行您的應用程式一次。 您應該在地圖上，看到標記，如下所示：

![Android 地圖釘選圖示](./media/how-to-use-android-map-control-library/android-map-pin.png)