---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156855"
---
1. 在 [Android Studio] 中，選取 [工具] 功能表，然後選取 [SDK 管理員]。 
2. 選取您專案中使用的 Android SDK 目標版本，然後選取 [顯示套件詳細資料]。 

    ![Android SDK 管理員 - 選取目標版本](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 如果尚未安裝 [Google API]，請加以選取。

    ![Android SDK Manager - 已選取 Google API](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. 切換到 [SDK 工具] 索引標籤。如果您尚未安裝 Google Play Services，請選取 [Google Play Services]，如下圖所示。 然後按一下 [套用] 來安裝。 請注意在稍後步驟中使用的 SDK 路徑。

    ![Android SDK Manager - 已選取 Google Play Services](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. 如果您看到 [確認變更] 對話方塊，請選取 [確定]。 元件安裝程式會安裝要求的元件。 在安裝元件後選取 [完成]。
4. 選取 [確定] 以關閉 [新專案的設定] 對話方塊。  
5. 開啟 **app** 目錄中的 `build.gradle` 檔案，然後在`dependencies` 底下新增這一行。 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. 選取工具列中的 [立即同步] 圖示。

    ![與 Gradle 同步](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. 開啟 **AndroidManifest.xml** 並將此標記新增至「應用程式」標記。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
