---
title: Microsoft Azure Data Box Edge 概觀 | Microsoft Docs
description: 說明 Azure Data Box Edge，這是針對 Azure 進行網路型傳輸使用實體裝置的儲存體解決方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305426"
---
# <a name="what-is-azure-data-box-edge"></a>什麼是 Azure 資料箱邊緣？ 

Azure Data Box Edge 為具備 AI 能力的邊緣運算裝置，並具有網路資料傳輸功能。 此文章為您提供 Data Box Edge 解決方案、其優點與主要功能，以及適合部署此裝置之案例的概觀。 

Data Box Edge 是一種硬體即服務解決方案。 Microsoft 為您提供了一個雲端管理裝置，該裝置具有內建的可現場程式化閘道陣列 (FPGA)，它可啟用加速的 AI 推斷，並具備儲存體閘道的所有功能。 

## <a name="use-cases"></a>使用案例

以下是 Data Box Edge 可用於邊緣的快速 Machine Learning (ML) 推斷，並在將資料傳送至 Azure 之前先進行預先處理的各種情節。

- **使用 Azure Machine Learning 進行推斷** - 透過使用 Data Box Edge，您可以執行 ML 模型，以在資料被傳送到雲端之前快速取得能用來據以採取動作的結果。 完整資料集可以選擇性地進行傳輸，以繼續重新定型並改善您的 ML 模型。 如需有關如何在 Data Box Edge 裝置上使用 Azure ML 硬體加速模型的詳細資訊，請參閱[在 Data Box Edge 上部署 Azure ML 硬體加速模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)。

- **預先處理資料** - 在將資料傳送至 Azure 之前先轉換資料，以建立更可操作的資料集。 預先處理功能可用來： 

    - 彙總資料。
    - 修改資料，例如移除個人資料。
    - 子集資料可將儲存體和頻寬最佳化，或用於進一步分析。
    - 分析並對 IoT 事件做出反應。 

- **透過網路將資料傳輸到 Azure** - 使用 Data Box Edge 來輕鬆且快速地將資料傳輸到 Azure，以進行進一步的運算和分析，或用於封存目的。 


## <a name="key-capabilities"></a>主要功能

Data Box Edge 具有下列功能：

|功能 |說明  |
|---------|---------|
|加速 AI 推斷| 透過內建 FPGA 啟用。|
|運算       |允許對資料進行分析、處理、篩選。|
|高效能 | 高效能計算與資料傳輸。|
|資料存取     | 能使用雲端 API 直接從 Azure 儲存體 Blob 與 Azure 檔案存取資料，以在雲端中進行額外的資料處理。 裝置上的本機快取可用來快速存取最近使用的檔案。|
|雲端管理     |裝置與服務是透過 Azure 入口網站來管理的。  |
|離線上傳     | 中斷連線模式支援離線上傳案例。|
|支援的通訊協定     | 針對資料擷取支援標準 SMB 與 NFS 通訊協定。 <br> 如需支援版本的詳細資訊，請移至 [Data Box Edge 系統需求](data-box-edge-system-requirements.md) \(英文\)。|
|資料重新整理     | 能夠以來自雲端的最新檔案對本機檔案進行重新整理。|
|加密    | 支援使用 BitLocker 在本機加密資料，並透過 *https* 保護對雲端的資料傳輸。|
|頻寬節流| 節流以限制尖峰時間的頻寬使用量。|


## <a name="components"></a>元件

Data Box Edge 解決方案由 Data Box Edge 資源、Data Box Edge 實體裝置與本機 Web UI 所組成。

* **Data Box Edge 實體裝置** - 由 Microsoft 所提供的 1U 機架裝載伺服器，可設定來將資料傳送到 Azure。 
    
* **Data Box Edge 資源** - Azure 入口網站中的資源，可讓您從不同的地理位置存取 Web 介面來管理 Data Box Edge 裝置。 使用 Data Box Edge 資源來建立及 管理資源、檢視和管理裝置與警示，以及管理共用。  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    如需詳細資訊，請移至[為您的 Data Box Edge 裝置建立訂單](data-box-edge-deploy-prep.md#create-a-new-resource)。

* **資料箱本機 Web UI** - 使用本機 Web UI 來對 Data Box Edge 裝置執行診斷、關機和重新啟動、檢視複製記錄，以及連絡 Microsoft 支援服務以提出服務要求。

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    如需有關使用 Web 型 UI 的資訊，請移至[使用 Web 型 UI 來管理資料箱](data-box-edge-manage-access-power-connectivity-mode.md)。


## <a name="region-availability"></a>區域可用性

Data Box Edge 實體裝置、Azure 資源，以及您要將資料傳輸至其中的目標儲存體帳戶，並不需要全都位於相同的區域。

- **資源可用性** - 如需 Data Box Edge 適用區域的完整清單，請移至[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 Data Box Edge 也可部署在 Azure Government 雲端中。 如需詳細資訊，請參閱[何謂 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。
    
- **目的地儲存體帳戶** - 儲存資料的儲存體帳戶在所有 Azure 區域都可用。 若要獲得最佳效能，儲存 Data Box Edge 資料的儲存體帳戶應位於接近裝置所在區域的區域。 儲存體帳戶的位置若離裝置遙遠，將會導致較長的延遲時間與較緩慢的效能。 


## <a name="next-steps"></a>後續步驟

- 檢閱 [Data Box Edge 系統需求](data-box-edge-system-requirements.md)。
- 了解 [Data Box Edge 限制](data-box-edge-limits.md)。
- 在 Azure 入口網站中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。




