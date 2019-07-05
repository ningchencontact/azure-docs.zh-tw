---
title: Marketplace ‎商業交易功能和考量 | Azure
description: 此文章描述提供項目類型的交易定價、計費、發票開立和付款考量。
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445446"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Marketplace 商業交易功能和考量

本文章涵蓋下列商務相關主題中的商業的 marketplace

* Marketplace 發行選項
* 交易一般概觀
* 交易計費模型
* 交易需求

## <a name="marketplace-publishing-options"></a>Marketplace 發行選項

下列的發行選項可用於商業 marketplace 發行者。

### <a name="list--trial-publishing-options"></a>清單和試用版發行選項

發行者可以利用清單、 試用版，以及發行選項的 BYOL 促銷和使用者取得用途。 使用這些選項，Microsoft 直接不參與發行者的軟體授權的交易，而且沒有任何相關聯的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。 

### <a name="transact-publishing-option"></a>交易發行選項

除了清單和試用版的發行選項，交易發行選項是提供給發佈者。 這會充分利用 Microsoft 的全域可用的商務功能，並可讓 Microsoft 代表 「 發行者 」 端的主應用程式雲端 marketplace 交易。

## <a name="transact-general-overview"></a>交易一般概觀

當使用交易發行選項時，Microsoft 可讓的協力廠商軟體的銷售和客戶的 Azure 訂用帳戶的某些供應項目類型的部署。 「 發行者 」 必須考慮基礎結構費用的計費和發行者自己的軟體授權費用，當選取的計費模型，並供應項目類型。

交易發行選項目前支援下列供應項目類型：虛擬機器、 Azure 應用程式和 SaaS 應用程式。


![[在 Azure Marketplace 中交易企業交易]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>基礎結構成本計費

**虛擬機器和 Azure 應用程式**

對於虛擬機器和 Azure 應用程式，Azure 基礎結構的使用量費用是向客戶的 Azure 訂用帳戶計費。  基礎結構的使用費用和軟體提供者的授權費用，在客戶的發票上會分開計價及呈現。

**SaaS 應用程式**

對於 SaaS 應用程式，發行者必須將 Azure 基礎結構使用費用及軟體授權費用為算為單一的成本項目。  它被以美元給客戶。 Azure 基礎結構使用量是對合作夥伴直接管理及計費。  客戶不會看到實際的基礎結構使用量費用。  發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。  軟體授權費用不是計量付費或以使用情況為基礎。

## <a name="transact-billing-models"></a>交易計費模型

視使用的交易選項，發行者的軟體授權費用可以呈現如下：  

* 免費：軟體授權不收費。 

* 自備授權 (BYOL)：軟體授權任何適用的費用，在發行者與客戶之間直接管理。 Microsoft 僅處理 Azure 基礎結構費用。 (僅限虛擬機器和 Azure 應用程式。)

* 隨用隨付：軟體授權費用會依使用的 Azure 基礎結構，呈現為每小時、每核心 (vCPU) 費率。 這只適用於虛擬機器和 Azure 應用程式。

* • 訂用帳戶價格：軟體授權費用會以每月或年，週期性費用為每一基座或固定費率計費。 這只適用於 SaaS 應用程式和 Azure 應用程式 - 受控應用程式。

* 軟體免費試用版：30 天或 90 天軟體授權不收費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>隨用隨付和月租方案 (網站型) 定價

WPay 作為-付和訂用帳戶價格時發行的隨用隨付或訂用帳戶的 「 交易 」 優惠，Microsoft 提供的技術和服務，以處理軟體授權購買，會傳回，及退款。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 可讓客戶訂單、 授權和使用發行者軟體的條款及條件的 Microsoft 商業服務商場和 「 發行者 」 的使用者授權合約與否。 發行者必須提供其使用者授權合約，或選取[標準的合約](https://docs.microsoft.com/azure/marketplace/standard-contract)建立供應項目時。


### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，發行者可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用提供的類型和計費模型，以創造營收的供應項目，發行者可以 transact 私用供應項目，完成交涉，特定交易的定價，或自訂設定。 所有 3 個交易發行選項都支援私人供應項目。

此選項可讓您更高或較低價格比公開可用的供應項目。 私人供應項目可用於為供應項目折扣或加入進階功能。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。


### <a name="examples"></a>範例

**隨用隨付** 

* 如果您啟用 [隨用隨付] 選項，就會有下列成本結構。

|授權成本  | 每小時美金 $1.00 元  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $1.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $1.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $1.14 元  |
|---------|---------|
|Microsoft 向您支付授權成本的 80%|   每小時美金 $0.80 元     |
|Microsoft 保留授權成本的 20%  |  每小時美金 $0.20 元       |
|Microsoft 保留 Azure 使用量成本的 100% | 每小時美金 $0.14 元 |

**自備授權 (BYOL)**

* 如果您啟用 [BYOL] 選項，就會有下列成本結構。

|授權成本  | 由您協商和收取的授權費用  |
|---------|---------|
|Azure 使用量成本 (D1/1 核心)    |   每小時美金 $0.14 元     |
|*由 Microsoft 向客戶收取的費用*    |  *每小時美金 $0.14 元*       |

* 在此案例中，使用您發佈的 VM 映像時，Microsoft 會收取每小時 $0.14 美元的費用。

|Microsoft 收取的費用  | 每小時美金 $0.14 元  |
|---------|---------|
|Microsoft 保留 Azure 使用量成本    |   每小時美金 $0.14 元     |
|Microsoft 保留授權成本的 0%   |  每小時美金 $0.00 元       |

**SaaS 應用程式的訂用帳戶**

這個選項必須設定為透過 Microsoft 銷售，並可以在以統一費率，或每位使用者每月或年度計費。
• 如果您啟用 Microsoft 選項 SaaS 供應項目，透過銷售，則您有下列的成本結構。

|授權成本       | 每月 $100.00  |
|--------------|---------|
|Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
|*由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (注意：發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |

* 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
* 降低 Marketplace 服務費用有資格的合作夥伴將會看到減少的交易費用 SaaS 上提供從 2019 年直到 2020 年 6 月。 在此案例中，Microsoft 會針對您的軟體授權的 $100.00 美元，並支付美金，鍵入 90.00 出到 「 發行者 」。

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80% <br> \* Microsoft 向您支付授權成本的 90%的任何合格的 SaaS 應用程式   |   每月 $80.00 <br> \* $，鍵入 90.00 每月    |
|Microsoft 保留授權成本的 20% <br> \* Microsoft 保留授權成本的任何合格的 SaaS 應用程式的 10%。  |  每月 $20.00 <br> \* $10.00     |

* **降低的 Marketplace 服務費用：** 某些您在我們的商業 Marketplace 發佈的 SaaS 產品，Microsoft 會減少其 Marketplace 服務費用從 20%（如 Microsoft Publisher Agreement 所述） 到 10%。  為了讓您符合資格的產品，您的產品中至少一個必須指定由 Microsoft 為 IP 共同銷售準備就緒或 IP 共同銷售排定優先順序。 若要接收此縮減的 Marketplace 服務費用的月份，資格必須符合該行事曆月份結束前至少五 （5） 工作天。 減少 Marketplace 服務費用不會套用到 Vm、 受管理應用程式或任何其他商業 Marketplace 透過提供的產品。  此降低 Marketplace 服務費用會提供完整的供應項目，於 2019 5 月 1 日和 2020 年 6 月 30 日之間的 Microsoft 所收集的授權費用。  該時間之後，Marketplace 服務費用將會回到其正常的數量而定。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**發票開立和付款**

發行者可以使用客戶慣用的發票開立方法來傳遞月租方案或 PAYGO 軟體授權費用。

**Enterprise 合約** 

如果客戶慣用的發票開立方法是 Microsoft Enterprise 合約，您的軟體授權費用會使用此發票開立方法來計費，作為分項成本獨立於任何 Azure 特定的使用量成本。

**信用卡和每月發票** 

客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

例如，如果客戶使用信用卡購買：

|說明    |    Date  |
|----------|----------|
|訂單期間   | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期間結束 (月)   | 2018 年 8 月 30 日 |
|計費日期 | 2018 年 9 月 1 日 |
|客戶付款日期 | 2018 年 9 月 1 日 |
|委付期間 (僅信用卡，30 天) | 2018 年 9 月 1日 - 2018 年 9 月 30 日 |
|收帳期間開始 | 2018 年 9 月 1 日 |
|收帳期間結束 (最大值，30 天) | 2018 年 9 月 30 日 |
|付款計算日期 (每月第 15 日) | 2018 年 10 月 1 日 |
|付款日期 | 2018 年 10 月 15 日 |

如果客戶使用 Enterprise 合約購買：

| 說明 |    Date  |
|----------|----------|
|訂單期間 | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期間結束 (季) | 2018 年 9 月 30 日 |
|計費日期 | 2018 年 10 月 15 日 |
|委付期間 (僅信用卡，30 天) | n/a |
|收帳期間開始 | 2018 年 10 月 15 日 |
|收帳期間結束 (最大值，90 天) | 2019 年 1 月 15日 |
|客戶付款日期 | 2018 年 12 月 30 日 |
|付款計算日期 (每月第 15 日) | 2019 年 1 月 15日 |
|付款日期 | 2019 年 2 月 15 日 |

**免費點數與金錢履約承諾** 

有些客戶選擇要使用金錢履約承諾，在 Enterprise 合約中預付給 Azure，或已收到用於 Azure 的免費點數。 雖然可以使用這些點數來支付 Azure 使用量，但不能用它們支付發行者軟體授權費用。

**計費和收帳** 

發行者軟體授權計費是使用客戶所選取的發票開立方法來呈現，並遵循發票開立時間軸。 系統會針對市集軟體授權，向沒有 Enterprise 合約的客戶每月計費。 系統會透過每季呈現的發票，向有 Enterprise 合約的客戶計費。

在選取月租方案或隨用隨付定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

* 若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

* 客戶通常會使用 Enterprise 合約或以信用卡付款的隨用隨付合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE] 
>所有的報告和深入解析的交易發行選項可透過合作夥伴中心的 Cloud Partner 入口網站或分析區段的 [Insights] 區段。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱可在 Cloud Partner 入口網站取得的[發行者合約](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) \(英文\)。

若要取得計費問題的說明，請連絡[商業 marketplace 發行者支援](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應項目類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

- Microsoft 帳戶和財務資訊所需的交易發行選項，不論該供應項目的定價模型。
- 必要的財務資訊包括支出帳戶和稅務設定檔。

如需有關設定這些帳戶的詳細資訊，請參閱 <<c0> [ 管理您的合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details)。


### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型： 

**虛擬機器** 

從免費、自備授權或隨用隨付定價模型選取，並以在供應項目層級定義的 SKU 呈現。 在客戶的 Azure 帳單上，Microsoft 會將發行者軟體授權費用與基礎 Azure 基礎結構費用分開呈現。 Azure 基礎結構費用是由使用發行者軟體而產生。

**Azure 應用程式：解決方案範本或受控應用程式** 

必須佈建一或多個虛擬機器，並透過虛擬機器定價總和來提取。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在某些情況下，Azure 基礎結構的使用費用會傳遞給客戶個別軟體授權費用，但在相同的帳單上。 不過，如果您設定受管理的應用程式的 ISV 基礎結構費用，供應項目到 「 發行者 」 計費的 Azure 資源，客戶會收到一般的費用，包括基礎結構、 軟體授權和管理服務的成本。

## <a name="next-steps"></a>後續步驟

* 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
* 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
