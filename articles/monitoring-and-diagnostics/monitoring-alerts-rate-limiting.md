---
title: "SMS、電子郵件、Azure App 推播通知和 Webhook 的速率限制 | Microsoft Docs"
description: "了解 Azure 如何限制可能來自動作群組的 SMS、電子郵件、Azure App 推送或 Webhook 通知的數目。"
author: dkamstra
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
ms.date: 2/16/2018
ms.author: dukek
ms.openlocfilehash: a4f97cc79945d266edd0af577e37fc9da2aa97bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>SMS 訊息、電子郵件、Azure App 推播通知和 Webhook 的速率限制
速率限制是當傳送了太多通知給特定電話號碼、電子郵件地址或裝置時所發生的通知暫停。 速率限制可確保警示為可管理且可採取動作。

以下項目的速率限制閾值：

 - **SMS**：每 5 分鐘不超過 1 個 SMS。
 - **電子郵件**：一小時內不超過 100 個電子郵件。
 
 其他動作不受速率限制。

## <a name="rate-limit-rules"></a>速率限制規則
- 當特定電話號碼或電子郵件收到的訊息超過閾值允許數量時，會受到速率限制。
- 電話號碼或電子郵件可以是跨許多訂用帳戶動作群組的一部分。 速率限制適用於所有訂用帳戶。 它適用於觸達閾值時，即使是從多個訂用帳戶傳送訊息。
- 當電子郵件地址受到速率限制時，將會傳送另一個通知來溝通速率限制。 通知會說明速率限制到期的時間。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)。
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。
