---
title: 設定 Azure 串流分析作業的監視警示
description: 本文說明如何使用 Azure 入口網站設定 Azure 串流分析作業的監視和警示。
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 26e8e004f917b1c138bc27389cac1cc52672f3d4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329865"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>設定 Azure 串流分析工作的警示

請務必監視您的 Azure 串流分析作業，以確保持續執行作業，而沒有任何問題。 本文說明如何針對應該監視的常見案例設定警示。 

您可以從入口網站中，透過作業記錄檔資料的度量定義規則，以及[以程式設計方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)。

## <a name="set-up-alerts-in-the-azure-portal"></a>在 Azure 入口網站中設定警示
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>作業意外停止時收到警示

下列範例示範如何設定當您的作業進入失敗狀態時的警示。 建議將此警示用於所有的作業。

1. 在 Azure 入口網站中，開啟您想要建立警示的串流分析作業。

2. 在 [作業]  頁面上，瀏覽至 [監視]  區段。  

3. 選取 **度量**，然後**新的警示規則**。

   ![Azure 入口網站串流分析警示設定](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. 您的串流分析作業名稱應該會自動出現在 [資源]  之下。 按一下 [新增條件]  ，然後選取 [設定訊號邏輯]  之下的 [所有系統管理作業]  。

   ![選取串流分析警示的訊號名稱](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. 在 [設定訊號邏輯]  之下，將 [事件層級]  變更為 [全部]  並將 [狀態]  變更為 [失敗]  . 離開**事件起始者**空白，然後選取**完成**。

   ![設定串流分析警示的訊號邏輯](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. 選取現有的動作群組或建立新的群組。 在此範例中，[電子郵件]  動作可將電子郵件傳送給具有 [擁有者]  Azure Resource Manager 角色的使用者，並經由該動作建立名為 **TIDashboardGroupActions** 的新動作群組。

   ![設定 Azure 串流分析作業的警示](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. [資源]  、[條件]  和 [動作群組]  應該各有一個項目。 請注意，若要引發警示，則必須符合所定義的條件。 例如，您可以在過去 15 分鐘內每隔 5 分鐘測量計量的平均值。

   ![建立串流分析警示規則](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   將 [警示規則名稱]  、[描述]  和您的 [資源群組]  新增至 [警示詳細資料]  ，然後按一下 [建立警示規則]  來建立串流分析作業的規則。

   ![建立串流分析警示規則](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>要監視的案例

建議將下列警示用於監視串流分析作業的效能。 這些計量應該在最後 5 分鐘的期間內每分鐘評估。

|計量|條件|時間彙總|閾值|更正措施|
|-|-|-|-|-|
|SU% 使用率|大於|最大值|80|有多個因素會讓 SU% 使用量增加。 您可以透過查詢平行化作業調整，或增加串流單位數目。 如需詳細資訊，請參閱[利用 Azure 串流分析中的查詢平行化作業](stream-analytics-parallelization.md)。|
|執行階段錯誤|大於|總計|0|檢查活動或診斷記錄，並且對輸入、查詢或輸出進行適當的變更。|
|浮水印延遲|大於|最大值|當此計量在過去 15 分鐘的平均值大於延遲傳入容許 (以秒為單位)。 如果您尚未修改延遲傳入容錯，則預設值會設為 5 秒。|請嘗試增加 SU 數目，或將您的查詢平行化。 如需 SU 詳細資訊，請參閱[了解及調整串流單位](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)。 如需查詢平行化的詳細資訊，請參閱[利用 Azure 串流分析中的查詢平行化作業](stream-analytics-parallelization.md)。|
|輸入還原序列化錯誤|大於|總計|0|檢查活動或診斷記錄，並且對輸入進行適當的變更。 如需診斷記錄的詳細資訊，請參閱[使用析診斷記錄針對 Azure 串流分進行疑難排解](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>取得說明

如需有關在 Azure 入口網站中設定警示的更多詳細資料，請參閱[接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。  

如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

