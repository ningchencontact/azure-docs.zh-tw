---
title: 了解計量警示在 Azure 監視器中的運作方式。
description: 取得計量警示有何用途以及其在 Azure 監視器中如何運作的概觀。
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 1ec47ddf5769dd8ed624277a86db57f449581b90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948684"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>了解計量警示在 Azure 監視器中的運作方式

Azure 監視器中的計量警示以多維度計量為基礎運作。 這些計量可能是平台計量、自訂計量 (預覽版)、來自 Log Analytics 轉換為計量的常用記錄、Application Insights 標準計量。 計量警示會定期評估，檢查某計量或計量時間序列上的條件是否為真，並在評估符合時通知您。 計量警示具狀態，也就是說只會在狀態變更時傳送通知。

## <a name="how-do-metric-alerts-work"></a>計量警示的運作方式

您可以透過指定要監視的目標資源、計量名稱與條件 (運算子與閾值) 來定義計量警示規則，以及要在警示規則引發時觸發的動作群組。
假設您已建立簡單的計量警示規則，如下所示：

- 目標資源 (您要監視的 Azure 資源)：myVM
- 計量：CPU 百分比
- 時間彙總 (針對未經處理的計量值執行的統計資料。 支援的時間彙總為最小、最大、平均、總計)：平均
- 期間 (檢查計量值的回溯查看時段)：     過去 5 分鐘內
- 頻率 (計量警示檢查條件是否符合的頻率)：1 分鐘
- 運算子：     大於
- 閾值：      70

從警示規則建立後開始，監視器會每隔 1 分鐘執行一次，並查看過去 5 分鐘的計量值，以及檢查這些值的平均是否超過 70。 如果條件符合，也就是說過去 5 分鐘的 CPU 百分比平均超過 70，則警示規則會引發啟動通知。 如果您已在與警示規則相關聯的動作群組中設定電子郵件或 Webhook 動作，您會收到兩者的啟動通知。

所引發警示規則的這個特定執行個體也能在 Azure 入口網站中，於所有「所有警示」刀鋒視窗中檢視。

假設在後續檢查中，"myVM" 上的使用量一直高於閾值，除非解決該情況，否則不會再次引發警示規則。

經過一段時間後，如果 "myVM" 上的使用量下降恢復至正常，也就是低於指定的閾值。 警示規則會再監視該條件兩次，以傳送解決通知。 當連續三個期間的警示條件都不符合時，警示規則會傳送已解決/停用訊息，以減少不穩定情況下的雜訊。

透過 Webhook 或電子郵件傳送已解決通知時，Azure 入口網站中警示執行個體的狀態 (稱為「監視器」狀態)，也會設定為已解決。

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>在 Azure 監視器中使用計量警示大規模進行監視

### <a name="using-dimensions"></a>使用維度

Azure 監視器中的計量警示也支援以一個規則監視多個維度值組合。 讓我們借助範例，來了解為什麼您可能會使用多個維度組合。

假設您有要用於網站的 App Service 方案。 您想要針對執行您網站/應用程式的多個執行個體監視其 CPU 使用量。 您可以使用計量警示規則，如下所示

- 目標資源：myAppServicePlan
- 計量：CPU 百分比
- 維度
  - 執行個體 = InstanceName1、InstanceName2
- 時間彙總：平均
- 期間 = 過去 5 分鐘內
- 頻率：1 分鐘
- 運算子：大於
- 閾值：70

和之前一樣，此規則會監視過去 5 分鐘的平均 CPU 使用量是否超過 70%。 不過，使用相同的規則，您可以監視兩個執行您網站的執行個體。 每個執行個體都是個別監視，而您也會個別得到通知。

假設您的 Web 應用程式預計會面臨大量需求，而您需要新增更多執行個體。 上述規則仍只會監視兩個執行個體。 不過，您可以建立規則，如下所示。

- 目標資源：myAppServicePlan
- 計量：CPU 百分比
- 維度
  - 執行個體 = *
- 時間彙總：平均
- 期間 = 過去 5 分鐘內
- 頻率：1 分鐘
- 運算子：大於
- 閾值：70

此規則會自動監視執行個體的所有值，也就是說 您可以在執行個體顯示時監視，而不需要再次修改計量警示規則。

### <a name="monitoring-multiple-resource-using-metric-alerts"></a>使用計量警示監視多個資源

如您在上一節所見，能以單一計量警示規則監視每個個別的維度組合 (也就是 計量時間序列)。 不過，仍限制一次只能對一個資源執行。 在預覽階段，計量警示現在也支援以一個規則監視多個資源。 如果您的訂用帳戶中有 100 個 VM，這個新功能有助於快速地為其設定監視功能。 

此功能目前為預覽狀態。 目前不支援透過 Azure 入口網站來建立監視多個資源的計量警示規則。 您可以透過 Azure Resource Manager 範本建立這些規則。

## <a name="typical-latency"></a>一般延遲

針對計量警示，如果您將警示規則頻率設定為 1 分鐘，通常可在 5 分鐘內得到通知。在通知系統負載過重時，則可能會延遲較久。

## <a name="supported-resource-types-for-metric-alerts"></a>計量警示支援的資源類型

您可以在此[文章](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)中找到所支援資源類型的完整清單

## <a name="next-steps"></a>後續步驟

- [了解如何在 Azure 中建立、檢視及管理計量警示](alert-metric.md)
- [了解如何使用 Azure Resource Manager 範本部署計量警示](monitoring-create-metric-alerts-with-templates.md)
- [深入了解動作群組](monitoring-action-groups.md)
