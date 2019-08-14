---
title: 適用於 MySQL 的 Azure 資料庫中的效能建議
description: 本文說明中的效能建議功能適用於 MySQL 的 Azure 資料庫
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 87942ae8132c89c502bd6e0f4c8d5b5c81a0a14c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950375"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中的效能建議

**適用範圍：** 適用於 MySQL 的 Azure 資料庫5。7

> [!IMPORTANT]
> 效能建議處於預覽狀態。

效能建議功能會分析您的資料庫, 以建立自訂的建議, 以改善效能。 為了產生建議, 分析會查看各種資料庫特性, 包括架構。 在您的伺服器上啟用[查詢存放區](concepts-query-store.md), 以充分利用效能建議功能。 如果效能架構已關閉, 開啟查詢存放區會啟用此功能所需的 performance_schema 和效能架構儀器子集。 在執行任何效能建議之後, 您應該測試效能以評估這些變更的影響。

## <a name="permissions"></a>Permissions

需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議

[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

在 MySQL 伺服器的 [Azure 入口網站] 頁面上, 從功能表列的 [**智慧效能**] 區段開啟 [**效能建議**]。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選取 [**分析**並選擇資料庫], 這將會開始進行分析。 根據您的工作負載, 分析可能需要幾分鐘的時間才能完成。 分析完成後，入口網站中會有通知。 分析會執行資料庫的深層檢查。 我們建議您在離峰期間執行分析。

[**建議**] 視窗會顯示一份建議清單 (如果找到的話), 以及產生這項建議的相關查詢識別碼。 使用查詢識別碼, 您可以使用[query_store](concepts-query-store.md#mysqlquery_store) view 來深入瞭解查詢。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不會自動套用建議。 若要套用建議, 請複製查詢文字, 並從您選擇的用戶端加以執行。 請記得測試和監視以評估建議。

## <a name="recommendation-types"></a>建議類型

目前只支援*建立索引*建議。

### <a name="create-index-recommendations"></a>建立索引建議

*建立索引*建議會建議新的索引, 以加速工作負載中最常執行或耗時的查詢。 此建議類型需要啟用[查詢存放區](concepts-query-store.md)。 查詢存放區會收集查詢資訊, 並提供詳細的查詢執行時間和頻率統計資料, 供分析用來提出建議。

## <a name="next-steps"></a>後續步驟
- 深入瞭解適用於 MySQL 的 Azure 資料庫中的[監視和微調](concepts-monitoring.md)。