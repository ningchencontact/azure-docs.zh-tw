---
title: Azure 監視器會發出警示的動作規則
description: 了解動作規則是什麼，以及如何設定和管理方式。
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e5d04fd136848684e866fae9768b252e3b6ca77f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138125"
---
# <a name="action-rules-preview"></a>動作規則 （預覽）

這篇文章描述動作的規則是什麼，以及如何設定和管理方式。

## <a name="what-are-action-rules"></a>什麼是動作規則？

動作規則可讓您在任何 Resource Manager 範圍 （訂用帳戶、 資源群組或資源） 定義動作 （或隱藏項目之動作）。 它們有各種不同的篩選器可讓您縮小至您想要在處理的警示執行個體的特定子集。 

您可以使用動作的規則：

* 如果您已規劃的維護期間或週末/假日，而不需要停用每個警示規則個別，隱藏動作和通知。
* 定義動作和大規模的通知：不需要定義個別針對每個警示的規則動作群組，您現在可以定義在任何範圍所產生的警示觸發的動作群組。 比方說，我可以選擇將動作群組 'ContosoActionGroup' 觸發程序來產生我的訂用帳戶內的每個警示。

## <a name="configuring-an-action-rule"></a>設定動作規則

您可以存取此功能，方法是選取**管理動作**從登陸頁面，在 Azure 監視器中的警示。 然後選取**動作的規則 （預覽）**。 您可以存取它們，方法是選取**動作的規則 （預覽）** 警示的登陸頁面的儀表板。

![從 Azure 監視器登陸頁面的動作規則](media/alerts-action-rules/action-rules-landing-page.png)

選取  **+ 新動作規則**。 

![加入新的 「 動作 」 規則](media/alerts-action-rules/action-rules-new-rule.png)

或者，您也可以選擇設定警示規則時建立動作規則。

![加入新的 「 動作 」 規則](media/alerts-action-rules/action-rules-alert-rule.png)

現在，您應該會看到開啟動作的規則建立流程。 設定下列項目： 

![新的動作規則建立流程](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>`Scope`

第一次選擇的範圍，也就是目標資源、 資源群組或訂用帳戶。 您也可以進行多重選取 （在單一訂用帳戶中） 上述任一種的組合。 

![動作規則範圍](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>篩選準則

此外，您就可以進一步縮小到特定的警示上定義的範圍子集來定義篩選器。 

可用的篩選器如下： 

* **嚴重性**：選取一或多個警示的嚴重性。 嚴重性 = Sev1 表示動作的規則是適用於具有 Sev1 嚴重性的所有警示。
* **監視服務**:原始的監視服務為基礎的篩選條件。 這也是多重選取。 例如，監視服務 ="Application Insights"表示動作的規則是適用於所有 「 Application Insights 」 型警示。
* **資源類型**:根據特定的資源類型進行篩選。 這也是多重選取。 例如，資源類型 = 動作規則是適用於所有虛擬機器的 「 虛擬機器 」 表示。
* **警示規則識別碼**:可讓您篩選特定的警示規則，使用 Resource Manager 識別碼的警示規則。
* **監視條件**:為監視條件篩選警示的執行個體 「 引發 」 或 「 已解決 」。
* **描述**：Regex 比對中定義的警示規則的描述。
* **警示的內容 （裝載）**:RegEx 的比對內[警示內容](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields)警示執行個體的欄位。

這些篩選器套用至另一部一起使用。 例如，如果我在設定 '資源類型' = ' 虛擬機器 和 嚴重性 = 'Sev0'，則我已經在我的 Vm 上篩選 'Sev0' 的所有警示。 

![動作的規則篩選器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>隱藏項目 」 或 「 動作群組設定

接下來設定警示歸併或動作群組支援的動作規則。 您無法選擇兩者。 組態會比對先前定義的範圍和篩選條件的所有警示執行個體上。

#### <a name="suppression"></a>隱藏項目

如果您選取**歸併**，設定動作和通知的歸併的持續時間。 選擇下列其中一項：
* **從現在開始 （一律）**:無限期地隱藏所有通知。
* **在排定的時間**:隱藏已繫結的持續時間內的通知。
* **使用循環**:隱藏週期性排程，可以是每日、 每週或每月。

![動作規則隱藏項目](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>動作群組

如果您選取**動作群組**中切換，加入現有的動作群組或建立新的帳戶。 

> [!NOTE]
> 您可以只有一個動作群組關聯的動作規則中。

![規則動作的動作群組](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>動作規則詳細資料

最後，設定執行規則的下列詳細資料
* 名稱
* 它會儲存所在的資源群組
* 描述 

## <a name="example-scenarios"></a>範例案例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>案例 1：根據嚴重性的警示的隱藏項目

Contoso 想要隱藏其訂用帳戶內 'ContosoSub' 每個週末的所有 Vm 上的所有 Sev4 警示的通知。

**解決方案：** 建立動作規則
* Scope = 'ContosoSub'
* 篩選器
    * 嚴重性 = 'Sev4'
    * 資源類型 = 「 虛擬機器 」
* 使用循環的隱藏項目設定為每週，且 '星期六' 和 '星期日' 檢查

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>案例 2：隱藏項目之警示的內容 （裝載） 為基礎的警示

Contoso 想要隱藏所有記錄檔針對 ' 電腦-01' 產生的警示的通知中 'ContosoSub' 無限期地，它正在進行維護。

**解決方案：** 建立動作規則
* Scope = 'ContosoSub'
* 篩選器
    * Monitor Service = 'Log Analytics'
    * 警示的內容 （裝載） 包含 ' 電腦-01'
* 隱藏項目設定為 ' 即日起 （永遠） '

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>案例 3：在資源群組定義的動作群組

Contoso 已定義[訂用帳戶層級的計量警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)，但想要定義警示，分別針對其資源群組 'ContosoRG' 的觸發程序的動作。

**解決方案：** 建立動作規則
* Scope = 'ContosoRG'
* 沒有篩選器
* 設定為 'ContosoActionGroup' 的動作群組

## <a name="managing-your-action-rules"></a>管理您的動作規則

您可以檢視和管理您動作的規則，從清單檢視，如下所示。

![動作的規則清單檢視](media/alerts-action-rules/action-rules-list-view.png)

從這裡開始，您可以大規模的啟用/停用/刪除動作規則選取旁邊的核取方塊。 按一下任何動作的規則會開啟其 [設定] 頁面中，可讓您更新其定義，並啟用/停用它。

## <a name="best-practices"></a>最佳作法

記錄警示以建立['的結果數目'](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log)選項產生**單一警示的執行個體**使用整個搜尋結果 （這可能是在多部電腦為例）。 在此案例中，如果 「 動作 」 規則會使用 「 警示內容 （裝載） 」 篩選器，它將會執行警示的執行個體上，只要沒有相符項目。 在案例 2 中先前所述，如果產生的記錄警示的搜尋結果同時包含 '電腦-01' 和' 電腦-02'、 整個通知隱藏 （也就是沒有產生 ' 電腦-02' 完全沒有通知）。

![動作的規則與記錄警示 （結果數目）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

最佳運用記錄警示動作的規則，我們建議您建立與記錄警示['計量測量'](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log)選項。 使用此選項，個別警示的執行個體產生根據定義的群組欄位。 則在案例 2 中，個別警示的執行個體產生 '電腦-01' 和' 電腦-02'。 與動作規則案例所述的情況下，' 電腦-02' 的通知會引發如往常繼續時，將會隱藏只 ' 電腦-01' 的通知。

![動作的規則與記錄警示 （結果數目）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常見問題集

* 問： 在設定執行規則時，我想要查看所有可能的重疊動作都規則，因此我要避免重複的通知。 是否可以這麼做？

    A. 一旦您在設定執行規則時定義範圍，您可以看到一份動作規則 （如果有的話），在相同範圍重疊。 此重疊可以是下列其中一個選項的其中一個：
    * 完全相符：比方說，您正在定義動作規則和重疊的動作規則是在相同的訂用帳戶中。
    * 子集：例如，您正在定義的動作規則是訂用帳戶，和重疊的動作規則所在的訂用帳戶內的資源群組。
    * 超集：比方說，您會定義動作規則所在資源群組，和重疊的動作規則所在的訂用帳戶包含的資源群組。
    * 交集：比方說，您會定義動作規則位於 'VM1' 和 'VM2'，和重疊的動作規則位於 'VM2' 和 'VM3'。

    ![重疊的動作規則](media/alerts-action-rules/action-rules-overlapping.png)

* 問： 時設定的警示規則，它可以知道是否有已定義動作的規則，可能會依據我在定義的警示規則？

    A. 一旦定義警示規則的目標資源，您可以看到處理在相同的範圍 （如果有的話） 按一下 [檢視設定動作] '動作' 區段下的動作規則的清單。 這份清單會填入該範圍的下列案例為基礎：
    * 完全相符：比方說，您要定義警示規則和動作規則是在相同的訂用帳戶。
    * 子集：例如，您正在定義的警示規則是訂用帳戶，而動作規則所在的訂用帳戶內的資源群組。
    * 超集：比方說，您要定義警示規則位於資源群組，和動作規則所在的訂用帳戶包含的資源群組。
    * 交集：例如，'VM1' 和 'VM2'，位於您要定義警示規則和動作規則位於 'VM2' 和 'VM3'。
    
    ![重疊的動作規則](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* 問： 我是否可以看到已隱藏動作規則的警示？

    A. 在 [警示清單頁面](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)，沒有額外的資料行，可以選擇呼叫 「 隱藏項目狀態 」。 如果已抑制警示執行個體的通知，它會顯示在清單中的該狀態。

    ![隱藏的警示執行個體](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* 問： 如果沒有動作規則動作群組，並隱藏項目與另一個使用相同範圍中，會發生什麼事？

    A. **一律隱藏項目會優先使用在相同範圍**。

* 問： 如果我有兩個單獨的動作規則中監視的資源發生什麼事？ 取得一或兩個通知？ 例如 'VM2' 在此案例中：

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    A. 針對 'VM1' 和 'VM3' 上的每個警示，不會一次觸發動作群組 'AG1'。 在 'VM2' 上的每個警示的動作群組 'AG1' 就會觸發兩次 (**動作規則無法刪除重複動作**)。 

* 問： 如果我有兩個單獨的動作規則中監視的資源，而其中一個呼叫的動作，而另一個用於隱藏項目，會發生什麼事？ 例如，'VM2' 在此案例中：

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. 針對 'VM1' 上的每個警示，不會一次觸發動作群組 'AG1'。 將隱藏動作和 'VM2' 和 'VM3' 上的每個警示的通知。 

* 問： 如果我有警示規則和動作規則定義的相同資源呼叫不同的動作群組發生什麼事？ 例如，在此案例中的 ' VM1':

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    A. 針對 'VM1' 上的每個警示，不會一次觸發動作群組 'AG1'。 每當觸發警示的規則 'rule1' 時，它也會觸發 'AG2' 此外。 **動作規則中定義的動作群組和警示規則分別作用，與任何重複**。 

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure 中的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
