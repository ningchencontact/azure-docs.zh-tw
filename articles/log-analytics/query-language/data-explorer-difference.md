---
title: Azure 監視器的 Log Analytics 語言參考 | Microsoft Docs
description: Log Analytics 所使用的資料總管查詢語言參考資訊。 包含 Log Analytics 專用的其他元素和 Log Analytics 查詢中不支援的元素。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 65f3388074ff7a84c6e0516a64665019d5883ab1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913167"
---
# <a name="log-analytics-query-language-differences"></a>Log Analytics 查詢語言差異

雖然 [Log Analytics](../log-analytics-queries.md) 建置於 [Azure 資料總管](/azure//data-explorer)上並使用[相同的查詢語言](/azure/kusto/query)，但該語言的版本確實存在一些差異。 這篇文章會識別用於資料總管的語言版本，與用於 Log Analytics 查詢的版本之間不同的元素。

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Log Analytics 中不支援的資料檔案總管元素
下列各節描述 Log Analytics 不支援的 Data Explorer 查詢語言的元素。

### <a name="statements-not-supported-in-log-analytics"></a>Log Analytics 中不支援的陳述式

* [別名](/kusto/query/aliasstatement)
* [查詢參數](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Log Analytics 中不支援的函式

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Log Analytics 中不支援的運算子

* [跨叢集聯結](/azure/kusto/query/joincrosscluster)
* [externaldata 運算子](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Log Analytics 中不支援的外掛程式

* [sql_request 外掛程式](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Log Analytics 中的其他運算子
下列運算子支援特定 Log Analytics 功能，且無法在 Log Analytics 以外的地方使用。

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>後續步驟

- 取得[撰寫 Log Analytics 查詢的不同資源](query-language.md)參考。
- 存取完整的[資料總管查詢語言的參考文件](/azure/kusto/query/)。
