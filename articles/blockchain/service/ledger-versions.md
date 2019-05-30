---
title: Azure 區塊鏈服務支援交易記錄資料庫版本、 修補和升級
description: Azure 區塊鏈服務，包括修補和系統管理的系統和使用者管理的升級相關的原則中的支援的總帳版本的概觀。
services: azure-blockchain
keywords: 區塊鏈
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399094"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支援的 Azure 區塊鏈 Service 交易記錄資料庫版本

Azure 區塊鏈服務可讓您使用以太坊基礎[仲裁](https://www.goquorum.com/developers)分類帳專為已知的參與者，識別為協會在 Azure 區塊鏈 Service 中的群組內的私用交易的處理。

目前，Azure 區塊鏈服務支援[仲裁 2.2.1 版](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1)並[Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

仲裁中的版本控制透過主要、 次要和修補程式版本。 比方說，如果仲裁版本 2.0.1，發行型別會分類，如下所示：

|主要 | Minor  | 修補程式  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 區塊鏈服務會自動將更新仲裁的修補程式的版本執行的現有成員所提供的仲裁的 30 天內。

## <a name="availability-of-new-ledger-versions"></a>新的交易記錄資料庫版本的可用性

Azure 區塊鏈服務的仲裁製造商所提供的 60 天內提供的最新主要和次要版本仲裁分類帳。 最多四個次要的版本可供 consortia 佈建的新成員和協會時從中選擇。 目前不支援升級從主要或次要版本。

## <a name="next-steps"></a>後續步驟

[Azure 區塊鏈服務中的限制](limits.md)
