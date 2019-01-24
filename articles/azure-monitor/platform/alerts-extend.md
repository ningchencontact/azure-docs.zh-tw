---
title: 擴充 (複製) Log Analytics 至 Azure 警示 - 概觀
description: 將警示從 OMS 入口網站的 Log Analytics 複製到 Azure 警示的程序概觀，詳細說明常見的客戶考量。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: e2f2a739f78072a0479e77e68e84c8f4b395468f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422221"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>將 Log Analytics 警示擴充到 Azure 警示

> [!NOTE]
> Microsoft 已經完成本文中關於 Azure 的公用版本的程序。 不過仍適用於美國政府版本。  

直到最近，Azure Log Analytics 剛剛加入了自己的警示功能，可根據 Log Analytics 資料主動通知您情況。 您之前可在 Microsoft Operations Management Suite 中管理警示規則。 全新警示體驗現已整合 Microsoft Azure 中各種服務之間的警示。 此功能即為 Azure 入口網站中的 Azure 監視器下的 **[警示]**，支援 Log Analytics 和 Azure Application Insights 中活動記錄、計量和記錄的警示。 

## <a name="benefits-of-extending-your-alerts"></a>擴充您警示的好處
在 Azure 入口網站建立和管理警示有幾項好處，例如：

- 不同於在 Operations Management Suite 入口網站中只能建立及檢視 250 個警示，在 Azure 警示中並沒有這項限制。
- 透過 Azure 警示，您可以管理、列舉和檢視所有警示類型。 之前您只能這樣處理 Log Analytics 警示，
- 現在您可以使用 [Azure 監視器角色](../../azure-monitor/platform/roles-permissions-security.md)，將使用者的存取權限制於僅供監視和警示。
- 您可以在 Azure 警示使用[動作群組](../../azure-monitor/platform/action-groups.md)。 此功能允許每個警示有多個動作。 您可以發送簡訊、撥打語音電話、叫用 Azure 自動化 Runbook、叫用 Webhook，並設定 IT 服務管理 (ITSM) 連接器。 

## <a name="process-of-extending-your-alerts"></a>擴充警示的程序
將警示從 Log Analytics 移至 Azure 警示的程序不包含以任何方式變更您的警示定義、查詢或設定。 唯一需要的變更是，在 Azure 中使用動作群組執行所有動作。 如果動作群組已經與警示相關聯，警示擴充至 Azure 時就會包含在內。

當您排程將 Log Analytics 工作區中的警示擴充至 Azure 以後，這些警示會繼續運作，且不會以任何方式妨礙您的設定。 當排程時，您的警示可能暫時無法進行修改，但您可以在這段時間內繼續建立新的 Azure 警示。 如果您嘗試編輯或從 Operations Management Suite 入口網站建立警示，您可以選擇繼續從 Log Analytics 工作區建立警示。 您也可以選擇在 Azure 入口網站中透過 Azure 警示建立警示。

 ![從 Log Analytics 或 Azure 警示建立警示的選項螢幕擷取畫面](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> 將警示從 Log Analytics 擴充至 Azure 不會向您的帳戶收取費用。 如果您遵循 [Azure 監視器定價原則](https://azure.microsoft.com/pricing/details/monitor/)中所述的限制及條件，用於查詢式 Log Analytics 警示的 Azure 警示也不會計費。  


### <a name="how-to-extend-your-alerts-voluntarily"></a>如何主動擴充您的警示
若要將您的警示擴充到 Azure 警示，您可以使用 Operations Management Suite 入口網站中提供的精靈，或者使用 API 以程式設計方式執行。 如需詳細資訊，請參閱[使用 Operations Management Suite 入口網站和 API 將警示擴充至 Azure](alerts-extend-tool.md)。

## <a name="experience-after-extending-your-alerts"></a>擴充警示之後的體驗
將警示擴充至 Azure 警示後，您可如同往常一樣繼續在 Operations Management Suite 入口網站中加以管理。

![Operations Management Suite 入口網站的螢幕擷取畫面，包含列出的警示](media/alerts-extend/PostExtendList.png)

當您嘗試編輯現有的警示，或在 Operations Management Suite 入口網站中建立新的警示時，您會自動重新導向 Azure 警示。  

> [!NOTE]
> 請確認已在 Azure 中正確指派需要加入或編輯警示的人員所需的相關權限。 若要了解您需要授予何種權限，請參閱[使用 Azure 監視器和警示的權限](../../azure-monitor/platform/roles-permissions-security.md)。  
> 

您可以繼續透過 [Log Analytics API](../../azure-monitor/platform/api-alerts.md) 和 [Log Analytics 資源範本](../../azure-monitor/insights/solutions-resources-searches-alerts.md)建立警示。 這麼做時，請務必加入動作群組。

## <a name="next-steps"></a>後續步驟

* 了解[啟動將警示從 Log Analytics 擴充至 Azure](alerts-extend-tool.md)的工具。
* 深入了解 [Azure 警示體驗](../../azure-monitor/platform/alerts-overview.md)。
* 了解如何建立 [Azure 警示中的記錄警示](alerts-unified-log.md)。

