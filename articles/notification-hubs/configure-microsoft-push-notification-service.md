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
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488203"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Microsoft 推播通知服務 (MPNS) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Microsoft 推播通知服務 (MPNS) 設定。 

## <a name="prerequisites"></a>必要條件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-microsoft-push-notification-service-mpns"></a>設定 Microsoft 推播通知服務 (MPNS)

下列程序可讓您針對通知中樞的 Microsoft 推播通知服務 (MPNS) 設定步驟： 

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Windows Phone (MPNS)** 左側功能表上。
1. 启用未经身份验证或经过身份验证的通知：

   a. 若要启用未经身份验证的推送通知，请选择“启用未经身份验证的推送” > “保存”。

      ![显示如何启用未经身份验证的推送通知的屏幕截图](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 启用经过身份验证的推送通知：
      * 在工具栏上选择“上传证书”。
      * 选择“文件”图标，然后选择证书文件。
      * 輸入憑證的密碼。
      * 選取 [確定] 。
      * 在“Windows Phone (MPNS)”页上选择“保存”。

## <a name="next-steps"></a>後續步驟
如需教學課程的逐步指示將通知推送到 Windows Phone 裝置，使用 Azure 通知中樞 」 和 「 Microsoft 推播通知服務 (MPNS)，請參閱[推播通知至 Windows Phone 應用程式使用通知中樞](notification-hubs-windows-mobile-push-notifications-mpns.md)。

