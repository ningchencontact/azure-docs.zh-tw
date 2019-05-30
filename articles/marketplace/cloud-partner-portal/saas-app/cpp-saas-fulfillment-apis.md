---
title: SaaS 履行 Api |Azure Marketplace
description: 引進了 「 履行 」 可讓您整合您的 SaaS Api 提供與 Azure Marketplace 的版本。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: e7a01af1eba865b0a088b0fa7226273527abd70e
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257413"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

> [!IMPORTANT] 
> SaaS 提供的功能已移轉到[Microsoft 合作夥伴中心](https://partner.microsoft.com/dashboard/directory)。  所有新的發行者必須使用合作夥伴中心建立新的 SaaS 供應項目，以及管理現有的供應項目。  SaaS 供應項目目前的發行者 batchwise 移轉從 Cloud Partner 入口網站來合作夥伴中心。  Cloud Partner 入口網站會顯示狀態訊息，指出何時已移轉特定的現有供應項目。
> 如需詳細資訊，請參閱 <<c0> [ 建立新的 SaaS 供應項目](../../partner-center-portal/create-new-saas-offer.md)。

SaaS 履行 Api 可讓獨立軟體廠商 (Isv) 與 Azure Marketplace 整合其 SaaS 應用程式。 這些 Api 可讓 ISV 應用程式，加入所有的商務功能頻道： 直接存取，合作夥伴主導 （轉銷商） 及欄位 led。  也就是列出可交易的 SaaS 供應項目在 Azure Marketplace 上的需求。

> [!WARNING]
> 此 API 的目前版本為第 2 版，它應該用於所有新的 SaaS 提供。  第 1 版的 api 已過時，且正加以維護，以支援現有的供應項目。


## <a name="business-model-support"></a>商務模型支援

此 API 支援下列的商務模型功能;您可以：

* 指定多個方案的供應項目。 這些計劃有不同的功能，而且可能定價方式不同。
* 提供供應項目上每個站台或每位使用者計費模型為基礎。
* 提供每月和年 （預先付費） 計費選項。
* 提供給客戶，根據交涉的商務協議的私用定價。


## <a name="next-steps"></a>後續步驟

如果您尚未這樣做，註冊您的 SaaS 應用程式中[Azure 入口網站](https://ms.portal.azure.com)中所述[註冊 Azure AD 應用程式](./cpp-saas-registration.md)。  之後，使用此介面的最新版本進行開發：[SaaS 履行 API 第 2 版](./cpp-saas-fulfillment-api-v2.md)。
