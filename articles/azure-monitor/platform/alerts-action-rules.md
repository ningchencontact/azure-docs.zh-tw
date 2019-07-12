---
title: Azure 監視器會發出警示的動作規則
description: 了解 Azure 監視器中的動作規則是什麼，以及如何設定和管理方式。
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656728"
---
# <a name="action-rules-preview"></a>動作規則 （預覽）

動作規則可協助您定義或隱藏任何的 Azure Resource Manager 範圍 （Azure 訂用帳戶、 資源群組或目標資源） 的動作。 它們具有各種的篩選條件，協助您縮小警示執行個體的特定子集，您想要處理的。

## <a name="why-and-when-should-you-use-action-rules"></a>為何和何時應該使用動作規則？

### <a name="suppression-of-alerts"></a>警示的隱藏項目

有許多情況下，可抑制警示產生通知。 這些案例範圍從計劃的維護期間的隱藏項目到隱藏在非營業時間的項目。 例如，負責 team **ContosoVM**想要在即將推出的週末，抑制警示通知，因為**ContosoVM**正在進行規劃的維護。 

雖然小組可以停用每個警示規則上設定**ContosoVM**手動 （並維護之後再次加以啟用），它不是簡單的程序。 動作規則可協助您定義能夠彈性地設定的一段歸併的警示歸併功能，大規模。 在上述範例中，小組可以在定義一項動作的規則**ContosoVM**可隱藏週末的所有警示的通知。


### <a name="actions-at-scale"></a>大規模的動作

警示規則可協助您定義的觸發程序時，會產生警示的動作群組，雖然客戶經常會有通用的動作群組跨越其範圍的作業。 例如，小組負責的資源群組**ContosoRG**可能會定義相同的動作群組內定義的所有警示規則**ContosoRG**。 

動作規則可協助您簡化這個程序。 藉由定義大規模的動作，可針對已設定的領域產生任何警示觸發的動作群組。 在上述範例中，小組可以在定義一項動作的規則**ContosoRG**就會觸發相同的動作群組，其內所產生的所有警示。

> [!NOTE]
> 目前動作規則不適用於 Azure 服務健康情況警示。

## <a name="configuring-an-action-rule"></a>設定動作規則

您可以存取此功能，方法是選取**管理動作**從**警示**Azure 監視器中的登陸頁面。 然後，選取**動作的規則 （預覽）** 。 您可以存取的規則，方法是選取**動作的規則 （預覽）** 警示的登陸頁面的儀表板。

![從 Azure 監視器登陸頁面的動作規則](media/alerts-action-rules/action-rules-landing-page.png)

選取  **+ 新動作規則**。 

![加入新的 「 動作 」 規則](media/alerts-action-rules/action-rules-new-rule.png)

或者，您可以建立 「 動作 」 規則，而您要設定警示規則。

![加入新的 「 動作 」 規則](media/alerts-action-rules/action-rules-alert-rule.png)

現在，您應該會看到建立動作規則的 [流程] 頁面。 設定下列項目： 

![新的動作規則建立流程](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>`Scope`

第一次選擇的範圍 （Azure 訂用帳戶、 資源群組或目標資源）。 您可以也選取多個在單一訂用帳戶範圍的組合。

![動作規則範圍](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>篩選準則

此外，您可以定義篩選來縮小它們到警示的特定子集。 

可用的篩選器如下： 

* **嚴重性**：要選取一或多個警示的嚴重性的選項。 **嚴重性 = Sev1**表示動作的規則是適用於所有的警示設定為 Sev1。
* **監視服務**:根據原始的監視服務篩選條件。 此篩選條件也是多重選取。 例如，**監視服務 ="Application Insights"** 表示動作的規則是適用於所有 Application Insights 為基礎的警示。
* **資源類型**:依據特定的資源類型的篩選條件。 此篩選條件也是多重選取。 例如，**資源類型 = 「 虛擬機器 」** 表示動作的規則是適用於所有虛擬機器。
* **警示規則識別碼**:若要使用 Resource Manager 識別碼的警示規則來篩選特定的警示規則選項。
* **監視條件**:警示的執行個體使用的篩選條件**引發**或**Resolved**做為監視條件。
* **描述**：（規則運算式） 的 regex 相符項目會定義針對描述，定義警示規則的一部分的字串相符項目。 例如，**描述包含 'prod'** 會比對包含字串"prod"的所有警示描述中。
* **警示內容 （裝載）** :Regex 相符項目定義對警示的承載的警示內容欄位的字串相符。 例如，**警示內容 （裝載） 包含 ' 電腦-01'** 會比對所有的警示，其裝載包含字串"電腦-01。 」

這些篩選會套用與另一個搭配使用。 比方說，如果您設定**資源類型 '= 虛擬機器**並**嚴重性' = Sev0**，然後您篩選所有**Sev0**只在 Vm 上的警示。 

![動作的規則篩選器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>隱藏項目 」 或 「 動作群組設定

接下來，設定警示歸併或動作群組支援的動作規則。 您無法選擇兩者。 組態會比對先前定義的範圍和篩選條件的所有警示執行個體上。

#### <a name="suppression"></a>隱藏項目

如果您選取**歸併**，設定動作和通知的歸併的持續時間。 選擇下列其中一個選項：
* **從現在開始 （一律）** :無限期地隱藏所有通知。
* **在排定的時間**:隱藏已繫結的持續時間內的通知。
* **使用循環**:隱藏週期性的每日、 每週或每月排程的通知。

![動作規則隱藏項目](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>動作群組

如果您選取**動作群組**中切換，加入現有的動作群組或建立新的帳戶。 

> [!NOTE]
> 您可以只有一個動作群組關聯的動作規則中。

![加入或建立新的動作規則選取動作群組](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>動作規則詳細資料

最後，設定執行規則的下列詳細資料：
* 名稱
* 在其中儲存的資源群組
* 描述 

## <a name="example-scenarios"></a>範例案例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>案例 1：根據嚴重性的警示的隱藏項目

Contoso 想要隱藏在訂用帳戶內的所有 Vm 上的所有 Sev4 警示通知**ContosoSub**每周末。

**解決方案：** 建立動作規則：
* Scope = **ContosoSub**
* 篩選器
    * 嚴重性 = **Sev4**
    * 資源類型 =**虛擬機器**
* 隱藏項目具有設定為每週、 週期和**星期六**並**星期日**檢查

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>案例 2：隱藏項目之警示的內容 （裝載） 為基礎的警示

Contoso 想要隱藏通知的所有記錄檔針對產生的警示**電腦-01**中**ContosoSub**無限期地因為它正在進行維護。

**解決方案：** 建立動作規則：
* Scope = **ContosoSub**
* 篩選器
    * Monitor Service = **Log Analytics**
    * 警示內容 （裝載） 包含**電腦-01**
* 隱藏項目設定為**即日起 （一律）**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>案例 3：在資源群組定義的動作群組

Contoso 已定義[訂用帳戶層級的計量警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但它想要定義專為產生的資源群組的警示，觸發程序的動作**ContosoRG**。

**解決方案：** 建立動作規則：
* Scope = **ContosoRG**
* 沒有篩選器
* 動作群組設定為**ContosoActionGroup**

> [!NOTE]
> *動作規則中定義的動作群組和警示規則會在沒有重複資料刪除獨立地運作。* 中所述的案例更早版本，如果動作群組都定義為警示的規則，就會觸發搭配動作規則中所定義的動作群組。 

## <a name="managing-your-action-rules"></a>管理您的動作規則

您可以檢視並管理您動作的規則，從清單檢視：

![動作的規則清單檢視](media/alerts-action-rules/action-rules-list-view.png)

從這裡開始，您可以啟用、 停用，或刪除動作規則大規模旁邊的核取方塊。 當您選取的動作的規則時，其組態 頁面隨即開啟。 頁面可協助您更新動作規則的定義，以及啟用或停用它。

## <a name="best-practices"></a>最佳作法

記錄您使用建立的警示[結果數目](alerts-unified-log.md)選項使用整個搜尋結果 （這可能會跨越多部電腦） 來產生單一的警示執行個體。 在此案例中，如果使用 「 動作 」 規則**警示內容 （裝載）** 篩選條件，它會在警示的執行個體上，只要沒有相符項目。 案例 2 中先前所述，如果產生的記錄警示的搜尋結果同時包含這兩者**電腦-01**並**電腦-02**，隱藏整個的通知。 沒有通知的產生**電腦-02**完全。

![動作的規則與記錄警示 （結果數目）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要最適合搭配動作規則的記錄警示，建立與記錄警示[計量測量](alerts-unified-log.md)選項。 此選項時，根據其定義的群組欄位會產生個別警示的執行個體。 然後，在案例 2 中，個別警示的執行個體產生**電腦-01**並**電腦-02**。 因為動作規則所述的案例中，針對通知**電腦-01**隱藏。 通知**電腦-02**引發如往常繼續。

![動作的規則與記錄警示 （結果數目）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常見問題集

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>我要設定的動作的規則，而我想要使避免重複的通知，請參閱所有可能的重疊動作規則。 它是這麼做？

當您設定動作的規則，定義領域後，您可以看到一份動作規則 （如果有的話），在相同範圍重疊。 此重疊可以是下列其中一個選項的其中一個：

* 完全相符：比方說，您定義的動作規則和重疊的動作規則是在相同的訂用帳戶中。
* 子集：例如，您定義的動作規則是訂用帳戶，和重疊的動作規則所在的訂用帳戶內的資源群組。
* 超集：例如，您定義的動作規則位於資源群組，和重疊的動作規則所在的訂用帳戶包含的資源群組。
* 交集：比方說，您定義的動作規則位於**VM1**並**VM2**，且重疊的動作規則上**VM2**並**VM3**。

![重疊的動作規則](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>我要設定的警示規則，而是，可能會知道是否有已定義動作的規則，可能會依據我在定義的警示規則？

警示規則定義之目標資源之後，您可以看到處理相同的範圍 （如果有的話） 所選取的動作規則的清單**設定動作 檢視**下方**動作**區段。 這份清單會填入基礎範圍在下列案例：

* 完全相符：比方說，您定義的警示規則和動作規則是在相同的訂用帳戶中。
* 子集：例如，您定義的警示規則是訂用帳戶，而動作規則所在的訂用帳戶內的資源群組。
* 超集：比方說，您定義的警示規則位於資源群組和動作規則所在的訂用帳戶包含的資源群組。
* 交集：比方說，您定義的警示規則是在**VM1**並**VM2**，而且動作規則位於**VM2**和**VM3**。
    
![重疊的動作規則](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>我是否可以看到已隱藏動作規則的警示？

在 [警示清單頁面](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)，您可以選擇額外的資料行，呼叫**隱藏項目狀態**。 如果已抑制警示執行個體的通知，它會顯示在清單中的該狀態。

![隱藏的警示執行個體](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果沒有動作規則動作群組，並隱藏項目與另一個使用相同範圍中，會發生什麼事？

一律隱藏項目會優先使用在相同範圍。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果我有兩個不同的動作規則中監視的資源，會發生什麼事？ 取得一或兩個通知？ 例如， **VM2**在下列案例：

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

在 VM1 和 VM3 的每個警示的動作群組 AG1 不會一次觸發。 在每個警示**VM2**，AG1 就會觸發兩次，因為動作規則不重複資料刪除動作的動作群組。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果我有兩個單獨的動作規則中監視的資源，而其中一個呼叫的動作，而另一個用於隱藏項目，會發生什麼事？ 例如， **VM2**在下列案例：

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

為 VM1 上每個警示，不會一次觸發動作群組 AG1。 將隱藏動作和 VM2 和 VM3 上的每個警示的通知。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果我有警示規則和動作規則定義的相同資源呼叫不同的動作群組發生什麼事？ 例如， **VM1**在下列案例：

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
為 VM1 上每個警示，不會一次觸發動作群組 AG1。 每當觸發 「 規則 1 」 的警示規則時，它也會觸發 AG2 此外。 動作規則中定義的動作群組和警示規則會在沒有重複資料刪除獨立地運作。 

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure 中的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
