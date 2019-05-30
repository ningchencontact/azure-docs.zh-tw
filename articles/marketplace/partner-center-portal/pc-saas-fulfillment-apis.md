---
title: SaaS 履行 Api |Azure Marketplace
description: 引進了 「 履行 」 可讓您整合您的 SaaS Api 提供與 Azure Marketplace 的版本。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258920"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

SaaS 履行 Api 可讓獨立軟體廠商 (Isv) 與 Azure Marketplace 整合其 SaaS 應用程式。 這些 Api 可讓 ISV 應用程式，加入所有的商務功能頻道： 直接存取，合作夥伴主導 （轉銷商） 及欄位 led。  也就是列出可交易的 SaaS 供應項目在 Azure Marketplace 上的需求。

> [!WARNING]
> 此 API 的目前版本為第 2 版，應該使用的所有新的 SaaS 供應項目。  第 1 版的 api 已過時，且正加以維護，以支援現有的供應項目。


## <a name="business-model-support"></a>商務模型支援

此 API 支援下列的商務模型功能;您可以：

* 指定多個方案的供應項目。 這些計劃有不同的功能，而且可能定價方式不同。
* 提供供應項目上每個站台或每位使用者計費模型為基礎。
* 提供每月和年 （預先付費） 計費選項。
* 提供給客戶，根據交涉的商務協議的私用定價。


## <a name="next-steps"></a>後續步驟

如果您尚未這樣做，註冊您的 SaaS 應用程式中[Azure 入口網站](https://ms.portal.azure.com)中所述[註冊 Azure AD 應用程式](./pc-saas-registration.md)。  之後，使用此介面的最新版本進行開發：[SaaS 履行 API 第 2 版](./pc-saas-fulfillment-api-v2.md)。
