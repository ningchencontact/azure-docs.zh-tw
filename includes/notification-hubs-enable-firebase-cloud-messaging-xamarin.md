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
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162182"
---
1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 建立專案之後，請選取 [將 Firebase 新增至 Android 應用程式]。 

    ![將 Firebase 新增至 Android 應用程式](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 [將 Firebase 新增至 Android 應用程式] 頁面上，執行下列步驟： 
    1. 針對 [Android 套件名稱]，請輸入套件的名稱。 例如： `tutorials.tutoria1.xamarinfcmapp` 。 

        ![指定套件名稱](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. 選取 [註冊應用程式]。 
4. 選取 [下載 google-services.json]，將檔案儲存到專案的**應用程式**資料夾，然後選取 [下一步]。 

    ![下載 google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. 選取頁面上的 [下一步]。 
7. 選取頁面上的 [略過此步驟]。 

    ![略過最後一個步驟](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定]。

    ![選取專案設定](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 如果尚未下載 **google-services.json** 檔案，您可以在此頁面下載。 
5. 切換到頂端的 [雲端通訊] 索引標籤。 
6. 複製並儲存**伺服器金鑰**以供稍後使用。 您可使用此值來設定通知中樞。
