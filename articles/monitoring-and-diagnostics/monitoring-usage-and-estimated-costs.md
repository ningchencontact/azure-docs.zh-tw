---
title: 在 Azure 監視器中監視使用量和估計成本 | Microsoft Docs
description: 使用 Azure 監視器的使用量和估計成本頁面的程序概觀
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>監視使用量和估計成本

在 Azure 入口網站的監視器中樞中，[使用量和估計成本] 頁面可協助您了解核心監視功能 (例如[警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 和 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)) 的使用量。 客戶若是採用在 2018 年 4 月前提供的定價方案，則也會提供透過深入解析與分析供應項目購買的 Log Analytics 使用量。

在此頁面上，使用者可以檢視他們在過去的 31 天內對這些資源的使用量；這些資料除了會依個別訂用帳戶彙總，也提供向下切入功能讓使用者查看這段期間內的使用量趨勢。 系統必須彙整許多資料才能產生預估值，因此請耐心等候頁面載入。
下列範例顯示監視使用量和估計最終成本的情形：

![使用量和估計成本入口網站螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/001.png)

按一下 [每月使用量] 資料行中的連結，將會開啟一個圖表，顯示過去 31 天的使用量趨勢：

![每個節點包含 671.47 GB 的螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/002.png)

以下是另一個類似的使用量和成本摘要，說明採用 2018 年 4 月 以使用量為基礎的新式計價模式的訂用帳戶。 請留意到，此時並沒有任何以節點為基礎的計費，且 Log Analytics 和 Application Insights 的資料擷取和保留都會在新的通用計量上報告。

![使用量和估計成本入口網站螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新的定價模式

我們在 2018 年 4 月發行了新的監視定價模式。  此模式非常適合雲端使用，並具有以使用量為基礎的定價功能。 您只需依用量付費，而無需承擔以節點為基礎的承諾。 [警示、計量、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 和 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 都有新定價模式的詳細資料可供參考。

對於在 2018 年 4 月 2 日之後加入 Log Analytics 或 Application Insights 的客戶而言，新的定價模式將是唯一選項。 已使用這些服務的客戶則可選擇是否移轉至新的定價模式。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>評估新定價模式的影響

新的定價模式將會根據客戶的監視使用模式，對每個客戶產生不同的影響。 對於已使用 2018 年 4 月 2 日之前的 Log Analytics 或 Application Insights 的客戶，Azure 監視器中的**使用量和估計成本**頁面將會提供相關方法，讓您評估在移轉至新的定價模式時所將產生的成本變更，並提供將訂用帳戶移轉至新模式的方式。 對大多數客戶而言，新的定價模式將較有利，但客戶的資料使用量若特別高，或是屬於成本較高的區域，可能就另當別論。

若要對您在**使用量和估計成本**頁面上選取的訂用帳戶查看成本的估計，請按一下頁面頂端附近的藍色橫幅。 建議您一次查看一個訂用帳戶的資料，因為新的定價模式是在此層級上採行的。

![定價模式選取螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/004.png)

現在，您會看到此頁面顯示類似內容和綠色橫幅：

![定價模式選取螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/005.png)

在此您會看到一組不同的計量 – 對應於新定價模式的計量。 比方說，如下的資料擷取計量

1. 深入解析與分析\每個節點的超額
2. 深入解析與分析\每個節點包含
3. Application Insights\基本超額資料
4. Application Insights\內含資料

會結合為新的通用資料擷取計量，名為**共用服務\資料擷取**，因為新的定價模式並沒有按節點的內含資料權利。

您會看到另一項變更是，資料若擷取到成本較高之區域的 Log Analytics 或 Application Insights 中，將會以新的區域計量顯示，以適當加以反映，例如**擷取資料 (美國中西部)**。

> [!NOTE]
> 按訂用帳戶的估計成本不會考慮 Operations Management Suite (OMS) 訂用帳戶的按節點權利帳戶層級。 如需深入討論新的定價模式在此情況下的運作方式，請洽詢帳戶代表。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新的定價模型和 Operations Management Suite 訂用帳戶的權利

凡購買 Microsoft Operations Management Suite E1 和 E2 的客戶，皆符合資格使用 [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) 和 [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans) 的按節點資料擷取權利。 若要以指定訂用帳戶享用 Log Analytics 工作區或 Application Insights 資源的這些權利，該訂用帳戶的定價模型必須保持在 2018 年 4 月前的定價模型；在該定價模型中可使用 Log Analytics「按節點 (OMS)」定價層和 Application Insights「企業」定價方案。 根據貴組織所購買套件的節點數目，讓部分訂用帳戶改採新的定價模型仍可能對您有利，但這需要仔細考量。 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>移轉至新定價模式時的變更

將訂用帳戶移轉至新的定價模式，會將每個 Log Analytics 的定價層變更為新的「每一 GB」層 (在 Azure Resource Manager 中名為 "pergb2018")。 此移轉也會將企業方案中的任何 Application Insights 資源變更為基本方案。 這些移轉的效果會顯示在前述的成本估計上。 

## <a name="moving-to-the-new-pricing-model"></a>移轉至新的定價模式

如果您已決定為訂用帳戶採用新的定價模式，請按一下 [使用量和估計成本] 頁面頂端的 [定價模式選取] 選項：

![監視新定價模式中的使用量和估計成本螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/006.png)

這會開啟 [定價模式選取] 頁面，列出您在先前的頁面上檢視的每個訂用帳戶：

![定價模式選取螢幕擷取畫面](./media/monitoring-usage-and-estimated-costs/007.png)

若要將訂用帳戶移轉至新的定價模式，請勾選方塊並按一下 [儲存] 即可。  您可以用相同方式重新移轉至舊的定價模式。 請注意，必須具備訂用帳戶擁有者或參與者權限，才能變更定價模式。
