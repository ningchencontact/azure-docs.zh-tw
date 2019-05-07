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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138668"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

Azure 事件中樞叢集可提供客戶最嚴苛的資料流處理需求的單一租用戶部署。 此單一租用戶供應項目具有保證的 99.99 %sla 的安心保障。 只有在定價層的專用上使用。

事件中樞叢集可以輸入數以百萬計的每秒容量保證與次秒延遲的事件。 命名空間和事件中樞建立專用的叢集內包含標準的供應項目及其他資源，但沒有任何輸入限制的所有功能。 它也包含[事件中樞擷取](event-hubs-capture-overview.md)功能不需要額外收費。 您可以使用它來自動批次和記錄資料串流至 Azure 儲存體或 Azure Data Lake。

專用的叢集進行佈建和計費的容量單位 (Cu)。 Cu 會預先配置的 CPU 和記憶體資源數量。 您可以購買 1、 2、 4、 8、 12、 16 或 20 的 Cu，每個叢集。 您多少可以擷取和資料流，每個 CU 取決於一些因素，例如生產者和取用者、 「 承載 」 圖形中，輸出速率的數目。

如需詳細資訊，請參閱基準測試結果的資料表。

## <a name="why-use-event-hubs-dedicated"></a>為何要使用事件中樞專用？

事件中樞專用的需要企業級容量的客戶提供下列三項優點。

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單一租用戶保證容量的較佳的效能

專用的叢集可保證在完整規模的容量。 它可以高達 gb 的資料流處理的資料，以因應暴增的流量完全持久的儲存體和次秒延遲的輸入。

#### <a name="inclusive-and-exclusive-access-to-features"></a>內含和專有功能的存取權 
專用的供應項目包含的功能，例如擷取不需要額外收費。 它也提供即將推出的功能，例如 BYOK 的獨佔存取權。 服務也會管理負載平衡、 作業系統更新、 安全性修補程式和資料分割。 使用這些功能，您可以在基礎結構維護和更多的時間，在建置用戶端的功能上花費較少的時間。

#### <a name="cost-savings"></a>節省成本
高輸入磁碟區，在叢集的成本遠少於每小時比您購買相當數量的輸送量單位 (Tu) 標準的供應項目。 高磁碟區 > 100 的 Tu。


## <a name="event-hubs-standard-vs-dedicated"></a>事件中樞 「 標準 vs。专用

下表比較事件中樞可用的服務層級。 事件中樞專用供應項目是依每月費用固定相較於大部分標準功能的使用量定價計費。 專用的層提供標準方案中，但有嚴苛的工作負載的客戶的企業規模容量的所有功能。

| 功能 | 標準 | 专用 |
| --- |:---:|:---:|
| 輸入事件 | 按百萬個事件付費 | 已含 |
| 輸送量單位 (每秒 1 MB 輸入，每秒 2 MB 輸出) | 按小時付費 | 已包括 |
| 訊息大小 | 1 MB | 1 MB |
| 分割數 | 每個命名空間的 40 | 每個 CU 2,000 |
| 用戶群組 | 每一個事件中樞的 20 | 事件中樞每 1,000 個 |
| 最大頻寬 | 20 Tu （最多 40 Tu) | 20 Cu |
| 代理連線 | 包含 1,000 個 | 包含的 100,000 |
| 訊息保留期 | 包含的一天 | 包含的最多七天 |
| 擷取 | 按小時付費 | 已包括 |

## <a name="what-can-i-achieve-with-a-cluster"></a>可以達到什麼與叢集？

事件中樞叢集，您可以在多少內嵌和串流處理取決於您生產者、 取用者，您擷取及處理的速率及更多。

下表顯示在測試期間所達成的基準測試結果。

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400,000 訊息數/秒 | 800 MB/秒 | 800,000 訊息數/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66,600 訊息數/秒 | 1.33 GB/秒 | 133,000 訊息數/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34,000 訊息數/秒 | 1.05 GB/秒 | 34,000 訊息數/秒 | 1,000 Tu | 250 個 TU |

在測試中，使用下列條件：

- 使用四個容量單位的專用層事件中樞叢集。
- 用於擷取的事件中樞具有 200 個分割區。
- 已擷取的資料已收到兩個接收者應用程式。 他們收到來自所有分割區的資料。

## <a name="use-event-hubs-dedicated"></a>使用事件中樞專用

若要使用事件中樞專用[連絡帳務支援](https://ms.portal.azure.com/#create/Microsoft.Support)或您的 Microsoft 代表。 您可以根據需求新增或移除 CU 於當月上下調整容量。 事件中樞產品小組可協助您取得的方式彈性部署最適合您。

## <a name="next-steps"></a>後續步驟

請連絡您的 Microsoft 業務代表或 Microsoft 支援服務以取得事件中樞專用容量的其他資訊。 若要深入了解事件中樞定價層，請使用下列連結：

- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡您的 Microsoft 業務代表或 Microsoft 支援服務以取得事件中樞專用容量的其他資訊。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
