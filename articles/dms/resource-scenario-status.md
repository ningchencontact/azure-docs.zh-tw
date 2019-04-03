---
title: 資料庫移轉案例狀態 | Microsoft Docs
description: 深入了解支援的 Azure 資料庫移轉服務的移轉案例的狀態。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7017566092530dce2359d45314ac00dca63b8ad0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886228"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>支援的 Azure 資料庫移轉服務的移轉案例的狀態
Azure 資料庫移轉服務的設計可同時支援不同的移轉案例 （來源/目標配對） 時，也將離線 （單次） 和線上 （持續同步處理） 的移轉作業。 「Azure 資料庫移轉服務」所提供的案例涵蓋範圍會隨著時間擴展。 定期都會新增新的案例。 這篇文章會識別目前支援的 Azure 資料庫移轉服務和狀態 （私人預覽，公開預覽狀態，或已正式推出），每個案例的移轉案例。

## <a name="offline-versus-online-migrations"></a>離線移轉與線上移轉之比較
您可以使用 Azure 資料庫移轉服務，來進行離線或線上移轉。 若使用「離線」移轉，在移轉開始的同時，應用程式也會開始停機。 若要限制停機時間移轉完成時需要移交到新的環境所需的時間，請使用*線上*移轉。 若要測試以判斷是否可接受; 停機離線移轉建議否則，請執行線上移轉。

## <a name="migration-scenario-status"></a>移轉案例狀態
支援的 Azure 資料庫移轉服務的移轉案例的狀態會隨時間變動。 通常，案例首次發行中**私人預覽**。 參與私人預覽需要客戶提交透過提名[DMS 預覽網站](https://aka.ms/dms-preview)。 在私人預覽之後, 案例狀態會變更為**公開預覽**。 Azure 資料庫移轉服務的使用者可以嘗試直接從使用者介面的公開預覽中的移轉案例。 無法註冊是必要的。  不過，在公開預覽中的移轉案例可能無法在所有區域中，而且可能會發生在最後發行版本之前的其他變更。 公開預覽之後，案例狀態會變更為**正式**。 正式推出 (GA) 是最終版本狀態，以及功能完整且可供所有使用者存取。 

## <a name="migration-scenario-support"></a>移轉案例支援
下表顯示使用 Azure 資料庫移轉服務時所支援的移轉案例。

> [!NOTE]
> 如果下方列為支援的案例並未出現在使用者介面中，請與[資料移轉小組](mailto:datamigrationteam@microsoft.com)聯絡以了解詳細資訊。

> [!IMPORTANT]
> 若要檢視目前處於私人預覽的 Azure 資料庫移轉服務所支援的案例，請參閱[DMS 預覽網站](https://aka.ms/dms-preview)。

### <a name="offline-one-time-migration-support"></a>離線 (單次) 移轉支援
下表顯示 Azure 資料庫移轉服務的支援，適用於離線移轉。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | 公開預覽 |
| **適用於 MySQL 的 Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **適用於 PostgreSQL 的 Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>線上 (持續同步) 移轉支援
下表顯示 Azure 資料庫移轉服務支援，公開預覽版或正式運作，以提供線上移轉。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 私人預覽 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | 公開預覽 |
| **適用於 MySQL 的 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **適用於 PostgreSQL 的 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 私人預覽 |

## <a name="next-steps"></a>後續步驟
如需的 Azure 資料庫移轉服務和區域可用性的概觀，請參閱文章[什麼是 Azure 資料庫移轉服務](dms-overview.md)。
