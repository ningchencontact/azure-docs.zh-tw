---
title: 避免非預期的成本和管理 Azure 中的計費
description: 了解如何避免 Azure 帳單上的意外費用。 使用 Azure 訂用帳戶的成本追蹤和管理功能。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612055"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>使用 Azure 計費與成本管理避免非預期的費用

當您註冊 Azure 時, 您可以執行幾項工作, 以進一步瞭解您的費用:

- [定價計算機](https://azure.microsoft.com/pricing/calculator/)可以在您建立 Azure 資源之前提供成本估計值。 

- [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)可為您提供目前的成本明細和您訂用帳戶的預測。 

- 如果您需要將不同專案或小組的成本分組並加以了解，請查看[資源標記](../azure-resource-manager/resource-group-using-tags.md)。 如果您的組織有您偏好使用的報告系統，請查看[計費 API](billing-usage-rate-card-overview.md)。

- 如果您的訂用帳戶是從 Enterprise 合約 (EA) 建立的, 您可以在 Azure 入口網站中查看成本。 如果您的訂用帳戶是透過雲端解決方案提供者 (CSP) 或 Azure 贊助, 則下列部分功能可能不適用於您。 如需詳細資訊, 請參閱[EA、CSP 和贊助的其他資源](#other-offers)。

- 如果您的訂用帳戶是免費試用供應專案、 [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、AZURE IN OPEN (AIO) 或 BizSpark, 則您的訂用帳戶會在您所有點數都使用時自動停用。 深入了解[消費限制](#spending-limit)，以避免您的訂用帳戶發生未預期的停用。

- 如果您已註冊[Azure 免費帳戶](https://azure.microsoft.com/free/),[您可以免費使用一些最熱門的 azure 服務12個月](billing-create-free-services-included-free-account.md)。 除了下列建議之外，請參閱[避免產生免費帳戶的費用](billing-avoid-charges-free-account.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在新增 Azure 服務之前取得估計的成本

以下是使用下列工具來估計成本的一些額外資訊:
- Azure 定價計算機
- Azure 入口網站
- 消費限制

下列各節中的影像顯示以美元為單位的範例價格。

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用價格計算機在線上評估成本

查看[價格計算機](https://azure.microsoft.com/pricing/calculator/)即可取得您感興趣的服務每月成本評估。 您可以新增任何第一方 Azure 資源來取得預估成本。 在定價計算機中, 您可以變更貨幣類型。

![定價計算機功能表的螢幕擷取畫面](./media/billing-getting-started/pricing-calc.png)

例如, 在定價計算機中, 如果您讓它保持執行, 則會估計 A1 Windows 虛擬機器 (VM) 在計算時數中的特定金額/月份上會產生費用:

![定價計算機的螢幕擷取畫面, 其中顯示每個月 A1 Windows VM 估計成本](./media/billing-getting-started/pricing-calcvm.png)

如需價格的詳細資訊, 請參閱[定價常見問題](https://azure.microsoft.com/pricing/faq/)。 如果您想要與 Azure 銷售人員交談, 請撥打 [常見問題] 頁面頂端所顯示的電話號碼。

### <a name="review-estimated-costs-in-the-azure-portal"></a>查看 Azure 入口網站中的預估成本

一般而言, 當您在 Azure 入口網站中新增服務時, 會有一個 view, 會以您的計費貨幣顯示每個月的預估成本。 例如，當您選擇 Windows VM 的大小時，會看到計算時數的預估每月成本：

![範例: 顯示每月預估成本的 A1 Windows VM](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> 檢查您是否開啟消費限制

如果您有使用信用額度的訂用帳戶，那麼您的消費限制依預設是開啟的。 如此一來，當您花光您的信用額度時，就不會針對您的信用卡收費。 請參閱 [Azure 供應項目完整清單及消費限制可用性](https://azure.microsoft.com/support/legal/offer-details/)。

不過, 當您達到消費限制時, 您的服務就會停用。 這表示您的 VM 會被解除配置。 若要避免服務停機，您必須關閉消費限制。 任何超額部分都會用您在檔案上的信用卡收費。

若要查看您是否有消費限制, 請移至[帳戶中心的訂閱視圖](https://account.windowsazure.com/Subscriptions)。 如果您的消費限制已開啟, 則會顯示橫幅, 如下所示:

![顯示帳戶中心已開啟消費限制的警告的螢幕擷取畫面](./media/billing-getting-started/spending-limit-banner.png)

按一下橫幅, 並遵循提示來移除消費限制。 如果您在註冊時未輸入信用卡資訊，則必須輸入信用卡資訊，才能移除消費限制。 如需詳細資訊，請參閱 [Azure 消費限制 - 運作方式以及啟用或移除方法](https://azure.microsoft.com/pricing/spending-limits/)。

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../cost-management/tutorial-acm-create-budgets.md)來管理成本, 並建立[警示](../cost-management/cost-mgt-alerts-monitor-usage-spending.md), 以自動通知專案關係人如何耗費異常和超支風險。 警示是根據支出與預算和成本閾值的費用。

## <a name="monitor-costs-when-using-azure-services"></a>使用 Azure 服務時監視成本
您可以使用下列工具來監視成本:

- Tags
- 成本細分和完工速率
- 成本分析

### <a name="tags"></a>將標記新增至資源以群組計費資料

針對支援的服務，您可以使用標記來將您的計費資料分組。 例如, 如果您針對不同的小組執行數個 Vm, 則可以使用標籤依成本中心將成本分類 (例如:HR、行銷、財務等等)或環境 (例如: 生產、進入生產階段前、測試)。

![顯示在入口網站中設定標籤的螢幕擷取畫面](./media/billing-getting-started/tags.png)

這些標記會顯示在不同的成本報告檢視中。 例如, 它們會立即顯示在您的 [[成本分析] 視圖](#costs)中, 以及在第一個計費週期後的詳細使用量 CSV 檔案中。

如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="costs"></a>監視成本細分和完工速率

當您的 Azure 服務執行之後, 請定期檢查費用。 您可以在 Azure 入口網站中看到目前的支出和完工速率。

1. 瀏覽 [Azure 入口網站中的 [訂用帳戶]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 然後選取訂用帳戶。

2. 如果您的訂用帳戶支援, 您會看到 [成本分解] 和 [完工速率]。

    ![Azure 入口網站中完工速率和成本細分的螢幕擷取畫面](./media/billing-getting-started/burn-rate.PNG)

3. 按一下左側清單中的 [[成本分析](../cost-management/quick-acm-cost-analysis.md)], 以查看資源的成本明細。 新增服務之後, 請等候24小時讓資料顯示。

    ![Azure 入口網站中成本分析檢視的螢幕擷取畫面](./media/billing-getting-started/cost-analysis.png)

4. 您可以依照不同屬性來篩選，例如[標記](#tags)、資源類型、資源群組和時間範圍。 如果您想要將此視圖匯出為逗點分隔值 (.csv) 檔案, 請按一下 [套用] 以確認篩選和**下載**。

5. 此外，您可以按一下資源來查看您的每日支出記錄，以及每一天的成本是多少。

    ![Azure 入口網站中消費歷程記錄檢視的螢幕擷取畫面](./media/billing-getting-started/costhistory.png)

將您看到的成本與您在選取服務時看到的預估值進行比較。 如果成本與估計值明顯不同, 請檢查您為資源選取的定價方案。

## <a name="optimize-and-reduce-costs"></a>優化並降低成本
如果您不熟悉成本管理的原則, 請閱讀[如何使用 Azure 成本管理將雲端投資優化](../cost-management/cost-mgt-best-practices.md)。

在 Azure 入口網站中, 您也可以透過自動關閉 Vm 和 Advisor 建議來優化和降低 Azure 成本。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>考慮成本縮減功能, 例如自動關閉 Vm

視您的案例而定，您可以在 Azure 入口網站中設定 VM 自動關機。 如需詳細資訊，請參閱[使用 Azure Resource Manager 的 VM 自動關機](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) \(英文\)。

![在入口網站中的自動關機選項的螢幕擷取畫面](./media/billing-getting-started/auto-shutdown.png)

自動關機與您在 VM 中使用電源選項關閉不一樣。 自動關機會停止並解除配置您的 VM，以停止額外的使用費用。 如需詳細資訊，請參閱 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 的定價常見問題，以及 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)有關 VM 的狀態。

如需更多針對開發和測試環境的成本削減功能，請參閱 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>開啟及審查 Azure Advisor 建議

[Azure Advisor](../advisor/advisor-overview.md)藉由識別使用量低的資源, 協助您降低成本。 在 Azure 入口網站中造訪 Adviso：

![Azure 入口網站中 Azure 建議程式按鈕的螢幕擷取畫面](./media/billing-getting-started/advisor-button.png)

您可以在 Advisor 儀表板的 [**成本**] 索引標籤中, 取得可操作的建議:

![建議程式的成本建議範例的螢幕擷取畫面](./media/billing-getting-started/advisor-action.png)

請參閱[從建議優化成本](../cost-management/tutorial-acm-opt-recommendations.md)教學課程, 以取得有關節省成本建議程式的引導式教學課程。

## <a name="review-costs-against-your-latest-invoice"></a>根據您最新的發票來審查成本

在計費週期結束時, 您可以使用最新的發票。 您也可以[下載發票和詳細的使用量](billing-download-azure-invoice-daily-usage-date.md)檔案, 以確定您已正確地向您收費。 如需比較每日使用量和發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

### <a name="billing-api"></a>計費 API

使用 Azure 計費 API 以程式設計方式取得使用量資料。 RateCard API 與使用情況 API 一起使用即可取得您的計費使用量。 如需詳細資訊，請參閱[深入瞭解 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a> 其他資源和特殊案例

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和贊助客戶
若要開始使用，請連絡帳戶管理員或 Azure 。

| 供應項目 | 資源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise 合約 (EA) | [EA 入口網站](https://ea.azure.com/)、[協助文件](https://ea.azure.com/helpdocs)和 [Power BI 報告](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 雲端解決方案提供者 (CSP) | 與您的提供者討論 |
| Azure 贊助 | [贊助入口網站 (英文)](https://www.microsoftazuresponsorships.com/) |

如果您管理的是大型組織的 IT，我們建議您閱讀 [Azure 企業 Scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) 和[企業 IT 技術白皮書](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (.pdf 下載，僅提供英文版)。

#### <a name="EA"></a>Azure 入口網站中的 Enterprise 合約成本視圖

企業成本檢視目前只提供公開預覽。 下列為需注意的事項：

- 訂閱費用是以使用情況計費，不包含預付金額、超額部分、包含的數量、調整內容和稅金。 系統會在申請時計算實際費用。
- 在 Azure 入口網站中顯示的數量可能與在企業版入口網站中顯示的不同。 在企業版入口網站中的更新可能需要幾分鐘之後才會顯示在 Azure 入口網站中。
- 如果您沒有看到成本，原因可能為下列其中之一：
    - 您沒有訂用帳戶層級的權限。 若要查看企業成本檢視，您必須是訂用帳戶層級上的帳單讀者、讀者、參與者或擁有者。
    - 您是帳戶擁有者且您的申請管理員已停用「AO 檢視費用」設定。  請連絡您的申請管理員以取得費用存取權。
    - 您是部門系統管理員, 且您的註冊管理員已停用「 **DA 視圖費用**」設定。  請連絡您的註冊管理員以取得存取權。
    - 您是透過通道合作夥伴購買 Azure，而該合作夥伴未釋出定價資訊。  
- 如果您在企業版入口網站中更新成本存取相關的設定，則變更可能需要幾分鐘才會在 Azure 入口網站中顯示。
- 消費限制和發票指導方針不適用於 EA 訂用帳戶。

### <a name="check-your-subscription-and-access"></a>檢查訂用帳戶和存取權

若要檢視成本，您必須有[帳單資訊的訂用帳戶層級存取權](billing-manage-access.md)。 只有帳戶管理員可以存取[帳戶中心](https://account.azure.com/Subscriptions)、變更帳單資訊，以及管理訂用帳戶。 帳戶管理員是完成註冊程序的人員。 如需詳細資訊，請參閱[新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色](billing-add-change-azure-subscription-administrator.md)。

若要查看您是否為帳戶管理員，請移至 [Azure 入口網站中的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 查看訂閱清單並尋找我的**角色**。 如果*帳戶管理員*是角色, 則您擁有完整許可權。 如果它顯示其他內容 (例如「*擁有*者」), 您就不會擁有完整許可權。

![Azure 入口網站的訂用帳戶檢視中您的角色的螢幕擷取畫面](./media/billing-getting-started/sub-blade-view.PNG)

若要管理訂用帳戶及變更帳單資訊，請[尋找帳戶管理員](billing-subscription-transfer.md#whoisaa)。要求帳戶管理員完成工作, 或將訂用帳戶[轉移給您](billing-subscription-transfer.md)。

如果您的帳戶管理員已不在您組織，而您需要管理帳單，請[與我們連絡](https://go.microsoft.com/fwlink/?linkid=2083458)。


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>申請服務事件的服務等級協定信用額度

服務等級協定 (SLA) 描述 Microsoft 對執行時間與連線能力的承諾。 當 Azure 服務遇到影響執行時間或連線能力 (通常稱為*中斷*) 的問題時, 就會回報服務事件。 如果我們未依照 SLA 中的說明來達到並維護每個服務的服務等級, 則您可能有資格取得每月服務費用的信用額度。

若要要求點數:

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 如果您有多個帳戶, 請確定您使用的是受 Azure 停機時間影響的帳戶。 
2. 建立新的支援要求。
3. 在 [**問題類型**] 底下, 選取 [**計費**]。
4. 在 [**問題類型**] 底下, 選取 [**退款要求**]。
5. 新增詳細資料以指定您要要求的 SLA 信用額度、提及日期/時間/時區, 以及受影響的服務 (Vm、網站等)
6. 請確認您的連絡人詳細資料, 然後選取 [**建立**] 以提交您的要求。

SLA 閾值會因服務而異。 例如, SQL Web 層的 SLA 為 99.9%, Vm 的 sla 為 99.95%, 而 SQL 標準層的 SLA 為 99.99%。

針對某些服務, 應套用 SLA 的必要條件。 例如, 虛擬機器必須在相同的可用性設定組中部署兩個以上的實例。

如需詳細資訊, 請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)和[Azure 服務的 SLA 摘要](https://azure.microsoft.com/support/legal/sla/summary/)檔。

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有任何疑問或需要協助, 請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 瞭解如何使用[消費限制](billing-spending-limit.md)來避免超支。
- 開始[分析您的 Azure 成本](../cost-management/quick-acm-cost-analysis.md)。
