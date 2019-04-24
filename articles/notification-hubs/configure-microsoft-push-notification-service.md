---
title: 在 Azure 通知中樞中設定 Microsoft 推播通知服務 |Microsoft Docs
description: 了解如何設定 Azure 通知中樞的 Microsoft 推播通知服務設定。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240321"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Microsoft 推播通知服務 (MPNS) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Microsoft 推播通知服務 (MPNS) 設定。 

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-microsoft-push-notification-service-mpns"></a>設定 Microsoft 推播通知服務 (MPNS)

下列程序可讓您針對通知中樞的 Microsoft 推播通知服務 (MPNS) 設定步驟： 

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Windows Phone (MPNS)** 左側功能表上。
1. 啟用未經驗證或已驗證的推播通知：

   a. 若要啟用未經驗證的推播通知，請選取 [啟用未經驗證的推播通知]  >  [儲存]。

      ![顯示如何啟用未經驗證的推播通知的螢幕擷取畫面](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 啟用已驗證推播通知：
      * 在工具列上選取 [上傳憑證]。
      * 選取檔案圖示，然後選取憑證檔案。
      * 輸入憑證的密碼。
      * 選取 [確定] 。
      * 在 [Windows Phone (MPNS)] 頁面上，選取 [儲存]。

## <a name="next-steps"></a>後續步驟
如需教學課程的逐步指示將通知推送到 Windows Phone 裝置，使用 Azure 通知中樞 」 和 「 Microsoft 推播通知服務 (MPNS)，請參閱[推播通知至 Windows Phone 應用程式使用通知中樞](notification-hubs-windows-mobile-push-notifications-mpns.md)。

