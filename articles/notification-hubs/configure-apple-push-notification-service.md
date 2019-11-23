---
title: Configure Apple Push Notification Service in Azure Notification Hubs | Microsoft Docs
description: Learn how to configure an Azure notification hub with Apple Push Notification Service (APNS) settings.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406113"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure Apple Push Notification Service (APNS) settings for a notification hub in the Azure portal
This article shows you how to configure Apple Push Notification Service (APNS) settings for an Azure notification hub by using the Azure portal. 

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-apple-push-notification-service"></a>Configure Apple Push Notification Service

The following procedure gives you steps to configure Apple Push Notification Service (APNS) settings for a notification hub:

1. In the Azure portal, on the **Notification Hub** page, select **Apple (APNS)** on the left menu.

1. 針對 [驗證模式]，選取 [憑證] 或 [權杖]。

   a. 如果您選取 [憑證]：
   * 選取檔案圖示，然後選取您要上傳的 [.p12] 檔案。
   * 輸入密碼。
   * 選取 [沙箱] 模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產] 模式。

     ![Azure 入口網站中的 APNS 憑證設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b.這是另一個 C# 主控台應用程式。 如果您選取 [權杖]：

   * 輸入 [金鑰識別碼]、[搭售方案識別碼]、[小組識別碼] 和 [權杖] 的值。
   * 選取 [沙箱] 模式。 或者，若要傳送推播通知給從市集購買應用程式的使用者，請選取 [生產] 模式。

     ![Azure 入口網站中的 APNS 權杖設定螢幕擷取畫面](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>後續步驟
For a tutorial with step-by-step instructions for pushing notifications to iOS devices, see the following article: [Push notifications to iOS devices by using Notification Hubs and APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
