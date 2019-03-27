---
title: Azure Analysis Services 擴充 | Microsoft Docs
description: 使用擴充功能複寫 Azure Analysis Services 伺服器
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6a69d8d60b2e588ded9ccca20521195ae11ff136
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449429"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

使用向外延展，用戶端查詢可以分散到多個*查詢複本*中*查詢集區*，縮短高查詢工作負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。 所有查詢複本都會建立在與伺服器相同的區域內。 您可以設定的查詢複本數目受限於您伺服器所在的區域。 若要深入了解，請參閱[依區域的可用性](analysis-services-overview.md#availability-by-region)。 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。 

## <a name="why-scale-out"></a>為什麼向外延展？

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

使用向外延展，您可以建立最多七個額外查詢複本資源的查詢集區 (八個總計，包括您*主要*伺服器)。 您可以調整以符合關鍵時刻的 QPU 需求查詢集區中的複本數目，您可以在任何時間分隔將處理伺服器與查詢集區。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 主要伺服器做為處理伺服器。 查詢複本服務只能針對主要伺服器與查詢集區中的每個複本之間同步處理的模型資料庫的查詢。 

當相應放大，可能需要五分鐘的時間以累加方式新增至查詢集區的新查詢複本。 當所有新的查詢複本都已啟動並執行，新的用戶端連線進行負載平衡所有查詢集區中的資源。 現有的用戶端連線仍會連接到目前的資源，而不會變更。 在相應縮小時，若有用戶端連線至要從查詢集區中移除的查詢集區資源，其現有的連線一律會終止。 用戶端可以重新連線到其餘的查詢集區資源。

## <a name="how-it-works"></a>運作方式

設定向外延展第一次，主要伺服器上的模型資料庫時*自動*與新的查詢集區中的新複本進行同步處理。 自動同步處理只出現一次。 自動同步處理期間，會複製到第二個位置，也會在 blob 儲存體中的待用加密的主要伺服器的資料檔案 （blob 儲存體中靜止加密）。 查詢集區中的複本就*會提供*第二組的檔案中的資料。 

只有當您向外延展伺服器第一次執行時自動同步處理，而您也可以執行手動同步處理。 同步處理，可確保在查詢集區中的複本上的資料，使其符合主要伺服器。 在處理 （重新整理） 模型，在主要伺服器上的時，必須執行同步處理*之後*完成處理作業。 這項同步處理會將更新的資料從 blob 儲存體中的主要伺服器的檔案複製到第二組的檔案。 查詢集區中的複本會接著會提供 blob 儲存體中檔案的第二組的更新資料。 

執行後續的向外延展作業時，比方說，從 2 到 5，查詢集區中的複本數目增加新的複本會會提供第二組的 blob 儲存體中的檔案中的資料。 沒有任何同步處理。 如果您要再執行同步處理之後會相應放大，在查詢集區中的新複本會提供兩次： 備援的序列化。 執行後續的向外延展作業時，務必要牢記在心：

* 執行同步處理*向外延展作業之前*以避免加入複本的備援序列化。

* 自動化這兩個處理時*和*相應放大作業，請務必先處理資料的主要伺服器，然後執行同步處理，然後執行向外延展作業。 此順序可確保在 QPU 及記憶體資源的影響降到最低。

* 即使在查詢集區中都沒有複本時，才允許同步處理。 如果您從零到一或多個複本，以新的資料從主要伺服器上的處理作業相應放大，第一次執行與查詢集區中的任何複本的同步處理，然後向外延展。同步處理之前向外擴充，可避免備援序列化的新加入的複本。

* 從主要伺服器中刪除的模型資料庫，它不會不會自動取得從刪除查詢集區中的複本。 您必須使用來執行同步處理作業[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell 命令移除該資料庫的檔案/秒從複本共用的 blob 儲存體位置，然後刪除模型在查詢集區中的複本上的資料庫。

* 重新命名時的主要伺服器上的資料庫，但沒有額外的步驟，必須確保資料庫正確同步處理至任何複本。 在重新命名之後，請使用執行同步處理[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)命令，並指定`-Database`使用舊的資料庫名稱的參數。 這項同步處理將移除任何複本的資料庫與舊名稱的檔案。 然後執行 另一個同步處理指定`-Database`與新的資料庫名稱的參數。 第二個同步處理會將新命名的資料庫複製到第二組的檔案，並會產生任何複本。 無法使用入口網站中的同步處理模型命令執行這些同步處理。

### <a name="separate-processing-from-query-pool"></a>與查詢集區分開處理

若要讓處理和查詢作業達到最佳效能，您可以選擇將處理伺服器和查詢集區區隔開來。 區隔之後，現有和新的用戶端連線都只會指派給查詢集區中的查詢複本。 如果處理作業短時間即可完成，您可以選擇只在執行處理和同步處理作業的期間內區隔處理伺服器與查詢集區，且隨後就將它重新納入查詢集區中。 

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

若要判斷您的伺服器是否需要擴充，請使用計量在 Azure 入口網站中監視您的伺服器。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 

要監看另一個很好的計量是由 ServerResourceType 平均 QPU。 此計量會比較的查詢集區的主要伺服器的平均 QPU。 

### <a name="to-configure-qpu-by-serverresourcetype"></a>若要設定透過 ServerResourceType QPU
1. 在 計量折線圖中，按一下 **新增計量**。 
2. 在**資源**，選取您的伺服器，然後在**度量命名空間**，選取**Analysis Services 的標準計量**，然後在**計量**，選取  **QPU**，然後在**彙總**，選取**Avg**。 
3. 按一下 **適用於分割**。 
4. 在 **值**，選取**ServerResourceType**。  

若要深入了解，請參閱[監視伺服器計量](analysis-services-monitor.md)。

## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在入口網站中，按一下 [擴充]。使用滑桿選取查詢複本伺服器的數目。 所選擇的複本數目不包括現有的伺服器。

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\) 中，選取 [是] 以將處理伺服器從查詢伺服器排除。 用戶端[連線](#connections)使用的預設連接字串 (不含`:rw`) 會被重新導向查詢集區中的複本。 

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存] 以佈建您的新查詢複本伺服器。 

當設定向外延展伺服器第一次，主要伺服器上的模型會自動同步處理的查詢集區中的複本。 一次，當您第一次設定向外延展至一或多個複本，才會發生自動同步處理。 在相同伺服器上的複本數目的後續變更*不會觸發另一個自動同步處理*。 即使您設為零的複本，然後再向外延展至任意數目的複本伺服器，不會再次發生自動同步處理。 

## <a name="synchronize"></a>同步化 

必須執行同步處理作業，以手動方式或使用 REST API。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀] > [模型] > [同步處理模型] 中。

![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

會傳回狀態碼：


|代碼  |描述  |
|---------|---------|
|-1     |  無效       |
|0     | 正在複寫        |
|1     |  解除凍結       |
|2     |   Completed       |
|3     |   Failed      |
|4     |    正在完成     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 PowerShell 之前,[安裝或更新的最新的 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 

若要執行同步處理，請使用[同步 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

若要設定查詢複本的數目，請使用[組 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性的 `-ReadonlyReplicaCount` 參數。

若要個別處理伺服器與查詢集區，使用[組 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性`-DefaultConnectionMode`參數來使用`Readonly`。

## <a name="connections"></a>連線

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

若為使用者用戶端連線 (如 Power BI Desktop、Excel 以及自訂應用程式)，請使用**伺服器名稱**。 

若為 SSMS、SSDT，以及 PowerShell、Azure 函數應用程式和 AMO 中的連接字串，請使用**管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有的處理作業皆發生在 （主要） 的管理伺服器上。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>疑難排解

**問題：** 使用者收到錯誤 [在連線模式 'ReadOnly' 中，找不到伺服器 '\<伺服器名稱>' 執行個體。]

**解決方案：** 選取時**區隔處理伺服器與查詢集區**選項，用戶端連線使用的預設連接字串 (不含`:rw`) 會重新導向至查詢集區的複本。 如果查詢集區中的複本因為尚未完成同步處理而未上線，則重新導向的用戶端連線可能會失敗。 若要避免連線失敗，則在執行同步處理時，查詢集區中必須有至少兩部伺服器。 每部伺服器會個別同步，而其他伺服器則維持線上狀態。 如果您選擇在處理期間查詢集區中不要有處理中的伺服器，可以選擇從集區中移除該伺服器以供處理，然後在處理完成之後，但在同步處理之前，將它加回集區。 使用記憶體和 QPU 計量來監視同步處理狀態。

## <a name="related-information"></a>相關資訊

[監視伺服器計量](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 
