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
ms.openlocfilehash: a33812dedaed81a1ed0b6fca8285f70f44decced
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198907"
---
1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]。 

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 [將 Firebase 新增至 Android 應用程式] 頁面上，執行下列步驟： 
    1. 對於 [Android 套件名稱]，複製應用程式 **build.gradle** 檔案的 **applicationId** 值。 在此範例中為 `com.fabrikam.fcmtutorial1app`。 

        ![指定套件名稱](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 選取 [註冊應用程式]。 
4. 選取 [下載 google-services.json]，將檔案儲存到專案的**應用程式**資料夾，然後選取 [下一步]。 

    ![下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. 在 Android Studio 中對於專案進行下列**設定變更**。 
    1.  在**專案層級 build.gradle**檔案 (&lt;專案&gt;/build.gradle) 中，將下列陳述式加入到**相依性**區段。 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. 在**應用程式層級 build.gradle**檔案 &lt;project&gt;/&lt;app-module&gt;/build.gradle) 中，將下列陳述式加入到**相依性**區段。 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. 將下行新增至**應用程式層級 build.gradle** 檔案結尾的相依性區段之後。 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. 在工具列上選取 [立即同步]*。 
 
        ![build.gradle 組態變更](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. 選取頁面上的 [下一步]。 
7. 選取頁面上的 [略過此步驟]。 

    ![略過最後一個步驟](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]。

    ![選取專案設定](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果您尚未將 **google-services.json** 檔案下載到 Android Studio 專案的**應用程式**資料夾，您可以在此頁面下載。 
5. 切換到頂端的 [雲端通訊] 索引標籤。 
6. 複製並儲存**伺服器金鑰**以供稍後使用。 您可使用此值來設定通知中樞。
