---
title: 如何使用 Windows 的 Log Analytics 代理程式的問題進行疑難排解 |Microsoft Docs
description: 說明 Log Analytics Linux 代理程式最常見問題的徵兆、原因和解決方法。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120106"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何針對 Log Analytics Linux 代理程式的問題進行疑難排解 

此文章提供的說明可協助您針對 Log Analytics Linux 代理程式的錯誤進行疑難排解，並建議可解決問題的解決方法。

如果這些步驟對您都沒有幫助，還有下列支援管道可供使用：

* 具有頂級支援權益的客戶可以透過[頂級支援](https://premier.microsoft.com/)開啟支援要求。
* 具有 Azure 支援合約的客戶可以在 [Azure 入口網站](https://manage.windowsazure.com/?getsupport=true)開啟支援要求。
* 造訪「Log Analytics 意見反應」頁面，以檢閱已提交的構想和錯誤[https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)，或提出新的構想和錯誤。 

## <a name="important-troubleshooting-sources"></a>重要的疑難排解來源

 若要協助與 Windows 的 Log Analytics 代理程式相關的問題進行疑難排解，代理程式事件記錄至 Windows 事件記錄檔中，特別是在*應用程式及 Services\Operations Manager*。  

## <a name="connectivity-issues"></a>連線能力問題

如果代理程式透過 proxy 伺服器或防火牆進行通訊，可能會有就地禁止從來源電腦 」 和 「 Azure 監視器服務進行通訊的限制。 如果通訊遭到封鎖，設定不正確，與工作區註冊可能會失敗時嘗試安裝代理程式，設定代理程式安裝後以回報至額外的工作區，或在成功註冊之後的代理程式通訊失敗。 本節描述針對這種類型的 Windows 代理程式問題進行疑難排解的方法。 

再次確認防火牆或 proxy 已設定為允許下列連接埠和下表中所述的 Url。 另請確認 HTTP 檢查未啟用 web 流量，因為它可以防止代理程式與 Azure 監視器之間 TLS 安全通道。  

|代理程式資源|連接埠 |Direction |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  
|*.azure-automation.net |連接埠 443 |輸出|是 |  

Azure Government 所需的防火牆資訊，請參閱[Azure Government 管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

有數種方式，您可以確認代理程式會成功地與 Azure 監視器中通訊。

- 啟用[Azure Log Analytics 代理程式健全狀況評估](../insights/solution-agenthealth.md)工作區中。 從代理程式健全狀況儀表板，檢視**沒有回應的代理程式的計數**快速查看 代理程式是否列出的資料行。  

- 執行下列查詢，以確認代理程式會將活動訊號傳送到它已設定為 [報表] 工作區。 取代<ComputerName>機器的實際名稱。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果與服務成功通訊的電腦，則查詢應傳回結果。 如果查詢未傳回結果，先確認代理程式設定為正確的工作區的報表。 如果設定正確，請繼續進行步驟 3，並搜尋 Windows 事件記錄檔，進而識別是否有代理程式會記錄哪些問題可能會造成它無法與 Azure 監視器通訊。

- 若要找出連線問題的另一個方法是藉由執行**TestCloudConnectivity**工具。 此工具會安裝代理程式資料夾中的預設 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*。 從提升權限的命令提示字元中，瀏覽至資料夾，並執行此工具。 工具會傳回結果，並反白顯示測試失敗 （例如，如果它與特定的連接埠/URL 已遭封鎖而） 的位置。 

    ![TestCloudConnection 工具執行結果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 篩選條件*Operations Manager*事件記錄檔所**事件來源** - *健全狀況服務模組*， *HealthService*，及*Service Connector* ，並依**事件層級***警告*並*錯誤*確認如果它已寫入的事件下表。 如有需要，請檢閱包含針對每個可能的事件的解決步驟。

    |事件識別碼 |source |描述 |解決方案 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |健全狀況服務 |從代理程式無法連線到服務 |代理程式無法在直接或透過防火牆/proxy 伺服器與 Azure 監視器服務通訊時，會發生此錯誤。 請確認代理程式 proxy 設定或網路防火牆/proxy 可讓電腦從服務的 TCP 流量。|
    |2138 |健全狀況服務模組 |Proxy 需要驗證 |設定代理程式 proxy 設定，並指定使用 proxy 伺服器進行驗證所需的使用者名稱/密碼。 |
    |2129 |健全狀況服務模組 |無法連接/失敗 SSL 交涉 |檢查您的網路介面卡 TCP/IP 設定與代理程式 proxy 設定。|
    |2127 |健全狀況服務模組 |傳送資料失敗的收到的錯誤碼 |如果是它只會在一天內會定期發生，可能只是隨機的異常可以略過。 若要了解發生的頻率發生的監視。 如果全天經常發生，請先檢查您的網路組態和 proxy 設定。 如果描述包含 HTTP 錯誤碼 404，而且是第一次代理程式會嘗試將資料傳送至服務，它會包含內部 404 錯誤碼 500 錯誤。 404 代表找不到，這表示，新的工作區的儲存體區域仍在佈建。 下一步 重試時，資料會成功地寫入至工作區如預期般運作。 HTTP 錯誤 403 可能表示權限或認證問題。 沒有隨附 403 錯誤，以協助疑難排解此問題的詳細資訊。|
    |4000 |服務連接器 |DNS 名稱解析失敗 |電腦無法解析時將資料傳送至服務所使用的網際網路位址。 這可能是在您電腦中，不正確的 proxy 設定或可能是暫時性的 DNS 問題 」 與您的提供者中的 DNS 解析程式設定。 如果定期發生，它可能是暫時性的網路相關問題所引起。|
    |4001 |服務連接器 |連接到服務失敗。 |代理程式無法在直接或透過防火牆/proxy 伺服器與 Azure 監視器服務通訊時，會發生此錯誤。 請確認代理程式 proxy 設定或網路防火牆/proxy 可讓電腦從服務的 TCP 流量。|
    |4002 |服務連接器 |服務會傳回 HTTP 狀態碼 403 以回應查詢。 請洽詢服務管理員以服務的健全狀況。 稍後將重試查詢。 |此錯誤會寫入代理程式的初始註冊階段期間，您會看到類似下列的 URL: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*。 錯誤的程式碼 403 禁止的方式，並可能因輸入錯誤的工作區識別碼或索引鍵，或日期和時間不正確的電腦上。 如果時間為自目前時間起的 + /-15 分鐘，則上架失敗。 若要修正此問題，請更新 Linux 伺服器的日期和/或時區。|

## <a name="data-collection-issues"></a>資料收集問題

之後安裝的代理程式，並對其設定的工作區或工作區的報表，它可能會停止接收設定、 收集或轉送效能、 記錄檔或其他資料，以根據啟用哪些服務和目標電腦。 就必須判斷：

- 是特定的資料類型或不在工作區中的所有資料？
- 這是資料類型為解決方案所指定，或指定為工作區的資料收集組態的一部分嗎？
- 多少部電腦受到影響？ 是單一或多個工作區報告的電腦？
- 它的運作和它停止在特定一天的時間，或它從未收集？ 
- 是您使用記錄搜尋查詢的語法是否正確？ 
- 代理程式不斷收到其設定 Azure 監視器？

疑難排解的第一個步驟是判斷是否電腦正在傳送活動訊號事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查詢未傳回結果，您需要判斷特定資料類型是否不收集以及轉送給服務。 這可能是代理程式不接收更新的組態伺服器或防止代理程式正常運作的其他徵兆所造成。 執行下列步驟，以進一步排解疑難。

1. 開啟提升權限的命令提示字元，在電腦上，並重新啟動代理程式服務輸入`net stop healthservice && net start healthservice`。
2. 開啟*Operations Manager*事件記錄檔，並搜尋**事件識別碼** *7023、 7024、 7025、 7028*並*1210年*從**事件來源** *HealthService*。  這些事件表示代理程式正在成功接收從 「 Azure 監視器的組態，並且它們會主動監視的電腦。 事件識別碼 1210; 該也會在最後一個指定的事件描述線條的所有方案和監視代理程式上的範圍中包含的深入解析。  

    ![事件識別碼 1210; 該描述](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 如果在數分鐘後您看不預期的資料，在 查詢結果 或 視覺效果中，視您正在檢視的資料解決方案或深入解析，從*Operations Manager*事件記錄檔中，搜尋**事件來源** *HealthService*並*健全狀況服務模組*篩選**事件層級***警告*和*錯誤*確認如果它具有下表中，寫入事件。

    |事件識別碼 |source |描述 |解決方案 |
    |---------|-------|------------|
    |8000 |HealthService |此事件會指定工作流程與效能、 事件或收集其他資料型別是無法轉送至擷取到工作區的服務。 | 事件識別碼 2136 來源 HealthService 與此事件會寫入，且可能表示代理程式而無法使用服務時，可能是因為設定不正確的 proxy 和驗證設定、 網路中斷或網路防火牆的通訊 /proxy 不允許從電腦服務的 TCP 流量。| 
    |10102 和 10103 |健全狀況服務模組 |工作流程 nelze přeložit 資料來源。 |如果指定的效能計數器或執行個體不存在的電腦上，或定義不正確的工作區資料的設定中，會發生這項目。 如果這是使用者指定[效能計數器](data-sources-performance-counters.md#configuring-performance-counters)、 驗證指定的資訊會遵循正確的格式和存在於目標電腦上。 |
    |26002 |健全狀況服務模組 |工作流程 nelze přeložit 資料來源。 |如果指定的 Windows 事件記錄檔不存在的電腦上，也可能會發生。 此錯誤，可安全地忽略電腦不應該有此註冊，否則如果這是使用者指定的事件記錄檔[事件記錄檔](data-sources-windows-events.md#configuring-windows-event-logs)，確認指定的資訊正確無誤。 |

