---
title: Azure 通知中樞設定百度雲端推播 |Microsoft Docs
description: 了解如何設定 Azure 通知中樞的百度。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234272"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 入口網站中設定通知中樞的百度雲端推播設定
這篇文章會示範如何使用 Azure 入口網站設定 Azure 通知中樞的百度雲端推播設定。 

## <a name="prerequisites"></a>必要條件
如果您尚未建立通知中樞，立即建立一個。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)。 

## <a name="configure-baidu-cloud-push"></a>設定百度雲端推播
下列程序可讓您設定百度雲端推播通知中樞的設定步驟：

1. 在 Azure 入口網站中，在**通知中樞**頁面上，選取**百度 （Android 中國）** 左側功能表上。 
2. 在百度雲推送專案中，輸入從百度主控台取得的 **API 金鑰**。 
3. 在百度雲推送專案中，輸入從百度主控台取得的**祕密金鑰**。 
4. 選取 [ **儲存**]。 

    ![顯示推播通知的百度 (Android China) 設定的通知中樞螢幕擷取畫面](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>後續步驟
如需教學課程的逐步指示將通知推送到百度中，使用 Azure 通知中樞和百度雲端推播，請參閱[透過百度開始使用通知中樞](notification-hubs-baidu-china-android-notifications-get-started.md)。
