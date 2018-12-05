---
title: Azure API 管理中的訂用帳戶 | Microsoft Docs
description: 深入了解 Azure API 管理中訂用帳戶的概念。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621715"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API 管理中的訂用帳戶

訂用帳戶是 Azure API 管理 (APIM) 中的重要概念。 這是 API 取用者取得透過 APIM 執行個體發佈之 API 存取權的最常用方式。 本文提供下列概念的概觀。

## <a name="what-is-subscriptions"></a>什麼是訂用帳戶

透過 APIM 發佈 API 時，安全地存取這些 API 的最簡單和最常用的方式是使用訂用帳戶金鑰。 換句話說，需要使用已發佈 API 的開發人員在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 否則，APIM 閘道會立即拒絕呼叫，並且不會將呼叫轉送到後端服務。

若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 訂用帳戶基本上是一組訂用帳戶金鑰的具名容器。 無論 API 發行者是否批准，需要使用已發佈 API 的開發人員都可取得訂用帳戶。 API 發行者還可以代表 API 取用者直接建立訂用帳戶。

> [!TIP]
> APIM 也支援其他機制來保護對 API 的存與，包括 [OAuth2.0](api-management-howto-protect-backend-with-aad.md)、[用戶端憑證](api-management-howto-mutual-certificates-for-clients.md)和 [IP 允許清單](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>訂用帳戶的範圍

訂用帳戶可以與各種範圍相關聯：產品、所有 API 或個別的 API。

### <a name="subscriptions-for-a-product"></a>產品的訂用帳戶

傳統上，APIM 中的訂用帳戶始終與單一 [API 產品](api-management-terminology.md)範圍相關聯。 開發人員可以在開發人員入口網站上找到產品清單，並為他們想要使用的產品提交訂用帳戶要求。 訂用帳戶要求核准之後 (自動或由 API 發行者)，開發人員可以使用其中的金鑰存取產品中的所有 API。

![產品訂用帳戶](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 在某些情況下，API 發行者可能會想要公開發佈 API 產品，而不需要訂用帳戶。 他們可以取消核取 Azure 入口網站中產品 [設定] 頁面中的 [需要訂用帳戶] 選項。 因此，無需 API 金鑰即可存取產品下的所有 API。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>適用於所有 API 或個別 API 的訂用帳戶

> [!NOTE]
> 目前，這項功能僅可在 API 管理使用層中使用。

當我們引進 APIM 的[使用](https://aka.ms/apimconsumptionblog)層時，我們進行了一些變更以簡化金鑰管理。 首先，我們新增了兩個訂用帳戶範圍 - 所有 API 和單一 API。 訂用帳戶的範圍不再限於 API 產品。 現在可以建立金鑰以授與對 API (或 APIM 執行個體中的所有 API) 的存取權，而不需要先建立一項產品，並向其新增 API。 此外，每個 APIM 執行個體現在都隨附一個不可變的所有 API 訂用帳戶，這使得在測試主控台內測試和偵錯 API 變得更加輕鬆容易。

第二，APIM 現在允許「獨立」訂用帳戶。 訂用帳戶不再需要與開發人員帳戶相關聯。 這在諸如多個開發人員或小組共用訂用帳戶時的情況下非常有用。

最後，API 發行者現在可以[直接在 Azure 入口網站中建立訂用帳戶](api-management-howto-create-subscriptions.md)：

![彈性的訂用帳戶](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>後續步驟
如需有關 API 管理的詳細資訊：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)
+ 如需深入了解 API 管理，請依照我們的[教學課程](import-and-publish.md)
+ 針對常見的問題，請查看我們的[常見問題集頁面](api-management-faq.md)