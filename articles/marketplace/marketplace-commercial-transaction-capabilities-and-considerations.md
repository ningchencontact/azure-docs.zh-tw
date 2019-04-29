---
title: Marketplace ‎商業交易功能和考量 | Azure
description: 此文章描述提供項目類型的交易定價、計費、發票開立和付款考量。
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: 3c07f7ac2b6407a3edf53fd5d3a4b402714fefbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61064992"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Azure Marketplace ‎商業交易功能和考量

Azure Marketplace 發行選項提供獨特的方式來將雲端軟體和服務提供者與客戶連接。 此文章涵蓋 Azure Marketplace 中的下列商務相關主題：

* Marketplace 發行選項
* 交易一般概觀
* 交易計費模型
* 交易需求

## <a name="marketplace-publishing-options"></a>Marketplace 發行選項

Azure Marketplace 發行者可取得下列發行選項。

### <a name="list--trial-publishing-options"></a>清單和試用版發行選項

在 Azure Marketplace 中，發行者可利用清單和試用版發行選項，進行促銷和使用者取得。 使用清單或試用版發行選項，Microsoft 不會在發行者的軟體授權交易中直接參與，且沒有相關的交易費用。 發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。 使用清單和試用版發行選項，發行者保有從客戶收取的發行者軟體授權費用的 100%。 

### <a name="transact-publishing-option"></a>交易發行選項

除了清單和試用版發行選項之外，Azure Marketplace 發行者還可取得交易發行選項。   它利用 Microsoft 可全球取得的商務功能。 此選項可讓 Microsoft 代替發行者裝載雲端市集交易。

## <a name="transact-general-overview"></a>交易一般概觀

當使用交易發行選項時，Microsoft 可讓的協力廠商軟體的銷售和客戶的 Azure 訂用帳戶的某些供應項目類型的部署。 當發行者在 Azure Marketplace 中選取計費模型和供應項目類型時，必須考慮 Azure 基礎結構費用的計費，以及發行者自己的軟體授權費用。

Azure Marketplace 中的交易發行選項目前支援下列提供項目類型：虛擬機器、Azure 應用程式或 SaaS 應用程式。

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

* 預付型方案：軟體授權費用會依使用的 Azure 基礎結構，呈現為每小時、每核心 (vCPU) 費率。 這只適用於虛擬機器和 Azure 應用程式。

* 月租方案定價 (網站型)：軟體授權費用以每月週期性費用呈現。  這只適用於 SaaS 應用程式和 Azure 應用程式 - 受控應用程式。

* 軟體免費試用版：30 天或 90 天軟體授權不收費。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免費和自備授權 (BYOL) 定價

在發行免費或自備授權交易供應項目的情況下，Microsoft 不會扮演為您的您軟體授權費用進行輔助銷售交易的角色。 如同清單和試用版發行選項，發行者保有發行者軟體授權費用的 100%。 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>預付型方案和月租方案 (網站型) 定價

在發行預付型方案或月租方案交易供應項目的情況下，Microsoft 會提供處理軟體授權購買、退貨及退款的技術和服務。 在此案例中，發行者針對這些目的授權 Microsoft 為代理人。 發行者允許 Microsoft 協助軟體授權交易，同時保留其為賣方、提供者、散發者和授權人的角色。

Microsoft 讓客戶能訂購、授權和使用發行者軟體，客戶必須遵守 Azure Marketplace 和發行者使用者授權合約 (請參閱 Cloud Partner 入口網站) 的條款與條件。 發行者必須在市集供應項目中提供其使用者授權合約。

透過市集處理的訂單會在單一帳單中向客戶的 Azure 訂用帳戶收費，其收費方式和客戶的 Azure 基礎結構成本一樣。 客戶可以使用其 Azure 訂用帳戶所慣用的發票開立方法和付款方式。

### <a name="free-software-trials"></a>免費軟體試用

針對交易發行的案例，發行者可以讓授權在 30 天或 90 天內可免費取得。 此折扣功能不包含因使用合作夥伴解決方案而產生的 Azure 基礎結構使用量成本。

### <a name="private-offers"></a>私人供應項目

除了使用供應項目類型和計費模型來創造供應項目的獲利，發行者也可以交易解決方案供應項目的私人版本，按照交涉、交易特定的定價來完成，並使用自訂映像來自訂設定。 所有 3 個交易發行選項都支援私人供應項目。

定價選項可以高於或低於公開顯示的定價。  私人供應項目可用於為供應項目折扣或加入進階功能。 藉由將客戶的 Azure 訂用帳戶加入供應項目層級的允許清單，他們就能取得私人供應項目。

### <a name="examples"></a>範例

**隨用隨付** 

* 如果您啟用 [預付型方案] 選項，就會有下列成本結構。

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

**SaaS 應用程式月租方案 (透過 Azure 銷售)**

此選項必須設定成透過 Microsoft 來銷售，且可以使用在供應項目層級定義的一或多個一般費率每月方案來定價。

* 如果您啟用 [透過 Azure 銷售] 選項，就會有下列結構成本。

|授權成本       | 每月 $100.00  |
|--------------|---------|
|Azure 使用量成本 (D1/1 核心)    | 直接向發行者收費，不是向客戶收費 |
|*由 Microsoft 向客戶收取的費用*    |  *每月 $100.00 (注意：發行者必須在授權費用中計算任何產生的或處理的基礎結構成本)*  |

* 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。

|Microsoft 收取的費用  | 每月 $100.00  |
|---------|---------|
|Microsoft 向您支付授權成本的 80%    |   每月 $80.00     |
|Microsoft 保留授權成本的 20%   |  每月 $20.00       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>客戶發票開立、付款和收帳

**發票開立和付款**

發行者可以使用客戶慣用的發票開立方法來傳遞月租方案或 PAYGO 軟體授權費用。

**Enterprise 合約** 

如果客戶慣用的發票開立方法是 Microsoft Enterprise 合約，您的軟體授權費用會使用此發票開立方法來計費，作為分項成本獨立於任何 Azure 特定的使用量成本。

**信用卡和每月發票** 

客戶也可以使用信用卡和每月發票來付款。 在此情況下，您的軟體授權費用的計費方式會如同 Enterprise 合約案例，作為分項成本獨立於任何 Azure 特定的使用量成本。

例如，如果客戶使用信用卡購買：

|描述    |    date  |
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

| 描述 |    date  |
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

在選取月租方案或預付型方案定價模型的情況下，Microsoft 是作為發行者的代理人，負責計費、付款和收帳的所有層面。

### <a name="publisher-payout-and-reporting"></a>發行者付款和報告

* 若未另外指定，任何由 Microsoft 代理收帳的軟體授權費用都依 20% 手續費收費，並在發行者付款時扣除。

* 客戶通常會使用 Enterprise 合約或以信用卡付款的預付型方案合約來購買。 合約類型會決定計費、發票開立、收帳和付款時間。

>[!NOTE] 
>交易發行選項的所有報告和見解，都可透過 Cloud Partner 入口網站的 [見解] 區段取得。

#### <a name="billing-questions-and-support"></a>計費問題和支援

如需詳細資訊和法律原則，請參閱可在 Cloud Partner 入口網站取得的[發行者合約](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) \(英文\)。

若要取得帳單問題的說明，請建立[支援事件](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423)，並視所使用的供應項目類型選擇虛擬機器或 Web 應用程式 (又稱 SaaS 應用程式)。

## <a name="transact-requirements"></a>交易需求

本節涵蓋不同供應項目類型的交易需求。

### <a name="requirements-for-all-offer-types"></a>所有提供項目類型的需求

**開發人員中心和 Microsoft 帳戶** 

* 交易發行選項需要開發人員中心和 Microsoft 帳戶，不論該供應項目的定價模型為何。
* 開發人員中心帳戶持有 Microsoft 代表發行者向客戶收費及付款給發行者所需的所有相關財務詳細資料。
* 雖然您可能會跨這兩個帳戶使用的相同組織或 Microsoft 登入詳細資料，開發人員中心是從 Cloud Partner 入口網站的帳戶不同的帳戶。 若要使用交易發行選項，發行者比需完成開發人員中心帳戶註冊程序，才能註冊存取 Cloud Partner 入口網站。

如需有關設定這些帳戶的詳細資訊，請參閱[成為雲端市集發行者](https://docs.microsoft.com/azure/marketplace/become-publisher)。

### <a name="requirements-for-specific-offer-types"></a>特定供應項目類型的需求

交易發行選項只能用來搭配下列市集供應項目類型： 

**虛擬機器** 

從免費、自備授權或預付型方案定價模型選取，並以在供應項目層級定義的 SKU 呈現。 在客戶的 Azure 帳單上，Microsoft 會將發行者軟體授權費用與基礎 Azure 基礎結構費用分開呈現。 Azure 基礎結構費用是由使用發行者軟體而產生。

**Azure 應用程式：解決方案範本或受控應用程式** 

必須佈建一或多個虛擬機器，並透過虛擬機器定價總和來提取。 對於單一方案上的受控應用程式，可以選取一般費率的每月月租方案作為定價模型，而不是虛擬機器定價。 在這兩種情況下，Azure 基礎結構使用費用會與軟體授權費用分開傳遞給客戶，但是在相同的帳單上。

## <a name="next-steps"></a>後續步驟

* 請依據供應項目類型區段，檢閱發佈選項中的資格需求，以完成供應項目的選取和設定。
* 請依據店面檢閱發佈模式，以取得解決方案如何對應至供應項目類型和組態的範例。
* 成為 Marketplace 發行者，並登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com)來建立及設定您的供應項目。
