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
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240375"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>使用 Azure 監視器將 Azure Active Directory 記錄與 Splunk 整合 (預覽)

在本文中，您將了解如何使用 Azure 監視器將 Azure Active Directory 記錄與 Splunk 整合。 首先，您需要將記錄路由傳送至 Azure 事件中樞，然後將其與 Splunk 整合。

## <a name="prerequisites"></a>必要條件

1. Azure 事件中樞包含 Azure AD 活動記錄。 了解如何[將活動記錄串流至事件中樞](reporting-azure-monitor-diagnostics-azure-event-hub.md)。 
2. 使用下列[指示](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)下載適用於 Splunk 的 Azure 監視器附加元件，並設定您的 Splunk 執行個體。

## <a name="tutorial"></a>教學課程 

1. 開啟您的 Splunk 執行個體，然後按一下 [資料摘要]。
    ![資料摘要](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "資料摘要")

2. 巡覽至 [Sourcetypes] 索引標籤，然後選取 [amal: aadal:audit] [Sourcetypes]![ 索引標籤](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "[Sourcetypes] 索引標籤")

3. 您會看到 Azure AD 活動記錄，如下圖所示。
    ![活動記錄](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "活動記錄")

> [!NOTE]
> 如果您無法在 Splunk 執行個體中安裝附加元件 (例如，如果使用 Proxy 或在 Splunk Cloud 上執行)，您可以使用這個[由事件中樞內新訊息觸發的 Azure Function](https://github.com/Microsoft/AzureFunctionforSplunkVS)，將這些事件轉寄給 Splunk HTTP 事件收集器。 
>

## <a name="next-steps"></a>後續步驟

* [解譯 Azure 監視器中的稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解譯 Azure 監視器中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [常見問題集和已知問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)