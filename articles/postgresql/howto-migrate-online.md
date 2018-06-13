---
title: 以最短停機時間移轉至適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明如何將 PostgreSQL 資料庫擷取至傾印檔案，從使用適用於 PostgreSQL 的 Azure 資料庫透過 pg_dump 所建立的封存檔案來還原 PostgreSQL 資料庫，以最短停機時間完成移轉，以及使用 Attunity Replicate for Microsoft Migrations 來設定初始負載和從來源資料庫至目標資料庫的持續資料同步作業。
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692084"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>以最短停機時間移轉至適用於 PostgreSQL 的 Azure 資料庫
您可以使用 Attunity Replicate for Microsoft Migrations，將現有的 PostgreSQL 資料庫移轉至適用於 PostgreSQL 的 Azure 資料庫。 Attunity Replicate 是 Attunity 與 Microsoft 共同提供的方案。 隨 Azure 資料庫移轉服務提供，Microsoft 客戶無需支付額外費用。 

Attunity Replicate 可協助將資料庫移轉期間的停機時間縮至最短，並讓來源資料庫全程保持運作。

Attunity Replicate 是一種資料複寫工具，可將各種來源與目標之間的資料同步處理。 它會傳播結構描述建立指令碼和與每個資料庫資料表相關聯的資料。 Attunity Replicate 不會傳播任何其他成品 (例如 SP、觸發程序、函式等)，或將這類成品中裝載的 PL/SQL 程式碼轉換為 T-SQL。

> [!NOTE]
> 雖然 Attunity Replicate 可支援一系列廣泛的移轉案例，但會著重在支援來源/目標組的特定子集。

執行最短停機時間的移轉程序概觀包括：

* 使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 命令搭配 -n 參數，然後使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 命令，將 **PostgreSQL 來源結構描述移轉**至適用於 PostgreSQL 資料庫的 Azure 資料庫。

* 使用 Attunity Replicate for Microsoft Migrations 來**設定初始負載和從來源資料庫至目標資料庫的持續資料同步作業**。 如此一來，在您準備將應用程式切換到 Azure 上目標的 PostgreSQL 資料庫而必須將來源資料庫設成唯讀的時間，便可縮至最短。

如需 Attunity Replicate for Microsoft Migrations 方案的詳細資訊，請參閱下列資源：
 - 移至 [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) \(英文\) 網頁。
 - 下載 [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)。
 - 移至 [Attunity Replicate 社群](https://aka.ms/attunity-community/)以取得快速入門指南、教學課程及支援。
 - 如需使用 Attunity Replicate 以從 PostgreSQL 移轉至適用於 PostgreSQL 的 Azure 資料庫逐步指南，請參閱[資料庫移轉指南](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql)。