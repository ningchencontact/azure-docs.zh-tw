---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文將概述專用的 Azure 事件中樞，它可讓您部署事件中樞的單一租用戶。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2a1785b9c749a8c413987974446190aafc08ed3a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105585"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

「事件中樞專用」容量可為需求最嚴苛的客戶提供單一租用戶部署。 在完整規模 Azure 事件中樞可以每秒輸入超過 2 百萬個事件，或使用完全耐久性儲存體遙測，每秒可高達 2 GB 且延遲不到一秒。 這也可在相同系統上，啟用能即時和批次處理的整合式解決方案。 透過供應項目中包含的[事件中樞擷取](event-hubs-capture-overview.md)，您可讓單一資料流同時支援即時和批次型管線，以降低解決方案的複雜度。

下表比較事件中樞可用的服務層。 相較於大部分標準功能的使用量定價，事件中樞專用供應項目的每月費用固定。 專用層提供標準方案的所有功能，但可對工作負載需求高的客戶提供企業規模容量。 

| 功能 | 標準 | 专用 |
| --- |:---:|:---:|
| 輸入事件 | 按百萬個事件付費 | 已含 |
| 輸送量單位 (每秒 1 MB 輸入，每秒 2 MB 輸出) | 按小時付費 | 已包括 |
| 訊息大小 | 1 MB | 1 MB |
| 發行者原則 | 是 | 是 |   
| 用戶群組 | 20 | 20 |
| 消息重播 | 是 | 是 |
| 最大輸送量單位 | 20 (可通融至 100)   | 1 容量單位 (CU) ≈ 50 |
| 代理連線 | 包含 1,000 個 | 包含 10 萬個 |
| 其他代理連線 | 是 | 是 |
| 訊息保留期 | 含 1 天 | 最多含 7 天 |
| 擷取 | 按小時付費 | 已包括 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>事件中樞專用容量的優點

使用事件中樞專用時，有以下的優點：

* 使用單一租用戶裝載，不會受到其他租用戶的干擾。
* 可重複每次效能。
* 容量保證符合您暴增的需求。
* 包含事件中樞的[擷取](event-hubs-capture-overview.md)功能，以提供與微批次和長期保留的整合。
* 零維護：此服務會管理負載平衡、作業系統更新、安全性修補程式及資料分割。
* 固定的每小時定價。
* 訊息保留最多 7 天，不額外收費。

事件中樞專用也會移除一些標準供應項目的輸送量限制。 標準層的輸送量單位可讓您每個 TU 的輸入為每秒 1000 個事件或每秒 1 MB，而輸出則為該數量的兩倍。 專用規模的供應項目的輸入和輸出事件計數沒有限制。 這些限制僅取決於所購買事件中樞的處理能力。

這個保留的專用環境提供了其他這一層獨有的功能，例如：

* 控制叢集中的命名空間數目。
* 指定每個命名空間的輸送量限制。
* 設定每個命名空間下事件中樞的數目。
* 決定分割區的數目限制。

這項服務以最大型遙測使用者為目標，同時提供給企業合約客戶。

## <a name="how-to-onboard"></a>如何加入

您可以根據需求新增或移除 CU 於當月上下調整容量。 專用方案的獨特之處在於可從事件中樞產品小組獲得更為實際的加入體驗，以最適合您的方式彈性部署。 若要開始使用此 SKU，請[連絡帳務支援](https://ms.portal.azure.com/#create/Microsoft.Support)或您的 Microsoft 專員。

## <a name="next-steps"></a>後續步驟

請連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。 您也可以造訪下列連結以深入了解事件中樞定價層︰

- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。 
