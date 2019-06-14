---
title: 設定 Azure 通知中樞中的 Apple 推播通知服務 |Microsoft Docs
description: 了解如何使用 Apple Push Notification Service (APNS) 設定來設定 Azure 通知中樞。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237818"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Apple Push Notification Service (APNS) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Apple Push Notification Service (APNS) 設定。 

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-apple-push-notification-service"></a>設定 Apple 推播通知服務

下列程序可讓您針對通知中樞的 Apple Push Notification Service (APNS) 設定步驟：

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Apple (APNS)** 左側功能表上。

1. 針對 [驗證模式]  ，選取 [憑證]  或 [權杖]  。

   a. 如果您選取 [憑證]  ：
   * 選取檔案圖示，然後選取您要上傳的 [.p12]  檔案。
   * 輸入密碼。
   * 選取 [沙箱]  模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產]  模式。

     ![Azure 入口網站中的 APNS 憑證設定螢幕擷取畫面](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. 如果您選取 [權杖]  ：

   * 輸入 [金鑰識別碼]  、[搭售方案識別碼]  、[小組識別碼]  和 [權杖]  的值。
   * 選取 [沙箱]  模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產]  模式。

     ![Azure 入口網站中的 APNS 權杖設定螢幕擷取畫面](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>後續步驟
如需教學課程逐步解說將通知推送到 iOS 裝置，請參閱下列文章：[使用通知中樞和 APNS 將通知推播至 iOS 裝置](notification-hubs-ios-apple-push-notification-apns-get-started.md)
