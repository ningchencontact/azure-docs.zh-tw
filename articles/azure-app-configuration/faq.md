---
title: Azure 應用程式設定常見問題集 |Microsoft Docs
description: Azure 應用程式組態相關的常見問題集
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393340"
---
# <a name="azure-app-configuration-faq"></a>Azure 應用程式設定常見問題集

本文說明 Azure 應用程式設定的相關常見問題的解答。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>應用程式組態與 Azure Key Vault 有何不同？

應用程式設定專為一組不同的使用案例： 它可協助開發人員管理應用程式設定和控制功能的可用性。 它旨在簡化許多複雜的組態資料所使用的工作。

應用程式設定支援：

- 階層式命名空間
- 標記
- 廣泛的查詢
- 批次擷取
- 特製化的管理作業
- 功能管理使用者介面

應用程式設定會對應至金鑰保存庫，並應該並排顯示使用兩個，在大部分的應用程式部署。

## <a name="should-i-store-secrets-in-app-configuration"></a>應該在應用程式組態中儲存祕密嗎？

雖然應用程式設定提供強固的安全性，Key Vault 仍然是儲存應用程式祕密的最佳位置。 金鑰保存庫會提供硬體層級加密、 更細微的存取原則，以及管理作業，例如憑證旋轉。

## <a name="does-app-configuration-encrypt-my-data"></a>應用程式組態是否會加密資料？

是。 應用程式組態會在所有的索引鍵值，它會保留，並它加密的網路通訊。 索引鍵名稱可做為擷取組態資料的索引，並不會加密。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何儲存設定 （測試、 預備、 生產環境，等等） 的多個環境？

目前，您可以控制誰能存取應用程式設定在每個存放區層級。 針對每個需要不同的權限的環境中使用不同的存放區。 此方法會提供最佳的安全性隔離。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用應用程式組態的建議的方式是什麼？

請參閱[最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>應用程式組態的成本多少？

若要使用公開預覽期間免費服務。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何回報問題或提供建議？

您可以連上我們直接依據[GitHub](https://github.com/Azure/AppConfiguration/issues)。

## <a name="next-steps"></a>後續步驟

* [有關 Azure 應用程式組態](./overview.md)
