---
title: 升級至最新一代 Azure SQL 資料倉儲 | Microsoft Docs
description: 將「Azure SQL 資料倉儲」升級至最新一代 Azure 硬體和儲存體架構的步驟。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>在 Azure 入口網站中升級至最新一代 Azure SQL 資料倉儲

使用 Azure 入口網站將「Azure SQL 資料倉儲」升級成使用最新一代 Azure 硬體和儲存體架構。 透過升級，您將可以利用更快的效能、更大的延展性，以及無限的資料行存放區索引儲存體。  

## <a name="applies-to"></a>適用於
此升級適用於「針對彈性最佳化」效能層級中的資料倉儲。  這些指示會將資料倉儲從「針對彈性最佳化」效能層級升級為「針對計算最佳化」效能層級。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="before-you-begin"></a>開始之前

1. 如果要升級的「針對彈性最佳化」資料倉儲已暫停，請[繼續執行資料倉儲](pause-and-resume-compute-portal.md)。
2. 請做好停機幾分鐘的準備。 
3. 升級程序會終止所有工作階段並中斷所有連線。 升級之前，請確定您的查詢已經完成。 如果您在交易進行中的情況下開始升級，復原時間可能會相當長。 

## <a name="start-the-upgrade"></a>開始升級

1. 在 Azure 入口網站中開啟您的資料倉儲，然後按一下 [升級為針對計算最佳化]。
2. 請注意 [針對計算最佳化] 效能層級選項。 預設選項相當於升級前的目前層級。
3. 選擇一個效能層級。 「針對彈性最佳化」效能層級的價格目前在預覽版期間為半價。
4. 按一下 [升級]。
5. 在 Azure 入口網站中檢查狀態。
6. 等候資料倉儲變更為 [線上]。

## <a name="rebuild-columnstore-indexes"></a>重建資料行存放區索引

在您的資料倉儲上線之後，您即可載入資料及執行查詢。 不過，一開始效能可能很差，因為背景程序正在將資料移轉至新硬體。 

若要強制儘快移轉資料，建議您重建資料行存放區索引。 若要這麼做，請參閱[重建資料行存放區索引以改善區段品質](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)的指引。 

## <a name="next-steps"></a>後續步驟
您的資料倉儲已上線。 若要使用新的效能功能，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。
 