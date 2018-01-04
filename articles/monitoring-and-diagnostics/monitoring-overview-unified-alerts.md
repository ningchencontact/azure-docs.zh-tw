---
title: "瀏覽 Azure 監視的警示 （預覽） 新體驗 |Microsoft 文件"
description: "了解新的簡單且可調整警示如何體驗 Azure 可讓撰寫、 檢視及管理更容易的警示"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 5ded43548d9aea106c6e083476df4e735b8c00a6
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>瀏覽 Azure 監視的警示 （預覽） 新體驗

## <a name="overview"></a>概觀
 在 Azure 中的體驗有新的警示查詢和更新功能。 這個新體驗可從**警示 （預覽）** Azure 螢幕下方的索引標籤。 下列是一些使用新的警示 （預覽） 體驗的優點：

 - **引發警示與警示規則的分隔**-在 Alerts(Preview) 經驗中，警示規則 （會觸發警示的條件的定義），並引發警示 （警示規則引發的執行個體） 而有所區別，因此操作和組態檢視被分開的。 
 - **A 統一度量和記錄檔的警示的撰寫體驗**-撰寫體驗輔助線設定警示的規則，使其更容易找出正確的項目上取得警示的程序以及使用者的新警示 （預覽）。 
 - **檢視引發 Azure 入口網站中的記錄分析警示**-中警示 （預覽） 體驗，您現在可以也請參閱引發記錄分析您的訂用帳戶中的警示。  

下列各節描述的詳細資料，新體驗的運作方式。 

## <a name="taxonomy"></a>分類
Alerts(preview) 經驗會使用下列概念中，分隔的警示規則，並引發警示的物件時整合跨越不同警示類型的撰寫經驗。

- **目標資源**-目標可以是任何 Azure 資源。 目標資源定義的範圍和警示使用的訊號。 範例： 虛擬機器、 儲存體帳戶、 虛擬機器規模集、 記錄分析工作區中或方案。 

- **準則**-準則組合的訊號並套用目標資源的邏輯。 範例： 百分比 CPU > 70%，伺服器回應時間 > 4 毫秒，記錄檔的結果計數查詢 > 100 等等。 

- **訊號**-信號所發出的目標資源，而且可以是數個類型。 本預覽版支援度量和記錄檔的訊號類型。

- **邏輯**-使用者定義的邏輯，以檢查訊號是否在預期範圍/值。  
 
- **動作**-傳送至收件者的通知 （例如，以電子郵件傳送地址或張貼到 webhook URL） 的特定呼叫。 通知通常可觸發多個動作。 支援在此預覽中，支援的動作群組的警示類型。  
 
- **警示規則**-會觸發警示條件的定義。 在此預覽中，警示規則會擷取的目標和準則的警示。 警示規則可以是已啟用或停用狀態。 
 
- **引發警示**-引發啟用的警示規則時建立。 引發警示的物件可以是引發或無法解析的狀態。

    > [!NOTE]
    > 這點不同於目前的警示體驗警示代表規則並引發的警示，因此可以是一個警告，作用中或已停用狀態。
    >

## <a name="single-place-to-view-and-manage-alerts"></a>使用單一位置來檢視及管理警示
警示 （預覽） 經驗的目標是單一的位置，來檢視和管理您所有 Azure 的警示。 下列小節將描述每個個別的畫面的新體驗的函式。

### <a name="alerts-preview-overview-page"></a>警示 （預覽） 概觀頁面
**監視的警示 （預覽）**概觀 頁面上顯示彙總的摘要所有引發的警示，並總計設定/啟用警示規則。 它也會顯示一份所有引發的警示。 變更訂用帳戶或篩選參數更新彙總，而且警示引發清單。

> [!NOTE]
> 引發的警示 （預覽） 中顯示的警示僅限於支援度量和記錄檔的警示。Azure 監視便會顯示引發警示，包括較舊的 Azure 警示中的計數

 ![警示預覽概觀](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>管理警示規則
**監視的警示 （預覽） > 規則**只有單一頁面來管理您的 Azure 訂用帳戶之間的所有警示的規則。 它會列出所有的警示規則 （啟用或停用），而且可以根據目標資源、 資源群組，規則名稱或狀態排序。 警示規則也可以停用/啟用或編輯從這個頁面。  

 ![警示規則預覽](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>所有的監視來源撰寫經驗的一個警示
警示 （預覽） 中遇到，警示可以撰寫以一致的方式，不論監視服務，或表示型別。 所有警示都引發並且能在單一頁面中提供的相關詳細資料。  
 
撰寫警示是三個步驟工作使用者第一次選取的目標警示中，後面接著選取右邊的訊號，然後指定要套用在訊號警示規則的一部分的邏輯。 這個簡化的製作程序不再需要使用者知道的監視來源或前，先選取一項 Azure 資源支援的訊號。 常見的撰寫經驗自動篩選的可選取的目標資源為基礎的訊號清單，並建立警示的邏輯會引導

深入了解如何建立下列警示類型[這裡](monitor-alerts-unified-usage.md)。 
- 度量的警示 （在目前的體驗中稱為接近即時度量的警示）
- 記錄檔警示 （記錄分析）
 

## <a name="alert-types-supported-in-this-preview"></a>支援在這個預覽中的警示類型


| **訊號類型** | **監視來源** | **說明** | 
|-------------|----------------|-------------|
| 計量 | Azure 監視器 | 呼叫[**接近即時度量警示**](monitoring-near-real-time-metric-alerts.md)中目前的體驗，這些度量的警示支援為 1 分鐘經常評估度量的條件，並允許多重度量的規則。 支援的資源類型的清單可[這裡](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for)。 其他度量的警示定義[這裡](monitoring-overview-alerts.md#alerts-in-different-azure-services)不支援中的警示 （預覽） 體驗。|
| 記錄檔  | Log Analytics | 接收通知或度量及/或事件資料的記錄搜尋查詢符合特定準則時，執行自動化的動作。|
| 記錄檔  | 活動記錄檔 | 不支援此警示 （預覽） 體驗。 |
| 記錄檔  | Application Insights | 不支援此警示 （預覽） 體驗。 |
| 計量 | Application Insights | 不支援此警示 （預覽） 體驗。 |
| 可用性測試 | Application Insights | 不支援此警示 （預覽） 體驗。 |


## <a name="next-steps"></a>後續步驟
- [了解如何使用新的警示 （預覽） 體驗，若要建立、 檢視和管理警示](monitor-alerts-unified-usage.md)
- [深入了解警示 （預覽） 體驗中的記錄檔警示](monitor-alerts-unified-log.md)
- [深入了解警示 （預覽） 的經驗度量的警示](monitoring-near-real-time-metric-alerts.md)

