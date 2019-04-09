---
title: SaaS 履行 Api-Azure Marketplace |Microsoft Docs
description: 引進了 「 履行 」 可讓您整合您的 SaaS Api 提供與 Azure Marketplace 的版本。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009440"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

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
