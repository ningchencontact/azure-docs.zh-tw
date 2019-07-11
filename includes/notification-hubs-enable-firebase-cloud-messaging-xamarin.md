---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509820"
---
1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]  。 

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. 在 [將 Firebase 新增至 Android 應用程式]  頁面上，採取下列步驟： 
1. 
    1. 針對 [Android 套件名稱]  ，請輸入套件的名稱。 例如： `tutorials.tutoria1.xamarinfcmapp` 。 

        ![指定套件名稱](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. 選取 [註冊應用程式]  。 
1. 
1. 選取 [下載 google-services.json]  。 然後將檔案儲存到專案的 **app** 資料夾，再選取 [下一步]  。 

    ![下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. 選取 [下一步]  。 
7. 選取 [略過此步驟]  。 

    ![略過最後一個步驟](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]  。

    ![選取專案設定](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. 如果尚未下載 **google-services.json** 檔案，您可以在此頁面下載。 

1. 切換到頂端的 [雲端通訊]  索引標籤。 

1. 複製並儲存**舊版伺服器金鑰**以供後續使用。 您可使用此值來設定通知中樞。
