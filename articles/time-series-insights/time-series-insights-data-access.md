---
title: 設定安全性以存取和管理 Azure 時間序列深入解析 | Microsoft Docs
description: 本文說明如何設定安全性和權限作為管理存取原則和資料存取原則，以保護 Azure 時間序列深入解析。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364806"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>使用 Azure 入口網站授與 Time Series Insights 環境的資料存取權

Time Series Insights 環境有兩種獨立的存取原則︰

* 管理存取原則
* 資料存取原則

這兩種原則可將特定環境的各種權限授與給 Azure Active Directory 主體 (使用者和應用程式)。 主體 (使用者和應用程式) 必須屬於與含有此環境之訂用帳戶相關聯的 Active Directory (也稱為 Azure 租用戶)。

管理存取原則可授與環境設定相關權限，例如
*   建立和刪除環境、事件來源、參考資料集，以及
*   管理資料存取原則。

資料存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。

這兩種原則能夠清楚區隔環境管理存取權與環境內資料存取權。 比方說，可以設定環境，以便移除環境擁有者/建立者的資料存取權。 此外，獲准讀取環境中資料的使用者與服務，可能無法存取環境的組態。

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>後續步驟

* 了解[如何將事件中樞的事件來源新增至 Azure 時間序列深入解析環境](time-series-insights-how-to-add-an-event-source-eventhub.md)。
* [將事件傳送](time-series-insights-send-events.md)到事件來源。
* 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中檢視您的環境。
