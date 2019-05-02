---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 這篇文章會提供專用的 Azure 事件中樞，這可提供事件中樞的單一租用戶部署的概觀。
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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708004"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

*事件中樞叢集*提供客戶最嚴苛的資料流處理需求的單一租用戶部署。 此單一租用戶供應項目具有保證的 99.99 %sla，而且僅適用於我們的定價層的專用。 事件中樞叢集可輸入數百萬個事件每秒容量保證與秒的延遲。 命名空間和事件中樞建立專用的叢集內包含標準的供應項目及其他資源，但沒有任何輸入限制的所有功能。 它也包含熱門[事件中樞擷取](event-hubs-capture-overview.md)，完全免費，可讓您將會自動批次和記錄資料串流至 Azure 儲存體或 Azure Data Lake 的功能。 

專用的叢集進行佈建和依計費**容量單位 (Cu)**，預先配置的 CPU 和記憶體資源數量。 您可以購買 1、 2、 4、 8、 12、 16 或 20 個的 Cu，每個叢集。 您可以在多少內嵌和串流處理每個 CU 取決於各種因素，例如生產者和取用者，裝載圖形輸出數目評等 （請參閱下方的基準測試結果，如需詳細資訊）。 

## <a name="why-dedicated"></a>為什麼專用嗎？

專用的事件中樞提供三個吸引人的優點，需要企業級容量的客戶：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單一租用戶保證容量的較佳的效能

專用的叢集可保證在完整規模的容量，可以輸入最多 gb 的資料流處理的資料，以容納任何完全持久的儲存體與低於一秒延遲暴增的流量。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>內含和專有功能的存取權 
專用的供應項目包含功能，例如擷取任何額外的費用，以及即將推出的功能，例如 BYOK 的獨佔存取權。 服務也管理負載平衡、 作業系統更新、 安全性修補程式和客戶的資料分割，以便您可以花較少的時間在基礎結構維護和更多的時間，在建置用戶端的功能。  

#### <a name="cost-savings"></a>節省成本
高輸入磁碟區 (> 100 的 Tu)，叢集成本遠小於每小時購買相當數量的輸送量單位，標準的供應項目。


## <a name="event-hubs-standard-vs-dedicated"></a>事件中樞 「 標準 vs。专用

下表比較事件中樞可用的服務層級。 事件中樞專用供應項目是依每月固定價格，相較於大部分標準功能的使用量定價計費。 專用層提供標準方案的所有功能，但可對工作負載需求高的客戶提供企業規模容量。 

| 功能 | 標準 | 专用 |
| --- |:---:|:---:|
| 輸入事件 | 按百萬個事件付費 | 已含 |
| 輸送量單位 (每秒 1 MB 輸入，每秒 2 MB 輸出) | 按小時付費 | 已包括 |
| 訊息大小 | 1 MB | 1 MB |
| 分割數 | 每個命名空間的 40 | 每個 CU 2000 |
| 用戶群組 | 每一個事件中樞的 20 | 每一個事件中樞的 1000 |
| 最大 頻寬 | 20 Tu （最多 40 Tu)    | 20 Cu |
| 代理連線 | 包含 1,000 個 | 包含 10 萬個 |
| 訊息保留期 | 含 1 天 | 最多含 7 天 |
| 擷取 | 按小時付費 | 已包括 |

## <a name="what-can-i-achieve-with-a-cluster"></a>可以達到什麼與叢集？

事件中樞叢集，您可以在多少內嵌和串流處理取決於各種因素，例如您生產者、 取用者，您會擷取與處理、 速率及更多。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400k 則訊息/秒 | 800 MB/秒 | 800k 則訊息/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66.6k 則訊息/秒 | 1.33 GB/秒 | 133k 則訊息/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34k 則訊息/秒 | 1.05 GB/秒 | 34k 則訊息/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用四個容量單位 (Cu) 的專用層事件中樞叢集。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

## <a name="how-to-onboard"></a>如何加入

若要開始使用此 SKU，請[連絡帳務支援](https://ms.portal.azure.com/#create/Microsoft.Support)或您的 Microsoft 專員。 您可以根據需求新增或移除 CU 於當月上下調整容量。 專用方案的獨特之處在於可從事件中樞產品小組獲得更為實際的加入體驗，以最適合您的方式彈性部署。 

## <a name="next-steps"></a>後續步驟

請連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。 您也可以造訪下列連結以深入了解事件中樞定價層︰

- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
