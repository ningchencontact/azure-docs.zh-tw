---
title: 資料庫移轉案例狀態 | Microsoft Docs
description: 了解「Azure 資料庫移轉服務」支援的移轉案例狀態。
services: database-migration
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: fea50b896c769587b21b8a4e2ce8c585d7aee8c4
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791557"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Azure 資料庫移轉服務支援的移轉案例狀態
「Azure 資料庫移轉服務」是設計成可同時支援離線 (單次) 和線上 (持續同步) 移轉的各種移轉案例 (來源/目標組)。 「Azure 資料庫移轉服務」所提供的案例涵蓋範圍會隨著時間擴展。 定期都會新增新的案例。 本文會指出「Azure 資料庫移轉服務」目前支援的移轉案例，以及狀態 (個人 [或有限] 預覽版、公開預覽版或正式推出版) 或每個案例。

## <a name="offline-versus-online-migrations"></a>離線移轉與線上移轉之比較
當您使用「Azure 資料庫移轉服務」將資料庫移轉至 Azure 時，可以執行「離線」或「線上」移轉。 若使用「離線」移轉，在移轉開始的同時，應用程式也會開始停機。 若使用「線上」移轉，則停機時間僅限於移轉完成時完全移轉到新環境所需的時間。 建議您測試離線移轉以判斷停機時間是否在可容忍的範圍內；如果無法容忍，則執行線上移轉。

## <a name="migration-scenario-status"></a>移轉案例狀態
「Azure 資料庫移轉服務」所支援的每個移轉案例狀態會隨時間改變。 通常，案例會先以「個人預覽版」發行，而若要利用功能，則需要客戶透過 [DMS 預覽網站](https://aka.ms/dms-preview)提交提名申請。 當「個人預覽版」完成時，案例狀態會變更為「公開預覽版」。 所有「Azure 資料庫移轉服務」使用者都可利用「公開預覽版」中提供的移轉案例。 不過，移轉案例可能並非在所有區域中都可供使用，且功能在最終發行之前可能還會經歷其他變更。 當移轉案例變成「正式推出版」(最終發行狀態) 時，即表示功能完整且可供所有「Azure 資料庫移轉服務」使用者存取。 

## <a name="migration-scenario-support"></a>移轉案例支援

下列表格顯示使用「Azure 資料庫移轉服務」時所支援的移轉案例。

> [!NOTE]
> 如果下方列為支援的案例並未出現在使用者介面中，請與[資料移轉小組](mailto:datamigrationteam@microsoft.com)聯絡以了解詳細資訊。

### <a name="offline-one-time-migration-support"></a>離線 (單次) 移轉支援
下表顯示適用於離線移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server | ✔ |
|   | Oracle  |   |
| **Azure Cosmos DB**  | MongoDB | ✔ |
| **適用於 MySQL 的 Azure DB**  | MySQL |  |
|   | RDS MySQL  |  |
| **適用於 PostgreSQL 的 Azure DB**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>線上 (持續同步) 移轉支援
下表顯示適用於線上移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Azure Cosmos DB**  | MongoDB  | ✔ |
| **適用於 MySQL 的 Azure DB**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **適用於 PostgreSQL 的 Azure DB**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>後續步驟
如需 Azure 資料庫移轉服務和區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。 
