---
title: "速率限制的 SMS、 電子郵件、 Azure 應用程式的推播通知及 webhook |Microsoft 文件"
description: "了解 Azure 如何限制的可能 SMS、 電子郵件、 從動作群組的 Azure 應用程式推入或 webhook 通知數目。"
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>速率限制的簡訊、 電子郵件、 Azure 應用程式的推播通知及 webhook 文章
速率限制是太多的通知傳送給特定的電話號碼、 電子郵件地址或裝置時，就會發生暫止的通知。 速率限制可確保警示為可管理且可採取動作。

速率限制臨界值如下：

 - **SMS**： 超過 1 SMS 每隔 5 分鐘。
 - **電子郵件**：1 小時 100 個訊息。
 - **Azure 應用程式的推播通知**： 沒有任何限制的推播通知的速率。
 - **Webhook**： 沒有任何速率限制的 webhook。

## <a name="rate-limit-rules"></a>速率限制規則
- 當特定電話號碼或電子郵件收到的訊息超過閾值允許數量時，會受到速率限制。
- 電話號碼或電子郵件可以是跨許多訂用帳戶動作群組的一部分。 速率限制適用於所有訂用帳戶。 它適用於觸達閾值時，即使是從多個訂用帳戶傳送訊息。  
- 速率限制電子郵件地址時，其他的通知會傳送速率限制的通訊。 通知會說明速率限制到期的時間。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)。
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。
