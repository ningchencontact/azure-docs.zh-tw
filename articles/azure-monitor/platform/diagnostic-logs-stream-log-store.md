---
title: Azure 監視器中的 Log Analytics 工作區 Stream Azure 診斷記錄
description: 了解如何在 Azure 監視器中的 Log Analytics 工作區的 Azure 診斷記錄串流。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 13eb1a8fcea2f74cda5921a51b8c2e8816be975f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303696"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Azure 監視器中的 Log Analytics 工作區 Stream Azure 診斷記錄

**[Azure 診斷記錄](diagnostic-logs-overview.md)** 的 Log Analytics 工作區，使用入口網站、 PowerShell cmdlet 或 Azure CLI 的 Azure 監視器中近乎即時資料流的處理。

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>您可以使用診斷記錄在 Log Analytics 工作區中

Azure 監視器提供彈性的記錄查詢及分析工具，可讓您深入了解從 Azure 資源產生的原始記錄資料。 一些功能包括：

* **記錄檔查詢**-移轉您的記錄資料，從各種記錄相互關聯來源，並產生圖表，進階的查詢可以釘選到 Azure 儀表板的寫入。
* **警示**-偵測一或多個事件符合特定查詢，並與使用 Azure 監視器警示的電子郵件或 webhook 呼叫收到通知。
* **進階分析** - 套用機器學習和模式比對演算法，以識別出記錄所揭露的可能原因。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>啟用診斷記錄串流至 Log Analytics 工作區

您可以透過入口網站或使用 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)，啟用以程式控制方式對診斷記錄進行串流的功能。 無論使用哪一種方式，您都需建立一個診斷設定，在其中指定 Log Analytics 工作區，以及要傳送至該工作區的記錄類別和計量。 診斷**記錄類別**是一種資源可以提供的記錄類型。

Log Analytics 工作區並不一定要與發出記錄的資源位於相同的訂用帳戶中，只要設定該設定的使用者對兩個訂用帳戶都具備適當的 RBAC 存取權即可。

> [!NOTE]
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> *例如*：可以依據每個佇列層級來瀏覽及繪製「事件中樞」上的「傳入訊息」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用入口網站串流診斷記錄
1. 在入口網站中，瀏覽至 Azure 監視器，然後按一下**diagnostic-settings**中**設定**功能表。


2. 選擇性地依資源群組或資源類型篩選清單，然後按一下您要設定診斷設定的資源。

3. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「開啟診斷」。

   ![新增診斷設定 - 無現有的設定](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   如果資源上已有設定，您將會看此資源上已設定的設定清單。 按一下「新增診斷設定」。

   ![新增診斷設定 - 現有的設定](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 為您的設定提供名稱，並選取 [傳送至 Log Analytics]  核取方塊，然後選取 Log Analytics 工作區。

   ![新增診斷設定 - 現有的設定](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. 按一下 [檔案]  。

過了幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且只要一產生新的事件資料，就會立即將診斷記錄串流至該工作區。 可能會有 15 分鐘的時間之間時就會發出事件，以及當它出現在 Log Analytics 中的位置。

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要透過 [Azure PowerShell Cmdlet](../../azure-monitor/platform/powershell-quickstart-samples.md) 啟用串流功能，您可以使用 `Set-AzDiagnosticSetting` Cmdlet 搭配下列參數︰

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

請注意，workspaceID 屬性會採用工作區的完整 Azure 資源識別碼，而不是採用 Log Analytics 入口網站中顯示的工作區識別碼/金鑰。

### <a name="via-azure-cli"></a>透過 Azure CLI

若要透過 [Azure CLI](../../azure-monitor/platform/cli-samples.md) 啟用串流，您可以使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 命令。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

您可以將字典新增至傳遞作為 `--logs` 參數的 JSON 陣列，從而將其他類別新增至診斷記錄。

僅在 `--workspace` 不是物件識別碼時才需要 `--resource-group` 引數。

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>如何查詢 Log Analytics 工作區中的資料？

Azure 監視器入口網站的 [記錄] 刀鋒視窗，在中，您可以查詢診斷記錄檔記錄管理解決方案，在 [AzureDiagnostics] 資料表的一部分。 另外還有[數個適用於 Azure 資源的監視解決方案](../../azure-monitor/insights/solutions.md)取得即時深入解析記錄資料傳送至 「 Azure 監視器，您可以安裝。

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure 診斷與特定資源  
一旦 Log Analytics 目的地啟用 Azure 診斷組態中，有兩個不同的方式，資料會顯示在您的工作區：  
- **Azure 診斷**-這是由大多數 Azure 服務目前使用的舊方法。 在此模式中，所有指向指定的工作區的任何診斷設定中的資料將會得到_AzureDiagnostics_資料表。 
<br><br>因為許多資源將資料傳送至相同的資料表 (_AzureDiagnostics_)，此資料表的結構描述是超級組所收集的所有不同的資料類型的結構描述。 例如，如果您已經建立下列資料類型的集合的診斷設定，所有傳送至相同的工作區：
    - 稽核記錄檔的資源 1 （具有 A、 B 和 C 資料行所組成的結構描述）  
    - （具有資料行 D、 E 和 F 所組成的結構描述） 的資源 2 的錯誤記錄檔  
    - 為資源 3 （具有資料行 G，H，和我所組成的結構描述） 的資料流量記錄  

    [AzureDiagnostics] 資料表會以特定範例資料，如下所示，看起來：  

    | ResourceProvider | 類別 | 具有使用 | b | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **資源特定**-在此模式中，選取工作區中的個別資料表會建立每個選取的診斷設定組態中每個類別。 這個較新的方法可更容易尋找完全是您想要尋找透過明確的關注點分離： 每個類別目錄的資料表。 此外，還會提供其支援的優點為動態型別。 您已看出此模式中的選取 Azure 資源類型，例如[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)或是[Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor)記錄檔。 最後，我們會預期每個資料類型，來移轉至特定資源的模式。 

    在上述範例中，這會導致所建立的三個資料表： 
    - 表格_AuditLogs_ ，如下所示：

        | ResourceProvider | 類別 | 具有使用 | b | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - 表格_ErrorLogs_ ，如下所示：  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - 表格_DataFlowLogs_ ，如下所示：  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    使用的資源特定模式的其他優點包括更佳的效能在擷取延遲和查詢時間，探索起來更加方便，結構描述和其結構、 能夠授與特定資料表，以及更多的 RBAC 權限。

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>選取 Azure 診斷與特定資源的模式
針對大部分的 Azure 資源，您將不可以選擇是否要使用的 Azure 診斷或特定資源的模式;資料會自動將會流經此資源已選取要使用的方法。 請參閱您已將資料傳送至 Log Analytics，如需詳細資訊，運用模式啟用的資源所提供的文件。 

上一節所述，最終目標是 Azure 監視器將所有服務在使用 Azure 資源特定模式。 若要簡化這項轉換，並確保沒有資料遺失，在某些 Azure 服務上的架至 Log Analytics 會提供您選取的模式時，它的：  
   ![診斷設定模式選取器](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

我們**強**建議，以避免潛在困難的移轉之後，任何新建立的診斷設定使用的資源中心的模式。  

現有的診斷設定，一次啟用特定的 Azure 資源，您將能夠追溯切換至特定資源模式的 從 Azure 診斷。 您先前擷取的資料仍可用於_AzureDiagnostics_資料表直到它如您在工作區中的保留設定中設定過期，但是任何新的資料會傳送至專用的資料表。 會針對任何查詢，這表示有跨這兩種舊的資料和新 （直到過了舊資料完全過期），則[聯集](https://docs.microsoft.com/azure/kusto/query/unionoperator)將需要在您的查詢運算子結合兩個資料集。

請尋找新的 Azure 相關新聞上的服務中的資源特定模式的支援記錄檔[Azure 更新](https://azure.microsoft.com/updates/)部落格 ！

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>已知限制： AzureDiagnostics 中的資料行限制
沒有任何指定的 Azure 記錄檔資料表，不需要超過 500 個資料行的明確限制。 一旦達到，任何包含資料的前 500 個以外的任何資料行的資料列皆會予以捨棄在擷取階段。 [AzureDiagnostics] 資料表是特別容易受到要影響這項限制。 這通常是因為各種資料來源傳送到相同的工作區，或多個傳送至相同的工作區的詳細資訊的資料來源。 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory，因為一組非常詳細的記錄檔，而是特別會受到這項限制已知的資源。 特別的是，任何特定資源之前，設定的診斷設定的模式已啟用或明確地選擇要用於反向相容性的原因的特定資源的模式：  
- *針對您的管線中的任何活動所定義的使用者參數*： 會為每個唯一命名的使用者參數，針對任何活動建立新的資料行。 
- *活動的輸入和輸出*： 這些而有所不同，活動間產生大量的資料行，因為其詳細資訊的性質。 
 
做為更廣泛的因應措施提案，建議將您的記錄檔，以儘速使用特定資源的模式。 如果您無法立即執行，過渡期的替代方式是將 ADF 記錄檔隔離至其自己的工作區，以影響您工作區中收集其他記錄類型的這些記錄檔的機會降到最低。 
 
#### <a name="workarounds"></a>因應措施
就短期來說，直到不啟用任何服務的特定資源的模式，在所有 Azure 服務，但支援的特定資源的模式，建議您區隔的詳細資料型別由這些服務發佈至不同的工作區，以減少可能會達到的限制。  
 
就長期來說，Azure 診斷會採用支援的資源特定模式的所有 Azure 服務。 我們建議儘速以減少可能會受到此 500 的資料行的限制此模式移動。  


## <a name="next-steps"></a>後續步驟

* [深入了解 Azure 診斷記錄檔](diagnostic-logs-overview.md)

