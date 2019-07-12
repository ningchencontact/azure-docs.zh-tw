---
title: Azure 地圖服務中的地圖樣式功能 | Microsoft Docs
description: 深入了解 Azure Maps 樣式 Android SDK 的相關功能。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870968"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>設定使用 Azure 地圖服務的 Android SDK 的地圖樣式

本文將說明兩種方式可設定使用 Azure 地圖服務的 Android SDK 的地圖樣式。 Azure 地圖服務具有六個不同的地圖樣式，可以選擇。 如需有關支援的地圖樣式的詳細資訊，請參閱[支援 Azure Maps 的地圖樣式](./supported-map-styles.md)。


## <a name="prerequisites"></a>先決條件

若要完成這篇文章中的程序，必須先安裝[Azure 地圖服務的 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)載入對應。


## <a name="set-map-style-in-the-layout"></a>設定版面配置中的地圖樣式

您可以在活動類別的配置檔案中設定地圖樣式。 編輯**res > 配置 > activity_main.xml**，讓它看起來像下面這樣：

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`上述的屬性設定地圖樣式**grayscale_dark**。 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>在 活動類別中設定地圖樣式

在 活動類別，可以設定地圖樣式。 複製下列程式碼片段**onCreate()** 方法的程式`MainActivity.java`類別。 這會設定為的地圖樣式**satellite_road_labels**。

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>