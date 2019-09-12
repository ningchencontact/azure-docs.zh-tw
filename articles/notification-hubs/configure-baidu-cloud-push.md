---
title: 在 Azure 通知中樞中設定百度 Cloud Push |Microsoft Docs
description: 瞭解如何設定 Azure 通知中樞的百度設定。
services: notification-hubs
author: jwargo
manager: patniko
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9feb5f66c5ae979a239c3349b2b1347ad307ce49
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884656"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>不再在 Azure 入口網站中設定通知中樞的百度雲端推送設定

本文說明如何使用 Azure 入口網站來設定 Azure 通知中樞的百度 Cloud Push 設定。

> [!IMPORTANT]
> 本教學課程已被取代。 

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-baidu-cloud-push"></a>設定百度雲端推播
下列程式提供設定通知中樞之百度雲端推播設定的步驟：

1. 在 Azure 入口網站的 [**通知中樞**] 頁面上，選取左側功能表上的 [**百度（Android China）** ]。 
2. 在百度雲推送專案中，輸入從百度主控台取得的 **API 金鑰**。 
3. 在百度雲推送專案中，輸入從百度主控台取得的**祕密金鑰**。 
4. 選取 [ **儲存**]。 

    ![顯示推播通知的百度 (Android China) 設定的通知中樞螢幕擷取畫面](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>後續步驟
如需教學課程，其中包含使用 Azure 通知中樞和百度 Cloud Push 將通知推送至百度的逐步指示，請參閱[使用百度開始使用通知中樞](notification-hubs-baidu-china-android-notifications-get-started.md)。
