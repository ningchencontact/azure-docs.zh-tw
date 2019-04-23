---
title: 在 Azure 監視器中監視使用量和估計成本
description: 使用 Azure 監視器的使用量和估計成本頁面的程序概觀
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 7117e7287f601b306893cb02dc5d7599d7c6224d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007690"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>在 Azure 監視器中監視使用量和估計成本

> [!NOTE]
> 本文說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。  請參閱下列文章以了解相關資訊。
> - [藉由控制 Log Analytics 中的資料量與保留期來管理成本](manage-cost-storage.md)說明如何藉由變更資料保留期限來控制成本。
> - [分析 Log Analytics 中的資料使用量](../../azure-monitor/platform/data-usage.md)說明如何分析資料使用量及提出警示。
> - [管理 Application Insights 的價格和資料量](../../azure-monitor/app/pricing.md)說明如何分析 Application Insights 中的資料使用量。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在 Azure 入口網站的監視器中樞中，[使用量和估計成本] 頁面會說明核心監視功能 (例如[警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 和 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)) 的使用量。 客戶若是採用在 2018 年 4 月前提供的定價方案，則也會提供透過深入解析與分析供應項目購買的 Log Analytics 使用量。

在此頁面上，使用者可以檢視他們在過去 31 天內對這些資源的使用量 (依個別訂用帳戶彙總)。 向下切入功能可顯示這 31 天的使用量趨勢。 系統必須彙整許多資料才能產生此預估值，因此請耐心等候頁面載入。

下列範例顯示監視使用量和估計最終成本的情形：

![使用量和估計成本入口網站螢幕擷取畫面](./media/usage-estimated-costs/001.png)

選取 [每月使用量] 資料行中的連結以開啟一個圖表，顯示過去 31 天的使用量趨勢：

![每個節點包含長條圖的螢幕擷取畫面](./media/usage-estimated-costs/002.png)

以下是另一個類似的使用量和成本摘要。 此範例說明採用 2018 年 4 月 以使用量為基礎的新式定價模式的訂用帳戶。 請留意到，此時並沒有任何以節點為基礎的計費。 Log Analytics 和 Application Insights 的資料擷取和保留都會在新的通用計量上報告。

![使用量和估計成本入口網站螢幕擷取畫面 - 2018 年 4 月定價](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新的定價模式

我們在 2018 年 4 月[發行了新的監視定價模式](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。  此模式非常適合雲端使用，並具有以使用量為基礎的定價功能。 您只需依用量付費，而無需承擔以節點為基礎的承諾。 [警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 和 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 都有新定價模式的詳細資料可供參考。 

對於在 2018 年 4 月 2 日之後加入 Log Analytics 或 Application Insights 的客戶而言，新的定價模式將是唯一選項。 已使用這些服務的客戶則可選擇是否移轉至新的定價模式。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>評估新定價模式的影響
新的定價模式將會根據客戶的監視使用模式，對每個客戶產生不同的影響。 對於在 2018 年 4 月 2 日之前即使用 Log Analytics 或 Application Insights 的客戶，Azure 監視器中的 [使用量和估計成本] 頁面將會估計移轉至新的定價模式時所將產生的成本變更。 此外也提供將訂用帳戶移轉至新模式的途徑。 對大多數客戶而言，新的定價模式將較有利。 但客戶的資料使用量若特別高，或是屬於成本較高的區域，可能就另當別論。

若要對您在 [使用量和估計成本] 頁面上選擇的訂用帳戶查看成本的估計值，請選取頁面頂端附近的藍色橫幅。 建議您一次查看一個訂用帳戶的資料，因為新的定價模式是在此層級上採行的。

![監視新定價模式中的使用量和估計成本的螢幕擷取畫面](./media/usage-estimated-costs/004.png)

新的頁面會顯示與先前的頁面類似、但具有綠色橫幅的內容：

![監視現行定價模式中的使用量和估計成本的螢幕擷取畫面](./media/usage-estimated-costs/005.png)

此頁面也會顯示一組與新的定價模式相對應的不同計量。 以下清單為範例：

- 深入解析與分析\每個節點的超額
- 深入解析與分析\每個節點包含
- Application Insights\基本超額資料
- Application Insights\內含資料

新的定價模式並未以節點為基礎納入資料配置。 因此，這些資料擷取計量會結合為新的通用資料擷取計量，名為**共用服務\資料擷取**。 

擷取至較高成本區域的 Log Analytics 或 Application Insights 中的資料，則有另一項變更。 這些高成本區域的資料將會依新的區域計量顯示。 範例之一是**資料擷取 (美國中西部)**。

> [!NOTE]
> 按訂用帳戶的估計成本不會考量 Operations Management Suite (OMS) 訂用帳戶在帳戶層級上的個別節點權利。 如需深入討論新的定價模式在此情況下的運作方式，請洽詢帳戶代表。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新的定價模型和 Operations Management Suite 訂用帳戶的權利

凡購買 Microsoft Operations Management Suite E1 和 E2 的客戶，皆符合資格使用 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) 的按節點資料擷取權利。 若要以指定訂用帳戶享用 Log Analytics 工作區或 Application Insights 資源的這些權利： 

- 該訂用帳戶的定價模式必須保持在 2018 年 4 月前的模式。
- Log Analytics 工作區應該使用「每個節點 (OMS)」定價層。
- Application Insights 資源應該使用「企業」定價方案。

根據貴組織所購買套件的節點數目，讓部分訂用帳戶改採新的定價模式仍可能對您有利，但這需要仔細考量。 一般情況下，建議您只要維持如上所述 2018 年 4 月前的模式即可。

> [!WARNING]
> 如果貴組織已購買 Microsoft Operations Management Suite E1 和 E2，訂用帳戶的定價模式通常最好保持在 2018 年 4 月前的模式。 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>移轉至新定價模式時的變更

新的定價模式可將 Log Analytics 和 Application Insights 定價選項簡化至只有單一層 (或方案)。 將訂用帳戶移轉至新的定價模式將會：

- 將每個 Log Analytics 的定價層變更成新的「每一 GB」層 (在 Azure Resource Manager 中稱為「pergb2018」)
- 企業方案中的任何 Application Insights 資源會變更為基本方案。

成本估計會反映出這些變更的影響。

> [!WARNING]
> 重要注意事項：如果您使用 Azure Resource Manager 或 PowerShell 來部署訂用帳戶中的 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration)或 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell)，表示您已移至新的定價模式。 如果您為 Log Analytics 指定「pergb2018」以外的定價層/方案，或是為 Application Insights 指定「基本」方案，就不會因為指定了無效的定價層/方案而導致部署失敗；不過，雖然部署成功，**但系統也只會使用有效的定價層/方案** (此方式不適用於會產生無效定價層訊息的 Log Analytics 免費層)。
>

## <a name="moving-to-the-new-pricing-model"></a>移轉至新的定價模式

如果您已決定採用指定的訂用帳戶的新定價模型，請移至每個 Application Insights 資源，開啟**使用量和估計的成本**並確定它是在基本定價層，並移至每個 Log Analytics工作區中，開啟每個**定價層**頁面上，並將**每 GB (2018)** 定價層。 

> [!NOTE]
> 所有 Application Insights 資源和指定的訂用帳戶內的 Log Analytics 工作區，都採用最新的定價模型現在已移除需求，讓更大的彈性和更容易的設定。 

## <a name="automate-moving-to-the-new-pricing-model"></a>自動移轉至新的定價模式

如上所述，不再需要將監視的所有資源的訂用帳戶中都移至新的計價模式，在此同時，因此``migratetonewpricingmodel``動作將不再有任何作用。 現在您可以移動 Application Insights 資源與 Log Analytics 工作區分開到最新的定價層。  

自動化這項變更會記載使用 Application insights[組 AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan)具有``-PricingPlan "Basic"``及使用 Log Analytics [Set-azurermoperationalinsightsworkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace)與``-sku "PerGB2018"``。 

