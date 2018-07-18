---
title: 使用跨平台 Azure CLI 建立 Azure 服務的傳統警示 | Microsoft Docs
description: 當符合您指定的條件時，觸發電子郵件或通知、或呼叫網站 URL (Webhook) 或自動化。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287091"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>使用跨平台 Azure CLI 在 Azure 監視器中為 Azure 服務建立傳統計量警示 

> [!div class="op_single_selector"]
> * [入口網站](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 Azure 監視器現在支援[更新更好的計量警示](monitoring-near-real-time-metric-alerts.md)。 這些警示可以監視多個計量，且能針對維度計量發出警示。 即將推出更新計量警示的 Azure CLI 支援。
>
>

本文說明如何使用跨平台命令列介面 (Azure CLI) 來設定 Azure 傳統計量警示。

> [!NOTE]
> 自 2016 年 9 月 25 日起，「Azure 監視器」是以前所謂「Azure Insights」的新名稱。 不過，命名空間及此處描述的命令中仍包含 "insights" 一字。

您可以收到的警示會以 Azure 服務計量為基礎，或以 Azure 中發生的事件為基礎。

* **量值**：當指定的量值超出您在任一方向指派的閾值時會觸發警示。 也就是說，先符合條件而之後該條件不再符合時，兩方面皆會觸發警示。    

* **活動記錄事件**：警示可在「每一個」事件上觸發，或是在發生特定事件時觸發。 若要深入了解活動記錄，請參閱[建立活動記錄警示 (傳統)](monitoring-activity-log-alerts.md)。 

您可以設定讓傳統計量警示在觸發時執行下列動作︰

* 傳送電子郵件通知給服務管理員和共同管理員。 
* 將電子郵件傳送至您指定的電子郵件地址。
* 呼叫 Webhook。
* 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)。

您可以使用下列方式來設定和取得有關傳統計量警示規則的資訊： 

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

輸入命令並在結尾加上 **-help**，也可以收到命令的說明。 以下是範例： 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>使用 Azure CLI 建立警示規則
1. 安裝必要元件之後，登入 Azure。 請參閱 [Azure 監視器 CLI 範例](insights-cli-samples.md)，以取得開始作業時需要的命令。 這些命令可協助您您登入、顯示您正在使用的訂用帳戶，以及準備執行 Azure 監視器命令。

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. 若要列出資源群組上的現有規則，使用下列格式： 

   **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. 若要建立規則，您需要先取得幾項重要資訊：
    * 針對您想要為其設定警示的資源，取得其**資源識別碼**。
    * 該資源可用的**計量定義**。

     取得資源識別碼的方法之一，是使用 Azure 入口網站。 假設已建立資源，在入口網站中選取它。 然後在下一個刀鋒視窗中，選取 [設定] 區段中的 [屬性]。 [資源識別碼] 是下一個刀鋒視窗中的欄位。 
     
     您也可以使用 [Azure 資源總管](https://resources.azure.com/)取得資源識別碼。

     以下是 Web 應用程式的範例資源識別碼：

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     若要取得先前資源範例中那些計量的可用計量和單位清單，使用下列的 Azure CLI 命令︰  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     PT1M 是可用度量單位的資料粒度 (間隔為 1 分鐘)。 使用不同的資料粒度時，有不同的計量選項可選擇。
     
4. 若要建立以計量為基礎的警示規則，使用下列格式的命令︰

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    下列範例會設定網站資源的警示。 只要持續 5 分鐘有收到任何流量，就會觸發警示，在這之後，5 分鐘沒收到任何流量也會觸發警示。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. 若要在傳統計量警示引發時建立 Webhook 或傳送電子郵件，請先建立電子郵件或 Webhook。 然後立即建立規則。 您無法將 Webhook 或電子郵件與已建立的規則建立關聯。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. 您可以藉由查看個別規則來確認您的警示已正確建立。

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. 若要刪除規則，使用下列格式的命令：

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    這些命令會刪除本文中先前建立的規則。

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 深入了解 [在警示中設定 webhook](insights-webhooks-alerts.md)。
* 深入了解[在活動記錄事件上設定警示](monitoring-activity-log-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](../automation/automation-starting-a-runbook.md)。
* 依照[收集診斷記錄概觀](monitoring-overview-of-diagnostic-logs.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
