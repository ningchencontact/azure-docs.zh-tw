---
title: Azure 診斷記錄概觀
description: 深入了解 Azure 監視器] 和 [如何使用它們來了解 Azure 資源內發生的事件中的 Azure 診斷記錄檔。
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244869"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Azure 診斷記錄概觀

**診斷記錄**提供有關 Azure 資源之作業的豐富、 經常性資料。 Azure 監視器提供兩種類型的診斷記錄：

* **租用戶記錄** - 這些記錄來自租用戶層級服務，這些服務存在於 Azure 訂用帳戶外部，例如 Azure Active Directory 記錄。
* **資源記錄** - 這些記錄來自 Azure 服務，可部署 Azure 訂用帳戶內的資源，例如網路安全性群組或儲存體帳戶。

    ![資源診斷記錄與其他類型的記錄](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

這些記錄的內容會依 Azure 服務和資源類型而有所不同。 例如，網路安全性群組規則計數器和 Key Vault 稽核是兩種診斷記錄的類型。

這些記錄檔不同於[活動記錄](activity-logs-overview.md)。 活動記錄檔提供深入了解您訂用帳戶使用 Resource Manager，例如建立虛擬機器，或刪除邏輯應用程式中的資源執行的作業。 活動記錄是訂用帳戶層級記錄。 資源層級診斷記錄可讓您深入探索在該資源本身內所執行的作業，例如從 Key Vault 取得密碼。

這些記錄也與客體 OS 層級診斷記錄不同。 客體 OS 診斷記錄是由虛擬機器內執行的代理程式或其他支援的資源類型所收集的記錄。 資源層級診斷記錄不需要代理程式，且會從 Azure 平台本身擷取資源特定的資料，而客體 OS 層級診斷記錄會從虛擬機器上執行的作業系統和應用程式擷取資料。

並非所有資源皆支援此處所述的診斷記錄。 [本文包含一個章節，會列出哪些服務會支援診斷記錄](./../../azure-monitor/platform/diagnostic-logs-schema.md)。

## <a name="what-you-can-do-with-diagnostic-logs"></a>診斷記錄的用途
以下是您可以利用診斷記錄進行的事：

![診斷記錄的邏輯位置](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* 將診斷記錄檔儲存到[**儲存體帳戶**](../../azure-monitor/platform/archive-diagnostic-logs.md)以利稽核或手動檢查。 您可以使用**資源診斷設定**指定保留時間 (以天為單位)。
* [Stream 他們**事件中樞**](diagnostic-logs-stream-event-hubs.md)供第三方服務或自訂的分析解決方案，例如 Power BI 擷取。
* 使用 [Azure 監視器](../../azure-monitor/platform/collect-azure-metrics-logs.md)來分析它們，其中資料會立即寫入至「Azure 監視器」，而無須先將資料寫入至儲存體。  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

您可以使用並非發出記錄的同一個訂用帳戶中的儲存體帳戶或事件中樞命名空間。 進行此設定的使用者必須具有這兩個訂用帳戶的適當 RBAC 存取權。

> [!NOTE]
>  您目前無法將網路流程記錄封存到位在安全虛擬網路後面的儲存體帳戶。

## <a name="diagnostic-settings"></a>診斷設定

資源診斷記錄是使用資源診斷設定進行設定的。 租用戶診斷記錄是使用租用戶診斷設定進行設定的。 服務的**診斷設定**會控制：

* 診斷記錄和計量的傳送位置 (儲存體帳戶、事件中樞和/或 Azure 監視器)。
* 傳送何種記錄類別，以及是否也會傳送計量資料。
* 每個記錄類別應該在儲存體帳戶中保留多久。
    - 保留期為 0 天表示會永遠保留記錄。 否則，值可以是任意數目的 1 到 365 之間的天數。
    - 如果已設定保留原則，但將儲存體帳戶的記錄儲存停用 (例如，若只選取事件中樞或 Log Analytics 選項)，保留原則不會有任何作用。
    - 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄會被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄會被刪除。 刪除程序會從 UTC 午夜開始，但是請注意，可能需要長達 24 小時的時間，記錄才會從您的儲存體帳戶中刪除。

這些設定會設定從 診斷設定，在入口網站中，使用 Azure PowerShell 和 CLI 命令，或使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)。

> [!NOTE]
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> *例如*：可以依據每個佇列層級來瀏覽及繪製「事件中樞」上的「傳入訊息」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>診斷記錄支援的服務、類別和結構描述

如需支援的服務以及這些服務所使用之記錄類別和結構描述的完整清單，[請參閱這篇文章](../../azure-monitor/platform/diagnostic-logs-schema.md)。

## <a name="next-steps"></a>後續步驟

* [將資源診斷記錄串流至**事件中樞**](diagnostic-logs-stream-event-hubs.md)
* [使用 Azure 監視器 REST API 變更資源診斷設定](https://docs.microsoft.com/rest/api/monitor/)
* [使用 Azure 監視器分析來自 Azure 儲存體的記錄](collect-azure-metrics-logs.md)
