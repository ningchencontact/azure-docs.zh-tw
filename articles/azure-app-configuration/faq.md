---
title: Azure 應用程式組態常見問題 |Microsoft Docs
description: 關於 Azure 應用程式組態的常見問題
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: dbfb6a1c4c53b1bd255560e688d3dc0cf3835a3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469627"
---
# <a name="azure-app-configuration-faq"></a>Azure 應用程式組態常見問題

本文解決有關 Azure 應用程式組態的常見問題。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>應用程式組態與 Azure Key Vault 有何不同？

應用程式組態是針對一組不同的使用案例而設計的：它可協助開發人員管理應用程式設定，並控制功能的可用性。 它的目的是要簡化處理複雜設定資料的許多工。

應用程式組態支援：

- 階層命名空間
- 條碼
- 大量查詢
- 批次抓取
- 特殊化的管理作業
- 功能管理使用者介面

應用程式組態是 Key Vault 的互補，而兩者應該在大部分的應用程式部署中並存使用。

## <a name="should-i-store-secrets-in-app-configuration"></a>我應該將秘密儲存在應用程式組態嗎？

雖然應用程式組態提供強化的安全性，但 Key Vault 仍然是儲存應用程式秘密的最佳位置。 Key Vault 提供硬體層級加密、細微存取原則和管理作業（例如憑證輪替）。

## <a name="does-app-configuration-encrypt-my-data"></a>應用程式組態加密我的資料嗎？

是。 應用程式組態會加密它所保留的所有金鑰值，並將網路通訊加密。 金鑰名稱是用來做為抓取設定資料的索引，並不會加密。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>應用程式組態中儲存的索引鍵和值是否有任何大小限制？

單一索引鍵/值專案有10KB 的限制。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何儲存多個環境的設定（測試、預備、生產等等）？

目前，您可以控制誰能夠存取每個存放區層級的應用程式組態。 針對每個需要不同許可權的環境使用個別的存放區。 這種方法可為您提供最佳的安全性隔離。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用應用程式組態的建議方式為何？

請參閱[最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>應用程式組態的成本是多少？

公開預覽期間可免費使用此服務。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收新版本的公告和與應用程式組態相關的其他資訊？

訂閱我們的[GitHub 公告](https://github.com/Azure/AppConfiguration-Announcements)存放庫。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何報告問題或提供建議？

您可以直接在[GitHub](https://github.com/Azure/AppConfiguration/issues)上聯繫我們。

## <a name="next-steps"></a>後續步驟

* [關於 Azure 應用程式組態](./overview.md)
