---
title: 商業 marketplace 支出摘要 |Azure Marketplace
description: '[付款摘要] 會顯示您在供應專案中所獲得之金錢的詳細資料。 也可以讓您了解何時會收到付款與付款金額。'
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980266"
---
# <a name="payout-reporting"></a>支付報告

[付款**摘要**] 會顯示您在 Microsoft 所獲得之金錢的詳細資料。 也可以讓您了解何時會收到付款與付款金額。

如果您在 Azure Marketplace 中銷售供應專案，您也會在付款**摘要**中看到成功支出的資訊。 如需有關 Azure Marketplace 付款的詳細資訊，請參閱[Microsoft Azure Marketplace 參與原則](https://go.microsoft.com/fwlink/p/?LinkId=722436)和[Microsoft Azure Marketplace 發行者合約](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

> [!NOTE]
> 若要符合付款資格，您的繼續作業必須達到 $50 的[付款閾值](payment-thresholds-methods-timeframes.md)。 如需付款閾值的詳細資訊，請參閱此頁面並回顧[Microsoft Azure Marketplace 發行者合約](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

- [支出報表： Cloud Partner 入口網站與合作夥伴中心之間的差異](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [客戶類型](#customer-types)
- [支出和使用量之間的 Corelation](#corelation-between-payout-and-usage)
- [交易記錄下載](#transaction-history-download-export)
- [帳單問題與支援](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>支出報表： Cloud Partner 入口網站與合作夥伴中心之間的差異

| | 雲端合作夥伴入口網站 | 合作夥伴中心 |
|---------|---------|---------|
| 連結 | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 和 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| 導覽 | 深入解析支出中提供的支出報告 | 合作夥伴中心提供的支出報告-支出圖示 |
| Scope | <ul> <li>針對進行中、收集和付費的集合，可以看到每行專案的交易 </li> <li>報表-顯示訂單建立後的所有明細專案，包括進行中的收集和進行中的帳單，以及尚未符合資格的收集狀態和明細專案。 </li> </ul> | <ul> <li>會在專案被視為合格收益後顯示。</li> <li>客戶必須先支付給 Microsoft，然後 Isv 才會看到付款報告開始。</li> <li>支出報表將不會顯示收集進行中，且正在進行計費。  </li> </ul>  |
| 交易尚未就緒，無法支付支出 | 計費進行中 | 下一個估計付款：支出狀態為 [未處理] 狀態。  |
| 支出狀態 |  | 尚未 <br> 收益符合付款資格。 如獎勵計畫的程式指南中所定義，它會維持在此狀態的冷卻期間。 <br> <br> 近期： <br> 付款順序-在處理付款之前產生暫止的內部評論。 <br> <br> 寄件日期： <br> 付款已傳送到您的銀行。 |

## <a name="customer-types"></a>客戶類型 

### <a name="enterprise-agreement"></a>Enterprise 合約

系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

### <a name="credit-cards-and-monthly-invoice"></a>信用卡和每月發票

客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用將以每月計費。

### <a name="csp-and-direct-pay-users"></a>CSP 和直接付費使用者

例如，如果客戶使用信用卡購買。

## <a name="corelation-between-payout-and-usage"></a>支出和使用量之間的 Corelation 

|描述    |    日期  | 訂單/使用量  | 支付 |
|----------|----------|-----------|-------------|
|訂單期間   | 2019年8月15日-8 月30日-2019 | **相互關聯屬性訂單** <br> <ul> <li>訂單識別碼</li> <li>客戶識別碼</li> </ul> <br> **用法** <br> <ul> <li>客戶識別碼 </li> <li>客戶名稱</li> <li>(UsageReference)發票/LineItemId</li> <li> 預估的延伸費用 <br> 預估付款 (PC) </li> </ul> |  |
|期間結束 (月)   | 2019年8月30日 | | |
|計費日期 | 2019年9月1日 | | |
|客戶付款日期 | 2019年9月1日 | | |
|委付期間 (僅信用卡，30 天) | 2019年9月1日-2019 年9月30日 | | **相互關聯屬性訂單：** <br> <ul><li>AssetId</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **用法** <br> <ul> <li>AssetId</li> <li>客戶識別碼</li> <li>客戶名稱</li> <li>訂單識別碼</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支出狀態：** 尚未 |
|收帳期間開始 | 2019年9月1日 | | |
|收帳期間結束 (最大值，30 天) | 2019年9月30日 | | |
|付款計算日期 (每月第 15 日) | 2019年10月1日 | | **相互關聯屬性** <br> <ul><li>AssetId</li> <li>客戶識別碼</li> <li>客戶名稱</li> </ul> <br> **用法** <br> <ul> <li>AssetId</li> <li>客戶識別碼</li> <li>客戶名稱</li> <li>訂單識別碼</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支出狀態：** 提交 |
|付款日期 | 2019年10月15日 | | **相互關聯屬性** <br> <ul><li>AssetId</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **用法** <br> <ul> <li>AssetId</li> <li>客戶識別碼</li> <li>客戶名稱</li> <li>訂單識別碼</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支出狀態：** 已傳送付款 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Enterprise 合約（每季/每月客戶）

| 描述 |    日期  | 用量 | 支付 |
|----------|----------|---------|-----------|
|訂單期間 | 2019年8月15日-8 月30日-2019 | **相互關聯屬性訂單** <br> <ul> <li>訂單識別碼</li> <li>客戶識別碼</li> </ul> <br> **使用報告** <br> <ul> <li>客戶識別碼 </li> <li>客戶名稱</li> <li>(UsageReference)發票/LineItemId</li> <li> 預估的延伸費用 <br> 預估付款 (PC) </li> </ul> | |
|期間結束 (季) | 2019年9月30日 | | |
|計費日期 | 2019年10月15日 | | |
|委付期間 (僅信用卡，30 天) | n/a | | |
|收帳期間開始 | 2019年10月15日 | | |
|僅信用卡，30天 | 2019年11月1日至2019年11月30日 | | |
|收帳期間結束 (最大值，90 天) | 2020年1月15日 | | |
|客戶付款日期 | 2019年12月30日 | | |
|支出計算 | 2020年1月15日 | | |
|付款日期 | 2020年2月15日 | | **以季為基礎的客戶** <br> <br> **訂單報表** <br> <ul><li>AssetId</li> <li>客戶識別碼</li> <li> 客戶名稱</li> </ul> <br> **用法** <br> <ul> <li>AssetId</li> <li>客戶識別碼</li> <li>客戶名稱</li> <li>訂單識別碼</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支出狀態：** 已傳送 |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>交易記錄下載匯出

此選項會提供您在 [交易歷程記錄] 頁面中看到的每個賺取明細專案的下載、賺取類型、日期、相關聯的交易金額、客戶、產品，以及適用于獎勵計畫的其他交易詳細資料。 

| 資料行名稱     | 描述    | 
|-------------|-------------------------------|
| earningId                      | 每個收益的唯一識別碼                                                                                                       |
| participantId                  | 方案下合作夥伴賺取的主要身分識別                                                                            | 
| participantIdType              | 如果是商店程式和 Azure Marketplace，則大多是獎勵計畫和賣方的程式識別碼                                          | 
| participantName                | 收益合作夥伴的名稱                                                                                                              | 
| partnerCountryCode             | 賺取合作夥伴的地點/國家/地區                                                                                                  |
| programName                    | 獎勵/商店計畫名稱                                                                                                             | 
| transactionId                  | 交易的唯一識別碼                                                                                                    | 
| transactionCurrency            | 原始客戶交易發生的貨幣（不是合作夥伴位置貨幣）                                     | 
| transactionDate                | 交易的日期。 適用于許多交易貢獻給一個收益的程式                                           | 
| transactionExchangeRate        | 用來顯示對應交易美元金額的匯率                                                                 | 
| transactionAmount              | 原始交易貨幣中的交易數量（根據產生的收益）                                              | 
| transactionAmountUSD           | 交易金額（美元）                                                                                                                | 
| 向上                          | 表示獲得收益的商務規則                                                                                                  | 
| earningRate                    | 交易金額所套用的獎勵率，以產生收益                                                                      | 
| quantity                       | 根據程式而有所不同。 表示交易式程式的計費數量                                                            | 
| quantityType                   | 表示數量的類型，例如：計費的數量、MAU                                                                                     |
| earningType                    | 指出是否為費用、退款、合作基金、銷售等。                                                                                          | 
| earningAmount                  | 原始交易貨幣中的賺取金額                                                                                      |
| earningAmountUSD               | 賺取金額（美元）                                                                                                                    |
| earningDate                    | 賺取的日期                                                                                                                      |
| calculationDate                | 在系統中計算賺取的日期                                                                                            |
| earningExchangeRate            | 用來顯示對應美元量的匯率                                                                                  |
| exchangeRateDate               | 用來計算 EarningAmount 美元的匯率日期                                                                                   | 
| paymentAmountWOTax             | 僅限「已傳送」付款的貨幣金額（不含稅）                                                                 |
| paymentCurrency                | 付款設定檔中由合作夥伴選擇的貨幣付款。 僅針對已傳送的付款顯示                                                   |
| paymentExchangeRate            | 使用 ExchangeRateDate 計算付款貨幣中 paymentAmountWOTax 的匯率                                            |
| paymentId            | 付款的唯一識別碼。 這個數位會顯示在您的 bank 語句中                                            |
| paymentStatus            | [付款狀態]                                            |
| paymentStatusDescription            | 付款狀態的易記描述                                            |
| customerId                     | 永遠為空白                                                                                                                     |
| customerName                   | 永遠為空白                                                                                                                     |
| partNumber                     | 永遠為空白                                                                                                                     |
| productName                    | 連結至交易的產品名稱                                                                                                       |
| productId                      | 唯一的產品識別碼                                                                                                                |
| parentProductId                | 唯一的父系產品識別碼。 注意：如果交易沒有父產品，則為 [父系產品識別碼 = 產品識別碼]。 |
| parentProductName              | 父系產品的名稱。 注意：如果交易沒有父產品，則為 [父系產品名稱 = 產品名稱]。   |
| productType                    | 產品類型 (例如應用程式、附加元件、遊戲等等)                                                                                        |
| invoiceNumber                  | 發票編號（僅適用于 EA）                                                                                                  |
| resellerId                     | 轉售商識別碼                                                                                                                      |
| resellerName                   | 轉售商名稱                                                                                                                            |
| transactionType                | 交易類型 (例如購買、退貨、作廢、退款等等)                                                               |
| localProviderSeller            | 記錄的本機提供者/賣方                                                                                                          |
| taxRemitted                    | 免稅金額 (銷售、使用或 VAT/GST 稅額)。                                                                                   |
| taxRemitModel                  | 負責代繳稅額之一方 (銷售、使用或 VAT/GST 稅額)。                                                                    |
| storeFee                       | Microsoft 保留的金額，讓應用程式或附加元件可以在商店中使用。                                            |
| transactionPaymentMethod       | 交易使用的客戶付款方式 (例如，卡片、行動裝置電信業者帳單、PayPal 等)。                                |
| tpan                           | 表示協力廠商 ad 網路                                                                                                     |
| Customercountrycomparer                | 客戶國家/地區                                                                                                                         |
| customerCity                   | 客戶城市                                                                                                                            |
| customerState                  | 客戶狀態                                                                                                                           |
| customerZip                    | 客戶郵遞區號                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | 程式的唯一識別碼                                                                                                        |
| externalReferenceIdLabel       | 唯一識別碼標籤                                                                                                                  |
| transactionCountryCode       | 發生交易的國家/地區代碼                                                                                                                  |
| taxCountry       | 銷售給客戶國家/地區                                                                                                                  |
| taxState       | 銷售給客戶狀態                                                                                                                  |
| taxCity       | 銷售給客戶城市                                                                                                                  |
| taxZipCode       | 銷售給客戶 Zip                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| 程式碼       | 要與程式名稱對應的字串                                                                                                                   |
| earningAmountInLastPaymentCurrency       | 在最後一個付款貨幣中的賺取金額（如果未支付先前付款，欄位將會是空的）                                                                                                                   |
| lastPaymentCurrency       | [上次付款貨幣] （如果未支付先前付款，欄位將會是空的）                                                                                                                   |
| AssetId       | Marketplace 服務客戶訂單的唯一識別碼。  它代表交易的採購明細專案。 可以有多個資產。                                                                                                                   |
| 訂單識別碼       | 與客戶的發票相關                                                                                                                   |
| LineItemId       | 客戶發票中的個別線條                                                                                                                   |
| 客戶國家/地區       | 客戶提供的國家/地區名稱。  這可能與客戶的 Azure 訂用帳戶中的國家/地區不同。                                                                                                                   |
| 客戶 EmailAddress       | 終端客戶所提供的電子郵件地址。  這可能與客戶的 Azure 訂用帳戶中的電子郵件地址不同。                                                                                                                   |
| SkuId       | 發佈期間定義的 SKU 識別碼。 供應專案可能有許多 Sku，但 SKU 只能與單一供應專案相關聯。                                                                                                                   |

>[!Note]
>您可以透過合作夥伴中心的 Cloud Partner 入口網站或分析 區段的 見解 區段，取得交易發行選項的所有報告和深入解析。

## <a name="billing-questions-and-support"></a>計費問題和支援

若要取得有關帳單問題的說明，請洽詢[商業 marketplace 發行者支援](https://aka.ms/marketplacepublishersupport)。
