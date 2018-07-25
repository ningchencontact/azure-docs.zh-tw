---
title: 教學課程 - 監視 Azure 防火牆記錄
description: 在本教學課程中，您會了解如何啟用及管理 Azure 防火牆記錄。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991838"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>教學課程：監視 Azure 防火牆記錄

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure 防火牆文件中的範例假設您已啟用 Azure 防火牆公開預覽。 如需詳細資訊，請參閱[啟用 Azure 防火牆公用預覽](public-preview.md)。

您可以使用防火牆記錄來監視 Azure 防火牆。 您也可以使用活動記錄來稽核 Azure 防火牆資源上的作業。

您可以透過入口網站存取其中一些記錄。 您可以將記錄傳送到 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、儲存體和事件中樞，並在 Log Analytics 中或透過不同的工具 (例如 Excel 和 Power BI) 來分析記錄。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 透過 Azure 入口網站啟用記錄功能
> * 使用 PowerShell 啟用記錄
> * 檢視和分析活動記錄檔
> * 檢視及分析網路和應用程式規則記錄

## <a name="diagnostic-logs"></a>診斷記錄檔

 下列診斷記錄可供 Azure 防火牆使用：

* **應用程式規則記錄**

   只有當您針對每個 Azure 防火牆加以啟用時，應用程式規則記錄才會儲存到儲存體帳戶、串流至事件中樞及/或傳送至 Log Analytics。 符合其中一個已設定應用程式規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **網路規則記錄**

   只有當您針對每個 Azure 防火牆加以啟用時，網路規則記錄才會儲存到儲存體帳戶、串流至事件中樞及/或傳送至 Log Analytics。 符合其中一個已設定網路規則的每個新連線，都會產生接受/拒絕連線的記錄。 資料會以 JSON 格式記錄下來，如下列範例所示：

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

您有三個選項可用來排序您的記錄：

* **儲存體帳戶**：如果記錄會儲存一段較長的持續期間，並在需要時加以檢閱，則最好針對記錄使用儲存體帳戶。
* **事件中樞**：如果要整合其他安全性資訊和事件管理 (SEIM) 工具以便在資源上取得警示，則事件中樞是絕佳的選項。
* **Log Analytics**：Log Analytics 最適合用來進行應用程式的一般即時監視，或查看趨勢。

## <a name="activity-logs"></a>活動記錄

   預設會收集活動記錄，您可在 Azure 入口網站中檢視它們。

   您可以使用 [Azure 活動記錄](../azure-resource-manager/resource-group-audit.md) (之前稱為「作業記錄和稽核記錄」) 來檢視提交至您 Azure 訂用帳戶的所有作業。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>透過 Azure 入口網站啟用診斷記錄

在您完成此程序來開啟診斷記錄之後，資料可能需要幾分鐘的時間，才會出現在您的記錄中。 如果您一開始沒有看到任何項目，請在幾分鐘後重新檢查一次。

1. 在 Azure 入口網站中，開啟您的防火牆資源群組，然後按一下防火牆。
2. 在 [監視] 底下，按一下 [診斷記錄]。

   針對 Azure 防火牆，可以使用兩個服務特定的記錄：

   * 應用程式規則記錄
   * 網路規則記錄

3. 若要開始收集資料，請按一下 [開啟診斷]。
4. [診斷設定] 頁面中提供診斷記錄的設定。 
5. 在此範例中，Log Analytics 會儲存記錄，因此請輸入**防火牆記錄分析**作為名稱。
6. 按一下 [傳送至 Log Analytics] 來設定您的工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。
7. 在 [Log Analytics] 下，按一下 [設定]。
8. 在 [OMS 工作區] 頁面中，按一下 [建立新的工作區]。
9. 在 [Log Analytics 工作區] 頁面上，輸入 **firewall-oms** 作為新的 **OMS 工作區**名稱。
10. 選取您的訂用帳戶、使用現有的防火牆資源群組 (**Test-FW-RG**)、選取 [美國東部] 作為位置，然後選取 [免費] 定價層。
11. 按一下 [確定]。
   ![啟動設定程序][1]
12. 在 [記錄] 底下，按一下 [AzureFirewallApplicationRule] 和 [AzureFirewallNetworkRule]，以收集應用程式規則和網路規則的記錄。
   ![儲存診斷設定][2]
13. 按一下 [檔案] 。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 啟用記錄

每個 Resource Manager 資源都會自動啟用活動記錄功能。 您必須啟用診斷記錄，才能開始收集可透過這些記錄取得的資料。

若要啟用診斷記錄，請使用下列步驟：

1. 請記下您的儲存體帳戶的資源識別碼 (記錄資料的儲存之處)。 此值的形式為：*/subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Storage/storageAccounts/\<儲存體帳戶名稱\>*。

   您可以使用訂用帳戶中的所有儲存體帳戶。 您可以使用 Azure 入口網站來尋找此資訊。 資訊位於資源的 [屬性] 頁面中。

2. 請記下您防火牆的資源識別碼 (將為其啟用記錄功能)。 此值的形式為：*/subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Network/azureFirewalls/\<防火牆名稱\>*。

   您可以使用入口網站來尋找此資訊。

3. 使用下列 Powershell Cmdlet 啟用診斷記錄功能：

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>診斷記錄不需要個別的儲存體帳戶。 將儲存體用於記錄存取和效能會產生服務費用。

## <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄資料：

* **Azure 工具**：透過 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 入口網站，從活動記錄擷取資訊。 [活動作業與 Resource Manager](../azure-resource-manager/resource-group-audit.md) 一文會詳述每個方法的逐步指示。
* **Power BI**：如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。 使用 [Power BI 的 Azure 活動記錄內容套件](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>檢視及分析網路和應用程式規則記錄

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 會收集計數器和事件記錄檔。 它也納入了視覺效果和強大的搜尋功能來分析您的記錄。

您也可以連接到儲存體帳戶並擷取存取和效能記錄檔的 JSON 記錄檔項目。 下載 JSON 檔案後，可以將它們轉換成 CSV，並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視它們。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。


## <a name="next-steps"></a>後續步驟

既然您已設定防火牆來收集記錄，您可以探索 Log Analytics 以檢視您的資料。

> [!div class="nextstepaction"]
> [Log Analytics 中的網路監視解決方案](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
