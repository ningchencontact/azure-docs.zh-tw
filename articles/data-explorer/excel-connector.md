---
title: 使用適用于 Microsoft Excel 的 Azure 資料總管連接器將資料視覺化
description: 在本文中, 您將瞭解如何使用適用于 Azure 資料總管的 Excel connector。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173829"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>使用適用于 Excel 的 Azure 資料總管連接器將資料視覺化

Azure 資料總管提供兩個選項來連接到 Excel 中的資料: 使用原生連接器, 或從 Azure 資料總管匯入查詢。 本文說明如何在 Excel 中使用原生連接器, 並連接到 Azure 資料總管叢集, 以取得資料並加以視覺化。

Azure 資料總管 Excel native connector 提供將查詢結果匯出至 Excel 的功能。 您也可以加入 KQL 查詢做為 Excel 資料來源, 以供其他計算或視覺效果使用。

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>將 Kusto 查詢定義為 Excel 資料來源, 並將資料載入至 Excel

1. 開啟 **Microsoft Excel**。
1. 在 [**資料**] 索引標籤中, 選取 [從 azure**資料總管**  > **取得資料** > ]。

    ![從 Azure 資料總管取得資料](media/excel-connector/get-data-from-adx.png)

1. 在 [ **Azure 資料總管 (Kusto)** ] 視窗中, 完成下欄欄位, 然後選取 **[確定]** 。

    ![Azure 資料總管 (Kusto) 視窗](media/excel-connector/adx-connection-window.png)
    
    |欄位   |描述 |
    |---------|---------|
    |**該群**   |   叢集的名稱 (必要)      |    
    |**[資料庫備份]**     |    資料庫的名稱      |    
    |**資料表名稱或 Azure 資料總管查詢**    |     資料表或 Azure 資料總管查詢的名稱    | 
    
    **Advanced 選項:**

     |欄位   |描述 |
    |---------|---------|
    |**限制查詢結果記錄號碼**     |     限制載入至 excel 的記錄數目  |    
    |**限制查詢結果資料大小 (位元組)**    |    限制資料大小      |   
    |**停用結果集截斷**    |         |      
    |**其他 Set 語句 (以分號分隔)**    |    加入`set`要套用至資料來源的語句     |   

1.  在 [導覽**器**] 窗格中, 流覽至正確的資料表。 在 [資料表預覽] 窗格中, 選取 [**轉換資料**] 以對您的資料進行變更, 或選取 [**載入**] 將其載入至 Excel。

![資料表預覽視窗](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > 如果已指定**資料庫**和/或**資料表名稱或 Azure 資料總管查詢**, 將會自動開啟正確的資料表預覽窗格。 

## <a name="analyze-and-visualize-data-in-excel"></a>在 Excel 中分析資料並將其視覺化

一旦資料載入至 excel, 並可在您的 Excel 工作表中使用之後, 您就可以建立關聯性和視覺效果來分析、摘要和視覺化資料。 

1.  在 [**資料表設計**] 索引標籤中, 選取 [**使用樞紐分析表摘要**]。 在 [**建立樞紐分析表**] 視窗中, 選取相關的資料表, 然後選取 **[確定]** 。

    ![建立樞紐分析表](media/excel-connector/create-pivot-table.png)

1. 在 [**樞紐分析表欄位**] 窗格中, 選取相關的資料表資料行來建立摘要資料表。 在下列範例中, 會選取 [ **EventId** ] 和 [**狀態**]。
    
    ![選取樞紐分析表欄位](media/excel-connector/pivot-table-pick-fields.png)

1. 在 [**樞紐分析表分析**] 索引標籤中, 選取 [**樞紐分析圖**] 以根據資料表建立視覺效果。 

    ![樞紐分析表](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 在下列範例中, 請使用**事件識別碼**、 **StartTime**和執行專案來查看氣象事件的其他相關資訊。

    ![顯現資料](media/excel-connector/visualize-excel-data.png)

1. 建立完整的儀表板來監視您的資料。

