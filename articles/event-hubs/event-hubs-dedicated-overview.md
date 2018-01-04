---
title: "Azure 事件中樞專用容量概觀 | Microsoft Docs"
description: "Microsoft Azure 事件中樞專用容量概觀。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 563152a019464f3d0342383ff13e6ee1c87a22fe
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

「事件中樞專用」容量可為需求最嚴苛的客戶提供單一租用戶部署。 在完整規模 Azure 事件中樞可以每秒輸入超過 2 百萬個事件，或使用完全耐久性儲存體遙測，每秒可高達 2 GB 且延遲不到一秒。 這也可在相同系統上，啟用能即時和批次處理的整合式解決方案。 透過供應項目中包含的[事件中樞擷取](event-hubs-capture-overview.md)，您可讓單一資料流同時支援即時和批次型管線，以降低解決方案的複雜度。

下表比較事件中樞可用的服務層。 相較於大部分標準功能的使用量定價，事件中樞專用供應項目的每月費用固定。 專用的層提供的標準方案，但嚴苛的工作負載的客戶企業規模容量的所有功能。 

| 功能 | 標準 | 專用 |
| --- |:---:|:---:|:---:|
| 輸入事件 | 按百萬個事件付費 | 已包括 |
| 輸送量單位 (每秒 1 MB 輸入，每秒 2 MB 輸出) | 按小時付費 | 已包括 |
| 訊息大小 | 256 KB | 1 MB |
| 發行者原則 | 是 | 是 |   
| 用戶群組 | 20 | 20 |
| 訊息重播 | 是 | 是 |
| 最大輸送量單位 | 20 (可通融至 100)   | 1 個容量單位 (CU) ≈ 50 |
| 代理連線 | 包含 1,000 個 | 包含 10 萬個 |
| 其他代理連線 | 是 | 是 |
| 訊息保留期 | 含 1 天 | 最多含 7 天 |
| 擷取 | 按小時付費 | 已包括 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>事件中樞專用容量的優點

使用事件中樞專用時，有以下的優點：

* 使用單一租用戶裝載，不會受到其他租用戶的干擾。
* 相較於標準的 256 KB，訊息大小增加至 1 MB。
* 可重複每次效能。
* 容量保證符合您暴增的需求。
* 包含[擷取](event-hubs-capture-overview.md)事件中心，以提供微批次和長期的保留與整合的功能。
* 零維護： 此服務會管理負載平衡、 作業系統更新、 安全性修補程式和資料分割。
* 固定的每小時定價。
* 設定訊息保留 7 天不額外收費。

事件中樞專用也會移除一些標準供應項目的輸送量限制。 標準層的輸送量單位可讓您每個 TU 的輸入為每秒 1000 個事件或每秒 1 MB，而輸出則為該數量的兩倍。 專用規模的供應項目的輸入和輸出事件計數沒有限制。 這些限制僅取決於所購買事件中樞的處理能力。

這個保留的專用環境提供了其他這一層獨有的功能，例如：

* 控制在叢集中的命名空間數目。
* 指定每個命名空間的輸送量限制。
* 設定每個命名空間之下的事件中心的數目。
* 決定資料分割數目的上限。

此服務為目標的最大的遙測使用者，而且可用於具有 enterprise 合約的客戶。

## <a name="how-to-onboard"></a>如何加入

您可以根據需求新增或移除 CU 於當月上下調整容量。 專用方案的獨特之處在於可從事件中樞產品小組獲得更為實際的加入體驗，以最適合您的方式彈性部署。 若要此 SKU，登入[連絡帳務支援](https://ms.portal.azure.com/#create/Microsoft.Support)或您的 Microsoft 代表再行審閱。

## <a name="next-steps"></a>後續步驟

請連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。 您也深入了解事件中心定價層，瀏覽下列連結：

- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。 
