---
title: Azure Analysis Services 擴充 | Microsoft Docs
description: 使用擴充功能複寫 Azure Analysis Services 伺服器
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c226608f6c1c776463aa05c02b1d3cc04b699ec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766821"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

通过横向扩展，客户端查询可分布在查询池的多个查询副本中，从而在高查询工作负载期间缩短响应时间。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。 所有查詢複本都會建立在與伺服器相同的區域內。 您可以設定的查詢複本數目受限於您伺服器所在的區域。 若要深入了解，請參閱[依區域的可用性](analysis-services-overview.md#availability-by-region)。 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。 

## <a name="why-scale-out"></a>为何要横向扩展？

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

使用向外延展，您可以建立最多七個額外查詢複本資源的查詢集區 (八個總計，包括您*主要*伺服器)。 可以缩放查询池中的副本数目以满足关键时刻的 QPU 需求，并且随时可以从查询池中隔离处理服务器。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 主服务器充当处理服务器。 查询副本仅为针对查询池中主服务器与每个副本之间同步的模型数据库发出的查询提供服务。 

横向扩展时，最长可能需要花费五分钟时间来以增量方式将新的查询副本添加到查询池。 所有新的查询副本正常运行后，新的客户端连接将在查询池中的资源之间进行负载均衡。 現有的用戶端連線仍會連接到目前的資源，而不會變更。 在相應縮小時，若有用戶端連線至要從查詢集區中移除的查詢集區資源，其現有的連線一律會終止。 客户端可以重新连接到剩余的查询池资源。

## <a name="how-it-works"></a>運作方式

首次配置横向扩展时，主服务器上的模型数据库将自动与新查询池中的新副本同步。 自动同步只发生一次。 在自动同步期间，主服务器的数据文件（在 Blob 存储中静态加密）将复制到另一个位置，并且也会在 Blob 存储中静态加密。 然后，查询池中的副本将与第二组文件中的数据合成。 

只能在首次横向扩展服务器时执行自动同步，不过，也可以执行手动同步。 同步可确保查询池中的副本数据与主服务器的数据相匹配。 处理（刷新）主服务器上的模型时，必须在处理操作完成之后执行同步。 这种同步会将更新的数据从 Blob 存储中的主服务器文件复制到第二组文件。 然后，查询池中的副本将与 Blob 存储中第二组文件内的已更新数据合成。 

执行后续的横向扩展操作时（例如，将查询池中的副本数从两个增加到五个），新副本将与 Blob 存储中第二组文件内的数据合成。 不会发生同步。 如果在横向扩展后执行同步，则查询池中的新副本将合成两次 - 多余的合成。 执行后续的横向扩展操作时，请务必记住：

* 先执行同步，再执行横向扩展操作，以免多余地合成添加的副本。

* 将处理操作和横向扩展操作自动化时，必须先处理主服务器上的数据，再执行同步，然后执行横向扩展操作。 遵循此顺序可确保尽量减轻对 QPU 和内存资源造成的影响。

* 即使查询池中没有副本，也允许同步。 如果在主服务器上通过处理操作将包含新数据的副本数从零个横向扩展为一个或多个，请先在查询池中不包含任何副本的情况下执行同步，然后再横向扩展。在横向扩展之前执行同步可以避免多余地合成新添加的副本。

* 从主服务器中删除模型数据库时，不会自动从查询池中的副本内删除该数据库。 必须使用 [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell 命令执行同步操作。该命令会从副本的共享 Blob 存储位置删除该数据库的文件，然后删除查询池中的副本内的模型数据库。

* 重命名主服务器上的数据库时，需要执行一个额外的步骤来确保数据库正确同步到所有副本。 重命名后，使用 [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) 并使用旧数据库名称指定 `-Database` 参数，来执行同步。 这种同步会从所有副本中删除使用旧名称的数据库和文件。 然后，使用新数据库名称指定 `-Database` 参数，来执行另一次同步。 第二次同步会将新命名的数据库复制到第二组文件，并合成所有副本。 无法在门户中使用“同步模型”命令执行这些同步。

### <a name="separate-processing-from-query-pool"></a>與查詢集區分開處理

若要讓處理和查詢作業達到最佳效能，您可以選擇將處理伺服器和查詢集區區隔開來。 區隔之後，現有和新的用戶端連線都只會指派給查詢集區中的查詢複本。 如果處理作業短時間即可完成，您可以選擇只在執行處理和同步處理作業的期間內區隔處理伺服器與查詢集區，且隨後就將它重新納入查詢集區中。 

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

若要判斷您的伺服器是否需要擴充，請使用計量在 Azure 入口網站中監視您的伺服器。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 

可监视的另一个很好指标是按 ServerResourceType 列出的平均 QPU。 此指标将主服务器的平均 QPU 与查询池的平均 QPU 进行比较。 

### <a name="to-configure-qpu-by-serverresourcetype"></a>按 ServerResourceType 配置 QPU
1. 在“指标”折线图中，单击“添加指标”。 
2. 在“资源”中选择你的服务器，在“指标命名空间”中选择“Analysis Services 标准指标”，在“指标”中选择“QPU”，然后在“聚合”中选择“平均”。 
3. 单击“应用拆分”。 
4. 在“值”中选择“ServerResourceType”。  

若要深入了解，請參閱[監視伺服器計量](analysis-services-monitor.md)。

## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在入口網站中，按一下 [擴充]。使用滑桿選取查詢複本伺服器的數目。 所選擇的複本數目不包括現有的伺服器。

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\) 中，選取 [是] 以將處理伺服器從查詢伺服器排除。 使用默认连接字符串（不带 `:rw`）的客户端[连接](#connections)将重定向到查询池中的副本。 

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存] 以佈建您的新查詢複本伺服器。 

首次配置服务器的横向扩展时，主服务器上的模型将自动与查询池中的副本同步。 自动同步只发生一次，即，首次配置横向扩展为一个或多个副本时。 以后对同一台服务器上的副本数进行更改不会再次触发自动同步。 即使将服务器设置为零个副本，然后再次横向扩展为任意数目的副本，也不会再次发生自动同步。 

## <a name="synchronize"></a>同步 

必须手动或使用 REST API 执行同步操作。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀] > [模型] > [同步處理模型] 中。

![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

返回状态代码：


|代碼  |描述  |
|---------|---------|
|-1     |  無效       |
|0     | 正在复制        |
|1     |  正在解冻       |
|2     |   Completed       |
|3     |   Failed      |
|4     |    正在完成     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在使用 PowerShell 之前，请[安装或更新最新的 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 

若要运行同步，请使用 [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

若要设置查询副本数，请使用 [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性的 `-ReadonlyReplicaCount` 參數。

若要从查询池隔离处理服务器，请使用 [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 将 `-DefaultConnectionMode` 可选参数指定为使用 `Readonly`。

若要進一步了解，請參閱[Az.AnalysisServices 模組搭配使用服務主體](analysis-services-service-principal.md#azmodule)。

## <a name="connections"></a>連線

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

若為使用者用戶端連線 (如 Power BI Desktop、Excel 以及自訂應用程式)，請使用**伺服器名稱**。 

若為 SSMS、SSDT，以及 PowerShell、Azure 函數應用程式和 AMO 中的連接字串，請使用**管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有处理操作均在管理服务器（主服务器）上发生。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>疑難排解

**問題：** 使用者收到錯誤 [在連線模式 'ReadOnly' 中，找不到伺服器 '\<伺服器名稱>' 執行個體。]

**解決方案：** 选择“从查询池隔离处理服务器”选项时，使用默认连接字符串（不带 `:rw`）的客户端连接将重定向到查询池副本。 如果查詢集區中的複本因為尚未完成同步處理而未上線，則重新導向的用戶端連線可能會失敗。 若要避免連線失敗，則在執行同步處理時，查詢集區中必須有至少兩部伺服器。 每部伺服器會個別同步，而其他伺服器則維持線上狀態。 如果您選擇在處理期間查詢集區中不要有處理中的伺服器，可以選擇從集區中移除該伺服器以供處理，然後在處理完成之後，但在同步處理之前，將它加回集區。 使用記憶體和 QPU 計量來監視同步處理狀態。

## <a name="related-information"></a>相關資訊

[監視伺服器計量](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 
