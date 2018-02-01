---
title: "在 Azure Log Analytics 中建立檢視以分析資料 | Microsoft Docs"
description: "Log Analytics 中的檢視表設計工具可讓您建立要在 Azure 入口網站中顯示的自訂檢視，以及包含 Log Analytics 工作區中不同資料的視覺效果。 本文包含檢視設計工具的概觀以及建立和編輯自訂檢視的程序。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>在 Log Analytics 中使用檢視表設計工具來建立自訂檢視
[Log Analytics](log-analytics-overview.md) 中的檢視表設計工具可讓您在 Azure 入口網站中建立自訂檢視，其中包含 Log Analytics 工作區中不同資料的視覺效果。 本文包含檢視設計工具的概觀以及建立和編輯自訂檢視的程序。

其他與檢視設計工具相關的文章︰

* [圖格參考](log-analytics-view-designer-tiles.md) - 可用於自訂檢視之每個圖格的設定參考。
* [視覺效果組件參考](log-analytics-view-designer-parts.md) - 可用於自訂檢視之每個圖格的設定參考。

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則所有檢視中的查詢必須以[新的查詢語言](https://go.microsoft.com/fwlink/?linkid=856078)撰寫。  在工作區升級之前建立的任何檢視都會自動轉換。

## <a name="concepts"></a>概念
檢視會顯示在 Azure 入口網站中 Log Analytics 工作區的 [概觀] 分頁上。  每個自訂檢視的圖格會依字母順序顯示，同時顯示相同工作區安裝之解決方案的圖格。

![概觀分頁](media/log-analytics-view-designer/overview-page.png)

以檢視設計工具建立的檢視包含下表中的各部分。

| 部分 | 說明 |
|:--- |:--- |
| 圖格 |顯示在您的 Log Analytics 工作區 [概觀] 分頁上。  是自訂檢視包含之資訊的視覺化摘要。  不同的圖格類型提供不同記錄的視覺效果。  按一下圖格即可開啟其自訂檢視。 |
| 自訂檢視 |會在使用者按一下圖格時顯示。  包含一或多個視覺效果組件。 |
| 視覺效果組件 |根據一或多個針對 Log Analytics 工作區中資料進行[記錄搜尋](log-analytics-log-searches.md)，所得結果的視覺效果。  大部分組件包含標頭 (提供高階的視覺效果) 和前幾名搜尋結果清單。  不同的組件類型提供 Log Analytics 工作區中不同記錄的視覺效果。  按一下組件中的項目可執行記錄檔搜尋以提供詳細的記錄。 |


## <a name="work-with-an-existing-view"></a>使用現有的檢視
當您開啟以檢視表設計工具建立的檢視時，它會具有功能表，其中具備下表中的選項。

![概觀功能表](media/log-analytics-view-designer/overview-menu.png)


| 選項 | 說明 |
|:--|:--|
| 重新整理   | 使用最新資料重新整理檢視。 | 
| 分析 | 開啟[進階分析入口網站](log-analytics-log-search-portals.md#advanced-analytics-portal)以使用記錄搜尋分析資料。(log-analytics-log-search-portals.md#advanced-analytics-portal)。 |
| Filter    | 為檢視中包含的資料設定時間篩選條件。 |
| Edit      | 在檢視表設計工具中開啟檢視以編輯其內容和設定。   |
| 複製     | 建立新的檢視並且在檢視表設計工具中開啟。  新檢視會有與原始檢視相同的名稱再加上「複製」二字。 |


## <a name="create-a-new-view"></a>建立新的檢視
在**檢視表設計工具**中建立新的檢視，方法是按一下 Azure 入口網站中 Log Analytics 工作區 [概觀] 分頁上的 [檢視表設計工具] 圖格。

![檢視設計工具圖格](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>使用檢視設計工具
無論您是建立新的檢視或編輯現有檢視，都會使用檢視表設計工具。  

檢視設計工具有三個窗格。  [設計] 窗格包含您要建立或編輯的自訂檢視。  從 [控制] 窗格將圖格和組件新增至 [設計] 窗格。  [屬性] 窗格會列出圖格或選取組件的屬性。

![[檢視設計工具]](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>設定檢視圖格
自訂檢視可以只有單一圖格。  選取 [控制] 窗格中的 [圖格] 索引標籤來檢視目前圖格或選取另一個圖格。  [屬性] 窗格會列出目前圖格的屬性。  根據 [圖格參考](log-analytics-view-designer-tiles.md) 中的詳細資訊來設定圖格屬性，然後按一下 [套用] 儲存變更。

### <a name="configure-visualization-parts"></a>設定視覺效果組件
檢視可以包含任意數目的視覺效果組件。  選取 [檢視] 索引標籤，然後要新增至檢視的視覺效果組件。  [屬性] 窗格會列出選取組件的屬性。  根據 [視覺效果組件參考](log-analytics-view-designer-parts.md) 中的詳細資訊來設定檢視的屬性，然後按一下 [套用] 儲存變更。

檢視只有一列視覺效果組件。  按一下現有組件並拖曳至新的位置，即可重新排列檢視中的組件。

按一下組件右上角的 [X] 按鈕，即可從檢視中移除視覺效果組件。


### <a name="menu-options"></a>功能表選項
當您在編輯模式中使用檢視時，您會有下表中的功能表選項。

![編輯功能表](media/log-analytics-view-designer/edit-menu.png)

| 選項 | 說明 |
|:--|:--|
| 儲存        | 儲存變更並關閉檢視。 |
| 取消      | 捨棄變更並關閉檢視。 |
| 刪除檢視 | 刪除檢視。 |
| 匯出      | 將檢視匯出至 [Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)，您可以將該範本匯入至其他工作區。  檔案的名稱是檢視的名稱，副檔名為 omsview。 |
| Import      | 將您從其他工作區匯出的 omsview 檔案匯入。  這樣會覆寫現有檢視的設定。 |
| 複製       | 建立新的檢視並且在檢視表設計工具中開啟。  新檢視會有與原始檢視相同的名稱再加上「複製」二字。 |
| 發佈     | 將檢視匯出至 JSON 檔案，該檔案可以插入至[管理解決方案](../operations-management-suite/operations-management-suite-solutions-resources-views.md)。  檔案的名稱是檢視的名稱，副檔名為 json。 建立的第二個檔案之副檔名為 resjson，其中包含在 JSON 檔案中定義之資源的值。

## <a name="next-steps"></a>後續步驟
* 將[圖格](log-analytics-view-designer-tiles.md)新增至您的自訂檢視。
* 將[視覺效果組件](log-analytics-view-designer-parts.md)新增至您的自訂檢視。
