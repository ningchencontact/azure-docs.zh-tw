---
title: Azure 監視 Log Analytics 中的記錄查詢範圍 |Microsoft Docs
description: 描述 Azure 監視 Log Analytics 中的記錄檔查詢的範圍和時間範圍。
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297249"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure 監視 Log Analytics 中的記錄查詢範圍和時間範圍
當您執行[記錄檔查詢](log-query-overview.md)中[Azure 入口網站中的 Log Analytics](get-started-portal.md)，評估查詢的資料集取決於範圍和您選取的時間範圍。 本文說明的範圍和時間範圍，以及設定每個根據您的需求。 它也會說明行為的不同類型的範圍。


## <a name="query-scope"></a>查詢範圍
查詢範圍定義的查詢會評估的記錄。 這通常會包括所有記錄中的單一 Log Analytics 工作區或 Application Insights 應用程式。 Log Analytics 也可讓您設定特定的受監視 Azure 資源的範圍。 這可讓資源擁有者，以便只著重於其資料，即使該資源寫入多個工作區。

範圍永遠會顯示在頂端的 [Log Analytics] 視窗。 圖示表示範圍是 Log Analytics 工作區或 Application Insights 應用程式。 無圖示代表另一個 Azure 資源。

![`Scope`](media/scope/scope.png)

用來啟動 Log Analytics，而且在某些情況下您可以變更範圍對它按一下，則方法會判斷範圍。 下表列出不同類型的使用範圍與每個不同的詳細資料。

| 查詢範圍 | 在範圍內的記錄 | 如何選取 | 變更範圍 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | Log Analytics 工作區中的所有記錄。 | 選取 **記錄檔**從**Azure 監視器**功能表或**Log Analytics 工作區**功能表。  | 可以變更成任何其他資源類型的範圍。 |
| Application Insights 應用程式 | 在 Application Insights 應用程式中的所有記錄。 | 選取 [ **Analytics**從**概觀**的 Application Insights] 頁面。 | 另一個 Application Insights 應用程式只能變更範圍。 |
| 資源群組 | 建立的資源群組中的所有資源的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 選取 **記錄檔**的資源群組功能表。 | 無法變更範圍。|
| 訂用帳戶 | 建立的訂用帳戶中的所有資源的記錄。 可能包含來自多個 Log Analytics 工作區的資料。 | 選取 [**記錄檔**從訂用帳戶] 功能表。   | 無法變更範圍。 |
| 其他 Azure 資源 | 建立資源的記錄。 可能包含來自多個 Log Analytics 工作區的資料。  | 選取 **記錄檔**從 資源 功能表。<br>或<br>選取 **記錄檔**從**Azure 監視器**功能表，然後選取新的範圍。 | 只能變更範圍相同的資源類型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>範圍為資源時的限制

當查詢範圍是 Log Analytics 工作區或 Application Insights 應用程式時，將可供在入口網站中的所有選項和所有的查詢命令。 因為它們與單一工作區或應用程式相關聯時透過範圍為資源，下列選項無法使用入口網站中：

- 儲存
- 查詢總管
- 新增警示規則

您無法在查詢時範圍為資源，因為查詢範圍已經包含具有該資源或資源集之資料的任何工作區中使用下列命令：

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>時間範圍
時間範圍內指定的一組查詢是根據記錄建立時將會接受評估的記錄。 這是上表中所指定的應用程式的工作區中的每一筆記錄的標準屬性所定義。

| 位置 | 屬性 |
|:---|:---|
| Log Analytics 工作區          | TimeGenerated |
| Application Insights 應用程式 | timestamp     |

選取從時間選擇器頂端的 [Log Analytics] 視窗中設定的時間範圍。  您可以選取預先定義的期間，或選取**自訂**來指定特定時間範圍內。

![時間選擇器](media/scope/time-picker.png)

如果您在查詢中使用的標準時間 」 屬性，如上表所示設定篩選，時間選擇器會變成**在查詢中設定**，且已停用時間選擇器。 在此情況下，它會最有效率放在查詢頂端的篩選，以便任何後續的處理就只需要使用已篩選的記錄。

![篩選的查詢](media/scope/query-filtered.png)

如果您使用[工作區](workspace-expression.md)或是[應用程式](app-expression.md)命令來擷取另一個工作區或應用程式中的資料，時間選擇器行為可能會有所不同。 如果範圍是 Log Analytics 工作區，而且您使用**應用程式**，或如果範圍是 Application Insights 應用程式，並使用**工作區**，則 Log Analytics 可能不了解中使用的屬性篩選條件應該判斷時間篩選。

在下列範例中，範圍是設定為 Log Analytics 工作區。  此查詢會使用**工作區**來擷取另一個 Log Analytics 工作區中的資料。 時間選擇器變更為**在查詢中設定**看到使用預期的篩選條件，因為**TimeGenerated**屬性。

![查詢與工作區](media/scope/query-workspace.png)

如果查詢使用**應用程式**不過要從 Application Insights 應用程式中擷取資料，無法辨識的 Log Analytics**時間戳記**篩選和時間選擇器中的屬性會維持不變。 在此情況下，這兩個篩選會套用。 在範例中，只有在過去 24 小時中建立的記錄會包含在查詢中即使它會指定在 7 天**其中**子句。

![查詢與應用程式](media/scope/query-app.png)

## <a name="next-steps"></a>後續步驟

- 逐步解說[使用 Azure 入口網站中的 Log Analytics 的教學課程](get-started-portal.md)。
- 逐步解說[撰寫查詢的教學課程](get-started-queries.md)。