---
title: 如何使用 Azure 監視器將 Azure Active Directory 記錄與 Splunk 整合 (預覽) | Microsoft Docs
description: 了解如何使用 Azure 監視器將 Azure Active Directory 記錄與 Splunk 整合 (預覽)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503837"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>使用 Azure 監視器將 Azure AD 記錄與 Splunk 整合 (預覽)

在此文章中，您將了解如何使用 Azure 監視器將 Azure Active Directory (Azure AD) 記錄與 Splunk 整合。 您首先會將記錄路由傳送至 Azure 事件中樞，然後將事件中樞與 Splunk 整合。

## <a name="prerequisites"></a>必要條件

若要使用此功能，您必須要有：
* 包含 Azure AD 活動記錄的 Azure 事件中樞。 了解如何[將活動記錄串流至事件中樞](reporting-azure-monitor-diagnostics-azure-event-hub.md)。 
* 適用於 Splunk 的 Azure 監視器附加元件。 [下載並設定您的 Splunk 執行個體](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) \(英文\)。

## <a name="tutorial"></a>教學課程 

1. 開啟您的 Splunk 執行個體，並選取 [資料摘要]。

    ![[資料摘要] 按鈕](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. 選取 [來源類型] 索引標籤，然後選取 [amal: aadal:audit]

    ![[資料摘要] 的 [來源類型] 索引標籤](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    下圖顯示 Azure AD 活動記錄檔：

    ![活動記錄](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> 如果您無法在 Splunk 執行個體中安裝附加元件 (例如，如果您使用 Proxy，或在 Splunk Cloud 上執行)，您可以將這些事件轉送至 Splunk HTTP 事件收集器。 若要這樣做，請使用此 [Azure 函式](https://github.com/Microsoft/AzureFunctionforSplunkVS) \(英文\)，事件中樞中的新訊息會觸發此函式。 
>

## <a name="next-steps"></a>後續步驟

* [解譯 Azure 監視器中的稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解譯 Azure 監視器中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常見問題集和已知問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
