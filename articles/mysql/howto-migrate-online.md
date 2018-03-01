---
title: "以最短停機時間移轉至適用於 MySQL 的 Azure 資料庫"
description: "本文說明如何以最短停機時間完成將 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫，以及如何使用 Attunity Replicate for Microsoft Migrations 來設定初始負載和從來源資料庫至目標資料庫的持續資料同步作業。"
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e1be72d97570643cc8a7c6eb05d3d363e96357b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>以最短停機時間移轉至適用於 MySQL 的 Azure 資料庫
您可以使用 Attunity Replicate for Microsoft Migrations，將現有的 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫。 Attunity Replicate 是 Attunity 與 Microsoft 共同提供的方案。 隨 Azure 資料庫移轉服務提供，Microsoft 客戶無需支付額外費用。 

Attunity Replicate 可協助將資料庫移轉期間的停機時間縮至最短，並讓來源資料庫全程保持運作。

Attunity Replicate 是一種資料複寫工具，可將各種來源與目標之間的資料同步處理。 它會傳播結構描述建立指令碼和與每個資料庫資料表相關聯的資料。 Attunity Replicate 不會傳播任何其他成品 (例如 SP、觸發程序、函式等)，或將這類成品中裝載的 PL/SQL 程式碼轉換為 T-SQL。

> [!NOTE]
> 雖然 Attunity Replicate 可支援一系列廣泛的移轉案例，但會著重在支援來源/目標組的特定子集。

執行最短停機時間的移轉程序概觀包括：

* 使用 [MySQL Workbench](https://www.mysql.com/products/workbench/) 將 **MySQL 來源結構描述移轉**至適用於 MySQL 的 Azure 資料庫受控資料庫服務。

* 使用 Attunity Replicate for Microsoft Migrations 來**設定初始負載和從來源資料庫至目標資料庫的持續資料同步作業**。 如此一來，在您準備將應用程式切換到 Azure 上目標的 MySQL 資料庫而必須將來源資料庫設成唯讀的時間，便可縮至最短。

如需 Attunity Replicate for Microsoft Migrations 方案的詳細資訊，請參閱下列資源：
 - 移至 [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate) \(英文\) 網頁。
 - 下載 [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html)。
 - 移至 [Attunity Replicate 社群](https://aka.ms/attunity-community)以取得快速入門指南、教學課程及支援。
 - 如需使用 Attunity Replicate 以將 MySQL 資料庫移轉至適用於 MySQL 的 Azure 資料庫逐步指南，請參閱[資料庫移轉指南](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql) \(英文\)。