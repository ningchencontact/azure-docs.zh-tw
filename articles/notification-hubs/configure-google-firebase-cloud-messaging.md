---
title: 設定 Google Firebase 雲端傳訊的 Azure 通知中樞 |Microsoft Docs
description: 了解如何使用 Google Firebase 雲端通訊的設定來設定 Azure 通知中樞。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488207"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Google Firebase 雲端通訊 (FCM) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Google Firebase 雲端通訊 (FCM) 設定。  

## <a name="prerequisites"></a>必要條件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>設定 Google Firebase 雲端通訊 (FCM)

下列程序可讓您針對通知中樞的 Google Firebase 雲端通訊 (FCM) 設定步驟： 

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Google (GCM/FCM)** 左側功能表上。 
2. 粘贴前面保存的 FCM 项目“API 密钥”。 
3. 選取 [ **儲存**]。 

   ![显示如何为 Google FCM 配置通知中心的屏幕截图](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>後續步驟
如需教學課程逐步解說使用 Azure 通知中樞和 Google Firebase 雲端通訊時，將通知推送到 Android 裝置，請參閱[使用通知中樞和 Google FCM 推播通知給 Android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md).

