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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027881"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支援的 Azure 區塊鏈 Service 交易記錄資料庫版本

Azure 區塊鏈服務可讓您使用以太坊基礎[仲裁](https://github.com/jpmorganchase/quorum/wiki)分類帳專為已知的參與者，識別為協會在 Azure 區塊鏈 Service 中的群組內的私用交易的處理。

目前，Azure 區塊鏈服務支援[仲裁 2.2.1 版](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1)並[Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

仲裁中的版本控制是透過主要版本、 主要的點發行版本和次要的點發行版本而定。 比方說，如果主要版本的仲裁版本 2.0.1，2，0 是主要的點的版本，和 1 是次要的點發行版本。 服務自動修補交易記錄資料庫的次要點發行的版本。 目前不支援升級的主要和主要的單點發行版本。

## <a name="availability-of-new-ledger-versions"></a>新的交易記錄資料庫版本的可用性

Azure 區塊鏈服務提供的分類帳製造商所提供的 60 天內的交易記錄資料庫的最新版本。 最多四個主要的單點發行版本可供 consortia 佈建的新成員和協會時從中選擇。

## <a name="next-steps"></a>後續步驟

[Azure 區塊鏈服務中的限制](limits.md)
