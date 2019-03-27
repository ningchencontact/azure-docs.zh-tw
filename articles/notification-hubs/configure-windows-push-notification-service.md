---
title: 在 Azure 通知中樞中設定 Windows 推播通知服務 |Microsoft Docs
description: 了解如何設定 Azure 通知中樞的推播通知服務的 Windows 設定。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488200"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的 Windows 推播通知服務 (WNS) 設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的 Windows 通知服務 (WNS) 設定。  

## <a name="prerequisites"></a>必要條件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-windows-push-notification-service-wns"></a>設定 Windows 推播通知服務 (WNS)

下列程序可讓您設定 Windows 推播通知服務 (WNS) 通知中樞的步驟： 

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**Windows (WNS)** 左側功能表上。
2. 輸入 [套件 SID] 和 [安全性金鑰] 的值。
3. 選取 [ **儲存**]。

   ![显示“包 SID”和“安全密钥”框的屏幕截图](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>後續步驟
如需教學課程的逐步指示將通知推送到通用 Windows 平台應用程式中，使用 Azure 通知中樞和 Windows 推播通知服務 (WNS)，請參閱[藉由使用 Azure 時，將通知傳送至 UWP 應用程式通知中樞](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。


