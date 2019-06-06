---
title: Azure Data Lake Analytics 的災害復原指引
description: 了解如何規劃災害復原，為您的 Azure Data Lake Analytics 帳戶。
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498885"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics 的災害復原指引

Azure Data Lake Analytics 是隨選分析作業服務，可簡化巨量資料。 無需部署、設定及調整硬體，您只需寫入查詢便可轉換資料並擷取寶貴的深入見解。 透過針對所需的功能設定級別，此項分析服務便可立即處理任何規模的工作。 只有在作業進行時您才需要支付費用，十分符合成本效益。 本文指導如何保護您的作業，從罕見的全區停電或意外刪除。

## <a name="disaster-recovery-guidance"></a>災害復原指引

在使用 Azure Data Lake Analytics 時，務必為您準備您自己的災害復原計劃。 這篇文章可協助引導您建置災害復原計劃。 有其他可協助您建立自己的計劃的資源：
- [Azure 應用程式的失敗及災害復原](/azure/architecture/reliability/disaster-recovery)
- [Azure 復原技術指導](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>最佳做法和案例指南

ADLA 帳戶的讀取和寫入 U-SQL 資料表，以及非結構化的資料的區域中，您可以執行週期性的 U-SQL 作業。  準備災害進行下列步驟：

1. 在中斷期間，會使用次要區域中建立 ADLA 和 ADLS 帳戶。

   > [!NOTE]
   > 由於帳戶名稱的全域唯一的使用一致的命名配置，指出哪一個帳戶為次要。

2. 對於非結構化資料，請參考[Azure Data Lake 儲存體 Gen1 中資料的災害復原指引](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 儲存在 ADLA 資料表和資料庫的結構化資料，建立複本的中繼資料成品，例如資料庫、 資料表、 資料表值函式和組件。 您需要在生產環境中發生的變更時，會定期重新同步這些成品。 比方說，新插入的資料已複寫到次要區域複製資料，並插入第二個資料表。

   > [!NOTE]
   > 這些物件名稱的範圍設定為次要帳戶，並不是全域唯一，所以他們可以有相同的名稱，如同主要實際執行帳戶。

在中斷期間，您需要更新您的指令碼，以便輸入的路徑指向次要端點。 然後使用者會提交到 ADLA 帳戶次要區域中工作。 作業的輸出會是寫入的 ADLA 和 ADLS 帳戶次要區域中。

## <a name="next-steps"></a>後續步驟

[Azure Data Lake 儲存體 Gen1 中資料的災害復原指引](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
