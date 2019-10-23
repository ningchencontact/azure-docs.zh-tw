---
title: 如何在您的 Azure 時間序列深入解析環境中設定保留期 | Microsoft Docs
description: 本文說明如何在您的 Azure 時間序列深入解析環境中設定保留期。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: bf6788bac7003bec0ccfc171d622cd9b28bf43e0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330892"
---
# <a name="configuring-retention-in-time-series-insights"></a>在時間序列深入解析中設定保留期

本文說明如何在 Azure 時間序列深入解析中，設定 [資料保留時間] 和 [超過儲存空間限制的行為]。

## <a name="summary"></a>總結

每個 Azure 時間序列深入解析環境都有設定**資料保留時間**的設定。 可設定的值為 1 到 400 天。 資料會依環境儲存容量或保留持續時間 (1-400) 進行刪除，以先達到者為準。

每個時間序列深入解析環境都有額外的「**儲存限制超過**」的設定行為。 此設定可控制當達到環境的容量上限時，要執行的輸入與清除行為。 有兩個行為可供選擇：

- **清除舊資料** (預設)
- **暫停輸入**

如需詳細資訊以進一步了解這些設定，請檢閱[了解時間序列深入解析中的保留期](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>設定資料保留期

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 找出您的現有時間序列深入解析環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]。 選取 Time Series Insights 環境。

1. 在 [設定] 標題之下，選取 [設定]。

    [![Select 設定，然後設定](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. 選取 [**資料保留時間（以天**為單位）]，以使用滑杆列設定保留期，或在文字方塊中輸入數位。

1. 請注意 [容量] 設定，因為此設定會影響資料事件數量上限和用於儲存資料的總儲存體容量。

1. 切換 [超過儲存空間限制的行為] 設定。 選取 [清除舊資料] 或 [暫停輸入] 行為。

    [![Data 保留-接受並儲存。](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. 接受核取方塊，表示您已複習檔，並瞭解資料遺失的潛在風險。 選取 [儲存] 以設定變更。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請檢閱[了解時間序列深入解析中的保留期](time-series-insights-concepts-retention.md)。

- 瞭解[如何調整您的時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。

- 瞭解如何[規劃您的環境](time-series-insights-environment-planning.md)。