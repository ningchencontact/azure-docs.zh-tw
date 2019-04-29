---
title: Azure 監視器檢視中的篩選 |Microsoft Docs
description: Azure 監視器檢視中的篩選器可讓使用者依特定屬性的值篩選檢視中的資料，而不需修改檢視本身。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551689"
---
# <a name="filters-in-azure-monitor-views"></a>Azure 監視器檢視中的篩選
A**篩選條件**中[Azure 監視器檢視](view-designer.md)可讓使用者依特定屬性的值篩選檢視中的資料，而不需修改檢視本身。  例如，您可以允許檢視的使用者篩選檢視，只顯示來自某個特定電腦或一組電腦的資料。  您可以在單一檢視上建立多個篩選，以允許使用者依多個屬性進行篩選。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。

## <a name="using-a-filter"></a>使用篩選
按一下頂端的檢視，以開啟下拉式清單，您可以變更檢視的日期時間範圍的日期時間範圍。

![篩選範例](media/view-designer-filters/filters-example-time.png)

按一下 **+** 以使用為檢視定義的自訂篩選器來新增篩選條件。 從下拉式清單中選取篩選條件的值或輸入一個值。 按一下 **+**，繼續新增篩選條件。 


![篩選範例](media/view-designer-filters/filters-example-custom.png)

如果您移除篩選的所有值，系統就不會再套用該篩選。


## <a name="creating-a-filter"></a>建立篩選

請在[編輯檢視](view-designer.md)時，從 [篩選] 索引標籤建立篩選。  篩選適用於檢視全域，因此會套用至檢視的所有部分。  

![篩選設定](media/view-designer-filters/filters-settings.png)

下表說明篩選的設定。

| 設定 | 描述 |
|:---|:---|
| 欄位名稱 | 用於篩選的欄位名稱。  此欄位必須符合中的 summarize 欄位**查詢值**。 |
| 查詢值 | 所要執行以填入使用者篩選下拉式清單的查詢。  這項查詢都必須使用[摘述](/azure/kusto/query/summarizeoperator)或是[相異](/azure/kusto/query/distinctoperator)提供唯一值，讓特定的欄位，而且必須符合**欄位名稱**。  您可以使用 [sort](/azure/kusto/query/sortoperator) 來排序對使用者顯示的值。 |
| Tag | 支援篩選之查詢中使用的欄位名稱，此名稱也會對使用者顯示。 |

### <a name="examples"></a>範例

下表包含一些常見的篩選範例。  

| 欄位名稱 | 查詢值 | Tag |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | 電腦 |
| EventLevelName | Event &#124; distinct EventLevelName | 严重性 |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | 严重性 |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>修改檢視查詢

若要讓篩選產生任何效果，您就必須將檢視中的任何查詢修改成依據選取的值進行篩選。  如果您未修改任何查詢檢視中的，任何使用者選取的值將會有任何作用。

在查詢中使用篩選值的語法如下： 

    where ${filter name}  

例如，如果您的檢視具有查詢所傳回的事件，並使用名為篩選_電腦_，您可以使用下列查詢。

    Event | where ${Computers} | summarize count() by EventLevelName

如果您新增了名為 Severity 的另一個篩選，則可以使用下列查詢來使用這兩個篩選。

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>後續步驟
* 深入了解您可以新增到自訂檢視中的[視覺效果組件](view-designer-parts.md)。
