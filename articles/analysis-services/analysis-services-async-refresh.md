---
title: "非同步的重新整理 Azure Analysis Services 模型 |Microsoft 文件"
description: "了解如何使用 REST API 的程式碼非同步的重新整理。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/18/2017
ms.author: owend
ms.openlocfilehash: 06d807b83f700c675c6979998dd8f74372a4845f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>非同步的重新整理使用 REST API
藉由使用任何支援 REST 呼叫的程式設計語言，您可以執行您的 Azure Analysis Services 表格式模型上的非同步資料重新整理作業。 這包括向外延展查詢的唯讀複本的同步處理。 

資料重新整理作業可能需要一些時間，視因素，包括資料磁碟區，請使用資料分割等的最佳化層級數目。這些作業有傳統上用來叫用現有的方法，例如使用[TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) （表格式物件模型）， [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) cmdlet，或[TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) （表格式模型指令碼語言）。 不過，這些方法可以要求通常不可靠，長時間執行的 HTTP 連線。

Azure Analysis Services 的 REST API 可讓資料重新整理作業會以非同步方式執行。 藉由使用 REST API，從用戶端應用程式的長時間執行 HTTP 連線時並非必要。 另外還有其他內建功能的可靠性，例如自動重試次數和批次的認可。

## <a name="base-url"></a>基底 URL

基底 URL 會遵循下列格式：

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

例如，名為 AdventureWorks 的模型，在名為 myserver，位於 West US Azure 地區，伺服器上的伺服器名稱是：

```
asazure://westus.asazure.windows.net/myserver 
```

此伺服器名稱的基底 URL 是：

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

藉由使用基底 URL，資源和作業可以附加根據下列： 

![非同步的重新整理](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- 任何結尾中的項目**s**是集合。
- 任何結尾的項目**（)**是函式。
- 任何其他項目是資源/物件。

例如，您可以使用 POST 動詞命令重新整理集合上執行重新整理作業，就像這樣：

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>驗證

所有呼叫必須使用授權標頭中的有效 Azure Active Directory (OAuth 2) 權杖進行驗證，而且必須符合下列需求：

- 語彙基元必須是使用者語彙基元或應用程式服務主體。
- 使用者或應用程式必須有足夠權限的伺服器或模型來執行要求的呼叫。 權限層級取決於模型或伺服器上的系統管理員群組中的角色。
- 語彙基元，必須設定為正確的對象`https://*.asazure.windows.net`。

## <a name="post-refreshes"></a>POST /refreshes

若要執行重新整理作業時，使用 POST 動詞 /refreshes 集合上將新的重新整理項目加入至集合。 位置標頭在回應中的包含重新整理識別碼。 用戶端應用程式可以中斷連線，並稍後再檢查狀態，如果必要的因為這是非同步的。

只有一個重新整理作業會接受模型一次。 如果沒有目前的執行重新整理作業，另一個送出，則會傳回 409 衝突 HTTP 狀態碼。

主體可能如下所示：

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>參數
指定參數就不需要。 預設會套用。

|名稱  |類型  |說明  |預設值  |
|---------|---------|---------|---------|
|類型     |  例舉       |  要執行的處理類型。 與 TMSL 對齊類型[重新整理命令](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl)類型： 完整、 clearValues、 計算，自動 dataOnly、 加上重組。       |   自動      |
|CommitMode     |  例舉       |  決定是否物件將會認可批次，或完成時。 模式包括： 預設情況下，交易式 partialBatch。  |  交易式       |
|MaxParallelism     |   int      |  這個值會決定要以平行方式執行處理命令的執行緒數目上限。 這與您可以將 TMSL MaxParallelism 屬性對齊[順序命令](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl)或使用其他方法。       | 10        |
|RetryCount    |    int     |   表示作業失敗之前重試的次數。      |     0    |
|物件     |   陣列      |   要處理的物件陣列。 每個物件包含: 「 資料表 」 處理整份資料表或 「 資料表 」 和 「 分割 」 時處理資料分割時。 如果未指定物件，會重新整理整個模型。 |   處理整個模型      |

CommitMode 等於 partialBatch。 它用於執行大型資料集的初始載入需要數小時。 如果已成功認可的一或多個批次之後，重新整理作業失敗，成功認可的批次會保留認可 （它無法回復已成功認可的批次）。

> [!NOTE]
> 在撰寫本文時，批次大小是 MaxParallelism 值，但是無法變更此值。

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId >

若要檢查的重新整理作業的狀態，請使用 GET 動詞命令對重新整理識別碼 以下是範例回應主體。 如果作業正在進行中， **inProgress**傳回狀態。

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>取得 /refreshes

若要取得之模型的歷程記錄的重新整理作業的清單，請使用 GET 動詞命令 /refreshes 集合上。 以下是範例回應主體。 

> [!NOTE]
> 在撰寫本文時，會儲存重新整理作業在過去 30 天內，並傳回，但無法變更此數字。

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>刪除 /refreshes/\<refreshId >

若要取消進行中重新整理作業，使用 DELETE 動詞命令重新整理 id。

## <a name="post-sync"></a>POST /sync

需要執行重新整理作業，可能需要向外延展查詢的複本與同步處理新的資料。若要執行同步處理作業的模型時，使用 POST 動詞命令上 /sync 函式。 位置標頭在回應中的包括同步處理作業識別碼。

## <a name="get-sync-status"></a>取得 /sync 狀態

若要檢查的同步處理作業的狀態，請使用 GET 動詞命令作為參數傳遞作業識別碼。 回應主體的範例如下：

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Syncstate 的值：

- 0： 複寫。 資料庫檔案會被複寫到目標資料夾。
- 1： 重新水合已。 資料庫是唯讀的伺服器執行個體上被凍結。
- 2： 完成。 已成功完成同步處理作業。
- 3： 失敗。 同步處理作業失敗。
- 4： 正在結束。 同步處理作業已完成，但會執行清除步驟。

## <a name="code-sample"></a>程式碼範例

以下是 C# 程式碼範例可協助您開始， [GitHub 上的 RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample)。

### <a name="to-use-the-code-sample"></a>若要使用程式碼範例

1.  要複製或下載儲存機制。 開啟 RestApiSample 方案。
2.  找到行**用戶端。BaseAddress =...** 並提供您[基底 URL](#base-url)。

程式碼範例可以使用互動式登入、 使用者名稱/密碼，或[服務主體](#service-principle)。

#### <a name="interactive-login-or-usernamepassword"></a>互動式登入或使用者名稱/密碼

這種形式的驗證需要 Azure 應用程式建立必要的 API 權限指派。 

1.  在 Azure 入口網站中，按一下**新增** > **Azure Active Directory** > **應用程式註冊** > **新增應用程式註冊**。

    ![新的應用程式註冊](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  在**建立**、 輸入的名稱、 選取**原生**應用程式類型。 如**重新導向 URI**，輸入**urn: ietf:wg:oauth:2.0:oob**，然後按一下 **建立**。

    ![設定](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  選取您的應用程式複製並儲存**應用程式識別碼**。

    ![複製應用程式識別碼](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  在**設定**，按一下 **必要的權限** > **新增**。

    ![加入 API 存取權](./media/analysis-services-async-refresh/aas-async-add.png)

5.  在**選取應用程式開發介面**，型別**SQL Server Analysis Services**至搜尋方塊，然後選取**Azure Analysis Services (SQL Server Analysis Services Azure)**。

    ![精選 API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  選取**讀取和寫入所有模型**，然後按一下 **選取**。 當兩者皆選取時，按一下 **完成**將權限。 可能需要幾分鐘才能傳播。

    ![選取 讀取和寫入所有模型](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  在程式碼範例中，尋找**UpdateToken()**方法。 請注意此方法的內容。
8.  尋找**字串 clientID =...**，然後輸入**應用程式識別碼**從步驟 3 中複製。
9.  執行範例。

#### <a name="service-principal"></a>服務主體

請參閱[自動化 Azure Analysis Services 的服務主體與 PowerShell](https://azure.microsoft.com/blog/automation-of-azure-analysis-services-with-service-principals-and-powershell/)部落格文章說明如何設定服務主體，並指派 Azure Analysis Services 中的必要權限。 當您完成之後部落格文章中詳述的步驟時，完成下列額外步驟：

1.  在程式碼範例中，尋找**字串授權單位 =...**，取代**常見**與貴組織的租用戶識別碼。
2.  註解/取消註解以便 ClientCredential 類別來具現化認證物件。 請確定\<應用程式識別碼 > 和\<應用程式金鑰 > 值目前存取安全的方式或使用憑證型驗證的服務主體。
3.  執行範例。


## <a name="see-also"></a>請參閱

[範例](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


