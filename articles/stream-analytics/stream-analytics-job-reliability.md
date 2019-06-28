---
title: 避免 Azure 串流分析作業中發生服務中斷
description: 本文說明可讓串流分析作業升級具有彈性的指引。
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 672706c97a423819dd26941e0b6e22affa9c2bb8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329845"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>在服務更新期間確保串流分析工作可靠性

完全受控服務的其中一個特性，就是能夠快速導入新的服務功能和改良功能。 因此，「串流分析」可以每週 (或更頻繁地) 進行服務更新部署。 不論做了多少測試，現有的執行中工作仍然可能有因引入錯誤而導致中斷的風險。 如果您正在執行任務關鍵作業，這些風險需要予以避免。 您可以降低此風險，由下列的 Azure **[配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 模型。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure 配對區域如何解決這個問題？

「串流分析」可確保以個別的批次更新在配對區域中的工作。 如此一來找出潛在問題並予以修復更新之間沒有足夠的時間間隔。

「印度中部是一個例外」  (其配對區域印度南部並沒有提供「串流分析」服務)，「串流分析」更新部署不會在一組配對區域同時發生。 **相同群組內**多個區域中的部署可能**同時**發生。

關於 **[可用性與配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** 的文章提供了有關配對區域的最新資訊。

建議您使用兩個配對區域中部署相同的工作。 您應該再[監視這些作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)以便發生意外時收到通知。 如果其中一種作業結束向上[失敗狀態](https://docs.microsoft.com/azure/stream-analytics/job-states)Stream Analytics 服務更新之後，您可以連絡客戶支援服務以協助您識別根本原因。 您也應該容錯移轉至狀況良好的工作輸出所有下游取用者。

## <a name="next-steps"></a>後續步驟

* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
