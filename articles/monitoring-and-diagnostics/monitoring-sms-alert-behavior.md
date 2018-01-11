---
title: "動作群組中的 SMS 警示行為 | Microsoft Docs"
description: "SMS 訊息格式和回應 SMS 訊息以取消訂閱、重新訂閱，或要求說明。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 012f001356463a8a7d9b95f186111959627f2c28
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>動作群組中的 SMS 警示行為
## <a name="overview"></a>概觀 ##
動作群組可讓您設定接收者清單。 然後這些群組就可在定義活動記錄警示時加以運用；確保觸發活動記錄警示時，特定的動作群組可以收到通知。 SMS 是其中一個可支援的警示機制；這些警示支援雙向通訊。 使用者可回應警示以執行下列動作：

- **取消訂閱警示：**使用者可取消訂閱所有動作群組或單一動作群組的 SMS 警示。  
- **重新訂閱警示：**使用者可重新訂閱所有動作群組或單一動作群組的 SMS 警示。  
- **要求說明︰**使用者可在 SMS 上要求其他資訊。 系統會將他們導向本文

本文涵蓋 SMS 警示的行為，以及使用者可依據其所在位置採取的回應動作：

## <a name="usacanada-sms-behavior"></a>美國/加拿大 SMS 行為
### <a name="receiving-an-sms-alert"></a>接收 SMS 警示
設定成動作群組一部分的 SMS 收件者，將會在警示啟動時收到 SMS。 SMS 會傳達以下資訊：
* 會收到此警示的動作群組簡稱
- 警示的標題

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>取消訂閱一個動作群組的 SMS 警示
使用者可以取消訂閱 SMS 一個動作群組的警示來回應以關鍵字的簡短程式碼 29873: 「 停用&lt;動作群組的簡短名稱&gt;"。

例如 使用者想要取消訂閱 shortname"Azure"，動作群組的警示就會傳送 SMS 的簡短程式碼 29873 說 「 停用 Azure 」

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>取消訂閱所有動作群組的 SMS 警示
使用者可以取消訂閱的所有動作群組的所有 SMS 警示所回應的簡短程式碼與任何下列關鍵字 29873:
* STOP

例如 使用者想要取消訂閱所有的動作群組的所有 SMS 警示就會傳送 SMS 的簡短程式碼 29873 說 「 停止 」

>[!NOTE]
>如果使用者已取消訂閱 SMS 警示，但又加入新的動作群組；則他們會接收到新動作群組的 SMS 警示，但對於所有之前的動作群組，仍是取消訂閱狀態。
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>重新訂閱一個動作群組的 SMS 警示
使用者可以重新訂閱 sms 一個動作群組的警示來回應以關鍵字的簡短程式碼 29873: 「 啟用&lt;動作群組的簡短名稱&gt;"。

例如 使用者想要重新訂閱 shortname"Azure"，動作群組的警示就會傳送 SMS 的簡短程式碼 29873，指出 「 啟用 Azure 」

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>重新訂閱所有動作群組的 SMS 警示
使用者可以重新訂閱警示之所有動作群組的所有 sms，簡短的程式碼與任何下列關鍵字 29873 回應：

* START

例如 使用者想要取消訂閱所有的動作群組的所有 SMS 警示就會傳送 SMS 的簡短程式碼 29873 說 「 啟動 」

### <a name="requesting-help-via-sms"></a>透過 SMS 要求說明
使用者可以要求它們收到回應簡短的程式碼與任何下列關鍵字 29873 SMS 的相關詳細資訊：
* HELP

使用者將會收到包含本文連結的回應。

## <a name="next-steps"></a>後續步驟
取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示  
深入了解 [SMS 速率限制](monitoring-alerts-rate-limiting.md)  
深入了解[動作群組](monitoring-action-groups.md)
