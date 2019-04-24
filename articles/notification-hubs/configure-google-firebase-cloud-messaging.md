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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239190"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Google Firebase 雲端通訊 (FCM) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Google Firebase 雲端通訊 (FCM) 設定。  

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>設定 Google Firebase 雲端通訊 (FCM)

下列程序可讓您針對通知中樞的 Google Firebase 雲端通訊 (FCM) 設定步驟： 

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Google (GCM/FCM)** 左側功能表上。 
2. 針對您稍早儲存的 FCM 專案，貼上 **API 金鑰**。 
3. 選取 [ **儲存**]。 

   ![顯示如何設定 Google FCM 的通知中樞的螢幕擷取畫面](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>後續步驟
如需教學課程逐步解說使用 Azure 通知中樞和 Google Firebase 雲端通訊時，將通知推送到 Android 裝置，請參閱[使用通知中樞和 Google FCM 推播通知給 Android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md).

