---
title: 設定可用性警示，使用 Azure Application Insights |Microsoft Docs
description: 在 Application Insights 中設定 Web 測試。 如果網站無法使用或回應緩慢，將收到警示。
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305009"
---
# <a name="availability-alerts"></a>可用性警示

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 會將來自全球各地的 Web 要求定期傳送給您的應用程式。 如果您的應用程式沒有回應，或回應速度太慢，它可以警示您。

## <a name="enable-alerts"></a>啟用警示

根據預設，會立即自動啟用警示，但為了完整設定警示您有一開始建立您的可用性測試。

![建立體驗](./media/availability-alerts/create-test.png)

> [!NOTE]
>  使用[新的整合警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)時，**必須**在警示體驗中設定[動作群組](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)的相關警示規則嚴重性和通知喜好設定。 若不進行下列步驟，您只會收到入口網站內部通知。

1. 儲存可用性測試之後，在 [詳細資料] 索引標籤上，按一下您剛剛建立之測試旁的省略符號。 按一下 [編輯警示]。

   ![編輯儲存後](./media/availability-alerts/edit-alert.png)

2. 設定所需的嚴重性層級、規則描述以及，同時也是最重要的，您想要用於此警示規則的通知喜好設定出現在其中的動作群組。

   ![編輯儲存後](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>對於 Y 個位置之中有 X 個回報失敗所發出的警示

您建立新的可用性測試，預設會在[新的整合警示體驗](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)中啟用 X/Y 個位置警示規則。 您可以選取「傳統」選項來選擇退出，也可以選擇停用警示規則。

> [!NOTE]
> 依照上述步驟，設定警示觸發時收到通知的動作群組。 若不進行這個步驟，此規則會觸發時，您只會收到入口網站內部通知。
>

### <a name="alert-on-availability-metrics"></a>可用性度量的警示

使用[新的整合警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)時，您也可以對於分段彙總可用性和測試持續時間計量設定警示：

1. 在 [計量] 體驗中選取某個 Application Insights 資源，然後選取 [可用性] 計量：

    ![可用性計量選取範圍](./media/availability-alerts/select-metric.png)

2. 從功能表設定警示選項會帶您前往新的體驗，您可以在其中選取特定測試或位置來設定警示規則。 您也可以在這裡設定此警示規則的動作群組。

### <a name="alert-on-custom-analytics-queries"></a>自訂分析查詢的警示

使用[新的整合警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)時，您可以設定[自訂記錄檔查詢](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log)的警示。 使用自訂查詢，您可以設定任何任意條件的警示，協助您取得可用性問題的最可靠訊號。 如果您要使用 TrackAvailability SDK 的傳送自訂可用性結果，這也特別適用。 

> [!Tip]
> 可用性資料的計量包括呼叫我們 TrackAvailability SDK 時您可能會提交的任何自訂可用性結果。 您可以使用計量支援的警示，設定自訂可用性結果的警示。
>

## <a name="troubleshooting"></a>疑難排解

專用[疑難排解文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [多重步驟 web 測試](availability-multistep.md)
* [Url ping web 測試](monitor-web-app-availability.md)

