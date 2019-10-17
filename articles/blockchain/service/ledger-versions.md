---
title: Azure 區塊鏈服務總帳版本、修補及升級
description: 概述 Azure 區塊鏈 Service 中支援的總帳版本，包括有關系統修補和系統管理和使用者管理之升級的原則。
services: azure-blockchain
keywords: 區塊鏈
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 40719f1c353b8961e14815cb61e7a862b47fc6f5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329128"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支援的 Azure 區塊鏈 Service 總帳版本

Azure 區塊鏈 Service 會使用以乙太坊為基礎的[仲裁](https://www.goquorum.com/developers)總帳，其設計目的是要處理一組已知參與者（在 Azure 區塊鏈 Service 中識別為聯盟）內的私人交易。

目前，Azure 區塊鏈 Service 支援[仲裁版本 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3)和[Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

仲裁中的版本控制是透過主要、次要和修補發行來完成。 例如，如果仲裁版本是2.0.1，則版本類型會分類如下：

|主要 | Minor  | 修補程式  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 區塊鏈 Service 會在從仲裁提供的30天內，自動將仲裁的修補版本更新為現有執行中的成員。

## <a name="availability-of-new-ledger-versions"></a>新總帳版本的可用性

Azure 區塊鏈 Service 提供仲裁廠商所提供60天內的仲裁總帳最新主要和次要版本。 在布建新的成員和聯盟時，最多會提供四個次要版本供聯盟選擇。 目前不支援從升級至主要或次要版本。 例如，如果您執行的是2.x 版，則目前不支援升級至版本3.x。 同樣地，如果您執行的是2.2 版，則目前不支援升級至2.3 版。

## <a name="next-steps"></a>後續步驟

[Azure 區塊鏈 Service 中的限制](limits.md)
