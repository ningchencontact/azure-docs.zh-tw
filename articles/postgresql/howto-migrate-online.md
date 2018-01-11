---
title: "最少停機時間移轉至 Azure 資料庫 PostgreSQL |Microsoft 文件"
description: "本文章說明如何執行最少停機時間移轉將 PostgreSQL 資料庫擷取至傾印檔案，還原 PostgreSQL 資料庫從 Azure 資料庫中的 pg_dump PostgreSQL，適用於建立封存檔案和設定初始載入和連續資料同步處理從來源資料庫到 Microsoft 移轉使用 Attunity 複寫目標資料庫。"
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>最少停機時間移轉 PostgreSQL Azure 資料庫
您可以移轉現有 PostgreSQL 資料庫至 Azure 資料庫的 PostgreSQL Attunity 複寫使用 Microsoft 移轉，從 Attunity 和 Microsoft Azure 的資料庫移轉服務以及提供共同贊助商，聯合供應項目Microsoft 客戶不需額外成本。 Attunity for Microsoft 移轉的複寫也可讓資料庫移轉中的最少停機時間和來源資料庫會繼續移轉程序期間可運作。

Attunity 複寫是一種資料複寫工具，可讓不同的來源和目標之間資料同步將傳播結構描述建立指令碼和每個資料庫資料表相關聯的資料。 Attunity 複寫並不會傳播任何其他成品 （例如 SP，觸發程序、 函式等) 或轉換，比方說，等成品，t-sql 中主控的 PL/SQL 程式碼。

> [!NOTE]
> 即使 Attunity 複寫支援一組廣泛的移轉案例，但是 Microsoft 移轉 Attunity Replicate 會著重於支援的特定子集來源/目標組。

包含執行最少停機時間的移轉程序的概觀：

1. **移轉 PostgreSQL 來源結構描述**到 Azure 資料庫使用 PostgreSQL 資料庫[pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html)的-n 參數，命令，然後使用[pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html)命令。

2. **設定初始載入和連續資料同步處理從來源資料庫到目標資料庫**透過 Attunity 複寫的 Microsoft 移轉。 如此一來，降到最低的來源資料庫必須設定為以唯讀狀態在準備切換到目標 PostgreSQL 資料庫應用程式在 Azure 上的時間。

如需有關 Microsoft 移轉供應項目的 Attunity 複寫的詳細資訊，請檢視下列資源：
 - 為 Microsoft 移轉 Attunity Replicate[網頁](https://aka.ms/attunity-replicate)。
 - [下載](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)Attunity for Microsoft 移轉的複寫。
 - Attunity Replicate[社群](https://microsoft.attunity.com/)使用快速入門指南、 教學課程和支援。
 - 如需使用 Attunity 從 PostgreSQL 移轉至 Azure 資料庫的 PostgreSQL 逐步指導，請參閱[資料庫移轉指南](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql)。