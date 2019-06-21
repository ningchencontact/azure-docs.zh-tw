---
title: 開始使用 Azure 地圖服務中的 Android 的地圖控制項 |Microsoft Docs
description: Android 中的地圖控制項 Azure 地圖服務。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273841"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>開始使用 Azure 地圖服務的 Android SDK

Azure 地圖服務的 Android SDK 是適用於 Android 的向量地圖庫。 這篇文章會引導您完成安裝 Azure 地圖服務的 Android SDK 及載入對應的處理程序。

## <a name="prerequisites"></a>必要條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

若要完成這篇文章中的程序，您必須先[建立 Azure 地圖服務帳戶](how-to-manage-account-keys.md)S1 定價層中。

### <a name="download-android-studio"></a>下載 Android Studio

您需要下載 Android Studio，並建立專案，而空的活動，然後再安裝 Azure 地圖服務的 Android SDK。 您可以[下載 Android Studio](https://developer.android.com/studio/)從 Google 免費。 

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中建立專案

首先，您必須使用空的活動建立新的專案。 完成這些步驟來建立 Android Studio 專案：

1. 底下**選擇您的專案**，選取**手機和平板電腦**。 您的應用程式將執行此表單係數。
2. 在 [**手機和平板電腦**索引標籤上，選取**空的活動**，然後選取**下一步]** 。
3. 在 [設定專案]  底下，選取 `API 21: Android 5.0.0 (Lollipop)` 作為最低版本的 SDK。 這是支援 Azure 地圖服務的 Android SDK 的最早版本。
4. 接受預設值`Activity Name`並`Layout Name`，然後選取**完成**。

請參閱[Android Studio 文件](https://developer.android.com/studio/intro/)取得更多協助安裝 Android Studio，並建立新的專案。

![建立專案](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>設定虛擬裝置

Android Studio 可讓您在電腦上設定虛擬 Android 裝置。 如此一來，可協助您測試您的應用程式，在開發期間。 若要設定虛擬裝置，您專案的畫面，右上角選取 [Android 虛擬裝置 (AVD) 管理員] 圖示，然後按**建立虛擬裝置**。 您也可以藉由選取來取得 AVD Manager**工具** > **Android** > **AVD Manager**從工具列。 在 [**手機**類別目錄中，選取**Nexus 5 X**，然後選取**下一步]** 。

您可以深入了解設定在 AVD [Android Studio 文件](https://developer.android.com/studio/run/managing-avds)。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure 地圖服務的 Android SDK 安裝

建置您的應用程式的下一個步驟是安裝 Azure 地圖服務的 Android SDK。 完成這些步驟來安裝 SDK:

1. 開啟最上層**build.gradle**檔案，並新增下列程式碼**所有專案**，**存放庫**封鎖區段：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新您**app/build.gradle**並加入下列程式碼：
    
    1. 請確定您的專案**minSdkVersion** API 21 或更新版本。

    2. 將下列程式碼新增至 [Android] 區段中：

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 更新您的相依性的區塊，並新增新的實作相依性行適用於最新的 Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure 地圖服務的 Android SDK 定期正在升級，並增強。 您所見[開始使用 Android 的地圖控制項](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)文件，以取得最新的 Azure 地圖服務實作的版本號碼。 您也可以設定的版本號碼"0.2"從"0 +"，讓它一律指向最新版本。

3. 編輯**res** > **版面配置** > **activity_main.xml**並取代為下列：
    
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

4. 在  **MainActivity.java**您需要的檔案：
    
    * 新增匯入適用於 Azure 地圖服務 SDK
    * 設定您 Azure 地圖服務的驗證資訊
    * 取得地圖控制項執行個體中**onCreate**方法

    在全球使用的方法中的 setSubscriptionKey 或 setAadProperties AzureMaps 類別上設定的驗證資訊之後，您就不必在每個檢視中新增驗證資訊。 地圖控制項包含它自己的生命週期方法管理 Android 的 OpenGL 生命週期，這必須直接呼叫包含的活動。 為了讓您的應用程式正確地呼叫地圖控制項的生命週期方法，您必須覆寫下列活動，其中包含地圖控制項中的生命週期方法，並呼叫各自的地圖控制項方法。 

    編輯**MainActivity.java**檔案，如下所示：
    
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

<center>

![Android 的對應](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>後續步驟

了解如何在地圖上新增覆疊的資料：

> [!div class="nextstepaction"]
> [將符號圖層加入至 Android 的對應](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [將圖形新增至 Android 的對應](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Android 的對應中的變更地圖樣式](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
