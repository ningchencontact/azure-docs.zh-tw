---
title: Azure SQL 資料倉儲 Gen2 支援較低的計算層級 | Microsoft Docs
description: Azure SQL 資料倉儲 Gen2 現在支援較低的計算層級
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 7ec84797486b662261fd5998bebdfa34c9dff5da
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466297"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Azure SQL 資料倉儲 Gen2 支援較低的計算層級

Microsoft 正在協助降低入門成本，藉由為速度飛快的 Azure SQL 資料倉儲 gen2 新增較低的計算層級，讓您能夠執行可處理嚴苛查詢的資料倉儲。 客戶可從 100 cDWU (資料倉儲單位) 開始，在數分鐘內調整為 30,000 cDWU，體驗到 Azure SQL 資料倉儲卓越的效能、彈性及安全性功能。 客戶可以透過較低的計算層級，來享受到 Gen2 的效能和彈性。 

Microsoft 透過卸除下一代資料倉儲進入點，為想要評估一個安全、高效能資料倉儲所有益處的價值導向客戶打開一扇大門，讓他們無須猜測哪一個試用環境最適合他們。  客戶將可以從最低 100 cDWU 開始，這比目前 500 cDWU 的進入點更低。  SQL 資料倉儲 Gen2 會繼續支援暫停和繼續作業，超出只是計算的彈性。  Gen2 也支援無限制資料行存放區儲存體容量，以及每個查詢 2.5 倍以上的記憶體、最多 128 個並行查詢，和調適性快取功能；相較於 Gen1 上同樣價格的同樣資料倉儲單位，Gen2 可帶來效能平均提升 5 倍以上的體驗。  異地備援備份是內建保證資料保護之 Gen2 的標準功能。 Azure SQL 資料倉儲 Gen2 已準備好為您調整。

入口網站目前不支援部署或調整為較低層級的 Gen2 執行個體。 我們正努力實現這項功能，在過渡時期，如果您想要利用這個新的層級，請[提交票證](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>開始使用 Azure SQL 資料倉儲 Gen2 

客戶可以選擇部署新的 Gen2 執行個體，也可以升級現有的 Gen1 資料倉儲執行個體，以便體驗新一代資料倉儲的彈性與效能。 

請試用 [Azure SQL 資料倉儲計算最佳化 Gen2 層級。](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
將 [Azure SQL 資料倉儲計算最佳化 Gen1 升級至 Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)。若要了解 Azure SQL 資料倉儲 Gen2 的運作方式，請觀看 [Microsoft Mechanics 影片。](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>支援較低計算層級的區域

- 美國東部 1 
- 美國東部 2
- 西歐
- 北歐
- 美國西部 2
- 東南亞
- 美國中南部
- 美國中部 
- 東亞
- 日本東部
- 印度中部
- 澳洲東部
- 加拿大中部
- 日本西部 
- 加拿大中部

## <a name="next-steps"></a>後續步驟

- [深入了解](upgrade-to-latest-generation.md)如何升級 SQL 資料倉儲以計算最佳化效能。 
