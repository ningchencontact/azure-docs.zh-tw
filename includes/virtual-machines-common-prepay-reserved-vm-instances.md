---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/14/2019
ms.openlocfilehash: c7ff7ab0800449c2a3aa9d58bd036981caccaa1c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156268"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>預付的 Azure 保留的 VM 執行個體 (RI) 的虛擬機器

預付虛擬機器，並使用 Azure 保留的虛擬機器 (VM) 執行個體節省成本。 如需詳細資訊，請參閱 [Azure 保留的 VM 執行個體供應項目](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

您可以在 [Azure 入口網站](https://portal.azure.com)購買保留 VM 執行個體。 若要購買執行個體：

- 您必須至少為一個企業或"隨用隨付"型方案訂用帳戶的「擁有者」角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

保留項目折扣會自動套用到符合保留項目範圍和屬性之執行中虛擬機器的數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、CLI 或 API 來更新保留項目的範圍。

## <a name="determine-the-right-vm-size-before-you-buy"></a>購買之前，請先決定正確的 VM 大小

您購買保留項目之前，應該先判斷您需要 VM 的大小。 下列各節將協助您判斷正確的虛擬機器大小。

### <a name="use-reservation-recommendations"></a>使用保留區建議

您可以使用保留的建議來協助判斷您應該購買的保留項目。

- 購買建議和建議的數量，會顯示當您購買 Azure 入口網站中的 VM 保留執行個體。
- Azure Advisor 提供個別的訂用帳戶的購買建議。  
- 若要取得共用的範圍和單一訂用帳戶範圍購買建議，您可以使用 Api。 如需詳細資訊，請參閱 <<c0> [ 保留適用於企業客戶的執行個體購買建議 Api](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- EA 客戶的購買建議給共享與單一訂用帳戶範圍有[Azure 耗用量 Insights Power BI 內容套件](/power-bi/service-connect-to-azure-consumption-insights)。

### <a name="classic-vms-and-cloud-services"></a>傳統的 Vm 和雲端服務

保留的虛擬機器執行個體自動套用至這兩個傳統 Vm，並啟用執行個體大小彈性時，雲端服務。 目前沒有任何特殊的適用於傳統 Vm 或雲端服務的 Sku。 相同的 VM Sku 適用於它們。

例如，您會將您的傳統 Vm 或雲端服務轉換為 Azure Resource Manager 為基礎的 Vm。 在此範例中，保留折扣會自動套用至對應的 Vm。 不需要*exchange*現有的保留執行個體-它會自動套用。

### <a name="analyze-your-usage-information"></a>分析您的使用方式資訊
您應該分析您的使用量資訊以協助判斷您應該購買的保留項目。

使用量資料可在 Api 與使用量檔案中。 一起使用這些判斷購買的保留項目。 您應該檢查以判斷購買的保留項目數量的每日有高使用率的 VM 執行個體。

避免`Meter`子類別目錄和`Product`中使用量資料的欄位。 它們不區分使用進階儲存體的 VM 大小。 如果您可以使用這些欄位來決定購買保留的虛擬機器大小，您可能會購買大小不正確。 然後，您無法取得您所預期的保留折扣。 相反地，請參閱`AdditionalInfo`欄位中，您的使用量檔案或使用情況 API，以判斷正確的 VM 大小。

### <a name="purchase-restriction-considerations"></a>購買限制考量

保留的 VM 執行個體可供大部分的 VM 大小，但有些例外狀況。 保留折扣不適用於下列 Vm:

- **VM 系列**-A 系列、 Av2 系列或 G 系列。

- **在預覽中的 Vm** -任何 VM 系列或處於預覽狀態的大小。

- **雲端**-保留項目不是適用於德國或中國地區購買。

- **配額不足，無法**-範圍為單一訂用帳戶的保留項目必須在新 ri 訂用帳戶中可用的 vCPU 配額。 例如，如果目標訂用帳戶的配額限制為 10 個 D 系列的 vCPU，您便無法購買一個適用於 11 個 Standard_D1 執行個體的保留項目。 保留的配額檢查包含已在訂用帳戶中部署的 VM。 例如，如果訂用帳戶的配額為 10 個 D 系列的 vCPU，且已部署 2 個 standard_D1 執行個體，則您可以在此訂用帳戶中購買一個適用於 10 個 standard_D1 執行個體的保留項目。 您可以[引號建立增加要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)若要解決此問題。

- **容量限制**-在極少數的情況下，購買新的保留項目子集的 VM 大小，因為區域中的低容量的 Azure 限制。

## <a name="buy-a-reserved-vm-instance"></a>購買保留 VM 執行個體

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]   > [保留]  。
3. 選取 [新增]  以購買新的保留項目。
4. 填寫必要欄位。 符合您選取之屬性的執行中 VM 執行個體符合取得保留項目折扣的資格。 取得折扣的 VM 執行個體實際數目取決於選取的範圍和數量。

    | 欄位      | 描述|
    |------------|--------------|
    |名稱        |此保留項目的名稱。|
    |訂用帳戶|用來支付保留項目的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。|    
    |`Scope`       |保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <ul><li>單一訂用帳戶 - 保留項目折扣會套用至此訂用帳戶中的 VM。 </li><li>共用 - 保留項目折扣會套用至計費內容內任何訂用帳戶中執行的 VM。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。</li></ul>|
    |區域    |保留項目所涵蓋的 Azure 區域。|    
    |VM 大小     |VM 執行個體的大小。|
    |已針對下列項目最佳化     |VM 執行個體大小彈性會將保留項目折扣套用至同一個 [VM 大小群組](https://aka.ms/RIVMGroups)中的其他 VM。 容量優先順序會讓資料中心容量供您的部署優先使用。 這可讓您更加確信您能夠在需要時啟動 VM 執行個體。 保留項目範圍是單一訂用帳戶時，才可使用容量優先順序。 |
    |詞彙        |一年或三年。|
    |數量    |保留項目內所購買的執行個體數目。 數量是可以取得帳單折扣之執行中 VM 執行個體的數目。 例如，如果您在美國東部執行 10 個 Standard_D2 VM，那麼您會指定數量為 10，以最大化所有執行中機器的效益。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>變更購買後的保留項目

您可以進行下列類型的變更，到購買後的保留項目：

- 更新保留範圍
- 執行個體大小彈性 （如果適用）
- 擁有權

您也可以分割成較小的區塊 （chunk） 和已分割保留項目合併的保留項目。 沒有任何變更會導致新的商業交易，或變更保留的結束日期。

您無法直接購買之後，進行下列類型的變更：

- 現有的保留區的區域
- SKU
- 數量
- 持續時間

不過，您可以*exchange*保留，如果您想要進行變更。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消保留，可能會有 12% 的提前解約金。 退款以您購買價格或目前保留價格的最低價格為準。 每年的退款金額上限為 50,000 美元。 您收到的退款是按比例計算的餘額扣除 12% 提前解約金的金額。 若要要求取消，請移至 Azure 入口網站中的保留，並選取 [退款]  以建立支援要求。

如果您需要將 [保留的 VM 執行個體] 保留變更為另一個區域、VM 大小群組或期限，您可以將它與等值或更高價值的另一個保留交換。 新保留區的期間開始日期不會延續自交換的保留區。 1 或 3 年的期限會從您建立新的保留時起算。 若要要求交換，請移至 Azure 入口網站中的保留，並選取 [交換]  以建立支援要求。

如需如何交換或退款的保留區的詳細資訊，請參閱[保留交換記錄和退費記錄](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../articles/billing/billing-manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](../articles/billing/billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的保留](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [了解預付型方案訂用帳戶的保留項目使用量](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)
