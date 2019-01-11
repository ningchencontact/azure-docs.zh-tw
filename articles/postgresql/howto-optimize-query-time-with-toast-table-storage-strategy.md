---
title: 使用快顯通知資料表儲存策略在適用於 PostgreSQL 的 Azure 資料庫伺服器中最佳化查詢時間
description: 本文說明如何使用快顯通知資料表儲存策略，在「適用於 PostgreSQL 的 Azure 資料庫」伺服器中最佳化查詢時間。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 1fb818a65e26f969f72131b0f5265f3efdd36bb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542193"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>使用快顯通知資料表儲存策略最佳化查詢時間 
本文說明如何使用快顯通知資料表儲存策略最佳化查詢時間。

## <a name="toast-table-storage-strategies"></a>快顯通知資料表儲存策略
有四個不同的策略可用來將可快顯通知的資料行儲存在磁碟上，它們分別代表壓縮和非正規儲存之間的各種組合。 此策略可設定於資料類型層級和資料行層級上。
- **一般**可防止壓縮或非正規儲存；此外也會停用 varlena 類型的單一位元組標頭。 **一般**是不可快顯通知資料類型的資料行唯一可用的策略。
- **擴充**可允許壓縮和非正規儲存。 **擴充**是多數可快顯通知資料類型的預設值。 首先會嘗試進行壓縮，如果資料列仍然太大，再嘗試進行非正規儲存。
- **外部**可允許非正規儲存，但不允許壓縮。 使用**外部**會讓寬文字和 bytea 資料行的子字串作業更為快速，但也會佔用較多儲存空間，因為這些作業已最佳化，而在非正規值未壓縮時只會擷取其必要的部分。
- **主要**可允許壓縮，但不允許非正規儲存。 對於這類資料行仍會執行非正規儲存，但這是在沒有任何其他方法可縮小資料列使其容納於頁面內時，才會採用的最後手段。

## <a name="using-toast-table-storage-strategies"></a>使用快顯通知資料表儲存策略
如果您的查詢會存取可快顯通知的資料類型，請考慮使用**主要**選項 (而非預設的**擴充**選項)，以縮短查詢時間。 **主要**不會防止非正規儲存。 反之，如果您的查詢不會存取可快顯通知的資料類型，使用預設的**擴充**選項可能會有所助益。 如此，主要資料表中較大的資料列部分將可容納於共用的緩衝區快取中，而有助於提升效能。

如果您有工作負載使用具有寬型資料表和高字元計數的結構描述，請考慮使用 PostgreSQL 快顯通知資料表。 範例客戶資料表有超過 350 個資料行，其中有數個資料行超過 255 個字元。 在轉換**主要**的快顯通知策略之後，其基準測試查詢時間從 4203 秒降低為 467 秒，改善了 89%。

## <a name="next-steps"></a>後續步驟
檢閱您的工作負載是否有前述特性。 

檢閱下列 PostgreSQL 文件：[Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) 