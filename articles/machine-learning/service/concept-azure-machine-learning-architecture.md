---
title: 架構與重要概念
titleSuffix: Azure Machine Learning service
description: 深入了解架構、 條款、 概念和 Azure Machine Learning 服務所組成的工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: e0181eea2895dbc2b3db3367c850140e3fad21d4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331712"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Azure Machine Learning 服務的運作方式：架構和概念

深入了解架構、 概念和 Azure Machine Learning 服務的工作流程。 服務的主要元件以及使用服務時的一般工作流程顯示在下圖中：

[![Azure Machine Learning 服務架構與工作流程](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>工作流程

機器學習服務工作流程通常會遵循此順序：

1. 開發機器學習服務訓練中的指令碼**Python**或透過視覺化介面。
1. 建立並設定**計算目標**。
1. **提交指令碼**至設定的計算目標以在該環境中執行。 在訓練期間，可以從**資料存放區**讀取或寫入指令碼。 而執行的記錄會在**工作區**中儲存為**執行**，並歸類在**實驗**之下。
1. **查詢實驗**取得目前和過去回合的記錄計量。 如果計量未指出所要的結果，請回到步驟 1 並逐一查看您的指令碼。
1. 找到令人滿意的回合之後，請在**模型登錄**中註冊保存的模型。
1. 開發使用模型的評分指令碼並**部署模型**作為**web 服務**在 Azure 中，或**IoT Edge 裝置**。

您執行這些步驟，使用下列其中一項：
+ [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Azure Machine Learning VS Code 擴充功能](how-to-vscode-tools.md)
+  [Azure Machine Learning 服務的視覺化介面 （預覽）](ui-concept-visual-interface.md)


## <a name="glossary-of-concepts"></a>名詞解釋的概念

+ <a href="#workspaces">工作區</a>
+ <a href="#experiments">實驗</a>
+ <a href="#models">模型</a>
+ <a href="#run-configurations">回合組態</a>
+ <a href="#datasets-and-datastores">資料集和資料存放區</a>
+ <a href="#compute-targets">計算目標</a>
+ <a href="#training-scripts">定型指令碼</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Git 追蹤</a>
+ <a href="#snapshots">快照集</a>
+ <a href="#activities">活動</a>
+ <a href="#images">影像</a>
+ <a href="#deployment">部署</a>
+ <a href="#web-service-deployments">Web 服務</a>
+ <a href="#iot-module-deployments">IoT 模組</a>
+ <a href="#ml-pipelines">ML 管線</a>
+ <a href="#logging">Logging</a>

> [!NOTE]
> 雖然本文定義了 Azure Machine Learning 服務使用的詞彙與概念，但並沒有定義 Azure 平台的詞彙與概念。 如需有關 Azure 平台技術的詳細資訊，請參閱 [Microsoft Azure 詞彙](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。


### <a name="workspaces"></a>工作區

[工作區](concept-workspace.md)是 Azure Machine Learning 服務的最上層資源。 它可以在您使用 Azure Machine Learning 服務時，提供集中式位置以處理您建立的所有成品。

下圖說明工作區的分類：

[![工作區分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

如需工作區的詳細資訊，請參閱[什麼是 Azure Machine Learning 工作區？](concept-workspace.md)。

### <a name="experiments"></a>實驗

實驗是從指定之指令碼的許多回合組成的群組。 它一律屬於某個工作區。 當您提交回合時，必須提供一個實驗名稱。 回合的資訊會儲存在該實驗底下。 如果提交回合並指定不存在的實驗名稱，就會自動以新指定的名稱建立一個新的實驗。

如需使用實驗的範例，請參閱[快速入門：開始使用 Azure Machine Learning 服務](quickstart-run-cloud-notebook.md)。

### <a name="models"></a>模型

簡單來說，模型是可接受輸入並產生輸出的一段程式碼。 機器學習服務模型的建立作業涵蓋選取演算法、提供資料給演算法，以及調整超參數。 定型是一種反覆進行的程序，可產生定型的模型，其中會封裝模型在定型程序期間學到項目。

模型是由 Azure Machine Learning 中的回合所產生的。 您也可以使用在 Azure Machine Learning 外部定型的模型。 您可以在 Azure Machine Learning 服務工作區中註冊模型。

Azure Machine Learning 服務與架構無關。 當您建立模型時，您可以使用任何受歡迎的機器學習架構，等 scikit-learn、 XGBoost、 PyTorch、 TensorFlow、 Chainer。

定型模型的範例，請參閱[教學課程：使用 Azure Machine Learning 服務將映像分類模型定型](tutorial-train-models-with-aml.md)。

**模型登錄**記錄的 Azure 機器學習服務工作區中的所有模型。

模型是透過名稱與版本來識別的。 每次註冊與現有模型名稱相同的模型時，登錄都會假設它是一個新版本。 版本會累加，新模型則會以相同的名稱來註冊。

註冊模型時，您可以提供額外的中繼資料標記，然後在搜尋模型時使用這些標記。

> [!TIP]
> 已註冊的模型是組成模型的一或多個檔案的邏輯容器。 例如，如果您有多個檔案中儲存的模型，您可以它們註冊為單一模型中您的 Azure Machine Learning 工作區。 註冊之後，您可以下載或部署已註冊的模型然後接收已註冊的所有檔案。

您無法刪除已註冊的模型，以供作用中的部署。

如需模型註冊的範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="run-configurations"></a>回合的組態

回合組態是定義指令碼在指定的計算目標中應如何執行的一組指示。 此組態包含一組豐富的行為定義，例如是否使用現有的 Python 環境，或使用依據規格建立的 Conda 環境。

回合組態可保存在檔案 (位於包含您定型指令碼的目錄內) 中，或者可以建構為記憶體內部物件並用來提交回合。

如需回合組態的範例，請參閱[選取及使用計算目標將模型定型](how-to-set-up-training-targets.md)。

### <a name="datasets-and-datastores"></a>資料集和資料存放區

**Azure Machine Learning 資料集**（預覽） 讓它更容易存取，並使用您的資料。 資料集管理各種案例，例如模型訓練中的資料，並建立的管線。 使用 Azure 機器學習服務 SDK，您就可以存取基礎儲存體、 探索及準備資料、 管理生命週期的不同資料集定義，並用於訓練和在生產環境中的資料集之間的比較。

資料集提供方法來處理常見的格式，例如使用中的資料`from_delimited_files()`或`to_pandas_dataframe()`。

如需詳細資訊，請參閱 <<c0> [ 建立及註冊 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。  如需使用資料集的更多範例，請參閱[範例 notebook](https://aka.ms/dataset-tutorial)。

A**資料存放區**是 Azure 儲存體帳戶的儲存體抽象概念。 資料存放區可以使用 Azure Blob 容器或 Azure 檔案共用作為後端儲存體。 每個工作區都有預設資料存放區，而且您可以註冊額外的資料存放區。 使用 Python SDK API 或 Azure Machine Learning CLI 在資料存放區中儲存及擷取檔案。

### <a name="compute-targets"></a>計算目標

A[計算目標](concept-compute-target.md)可讓您指定您執行您的定型指令碼或主機服務部署的計算資源。 此位置可能是您的本機或雲端式計算資源。 計算目標，讓您輕鬆變更計算環境，而不需要變更您的程式碼。 

深入了解[定型和部署的可用的計算目標](concept-compute-target.md)。 

### <a name="training-scripts"></a>將指令碼定型

您可以指定包含定型指令碼與相關聯檔案的目錄，以將模型定型。 您也可以指定用來儲存定型期間所收集資訊的實驗名稱。 在定型期間，整個目錄會複製到定型環境 (計算目標)，然後啟動回合組態指定的指令碼。 目錄的快照集也會儲存在工作區中的實驗底下。

如需範例，請參閱[教學課程：使用 Azure Machine Learning 服務將映像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="runs"></a>執行

回合是包含下列資訊的記錄：

* 與回合有關的中繼資料 (時間戳記、持續時間等等)
* 由您的指令碼記錄的計量
* 由實驗自動收集，或由您明確上傳的輸出檔案
* 包含您指令碼之目錄的快照 (執行之前)

當您提交指令碼以將模型定型時，就會產生回合。 回合可以有 0 或多個子回合。 例如，最上層回合可能有兩個子回合，其中每個可能都有自己的子回合。

如需檢視定型模型所產生之回合的範例，請參閱[快速入門：開始使用 Azure Machine Learning 服務](quickstart-run-cloud-notebook.md)。

### <a name="github-tracking-and-integration"></a>GitHub 追蹤和整合

當您啟動定型，執行 [source] 目錄所在的本機 Git 儲存機制時，儲存機制的資訊會儲存執行歷程記錄中。 例如，存放庫的最新的認可識別碼會記錄做為歷程記錄的一部分。 這適用於使用估計工具、 ML 管線或執行指令碼提交的執行。 它也適用於執行從 SDK 或 Machine Learning CLI 提交。

### <a name="snapshots"></a>快照集

提交回合時，Azure Machine Learning 會將包含指令碼的目錄壓縮成 zip 檔案，然後傳送至計算目標。 接著會將 zip 檔案解壓縮並在該處執行指令碼。 Azure Machine Learning 也會將 zip 檔案以快照方式儲存在回合記錄中。 任何擁有工作區存取權的人都能瀏覽回合記錄並下載快照集。

> [!NOTE]
> 若要避免不必要的檔案快照集所包含，請略過檔案 （.gitignore 或.amlignore）。 將此檔案放在快照集目錄，並新增要忽略的檔案名稱。 .Amlignore 檔案使用相同[語法和模式的.gitignore 檔案](https://git-scm.com/docs/gitignore)。 如果這兩個檔案存在，.amlignore 檔案的優先順序較高。

### <a name="activities"></a>activities

活動代表長時間執行的作業。 下列作業為活動範例：

* 建立或刪除計算目標
* 在計算目標上執行指令碼

活動可透過 SDK 或 Web UI 提供通知，方便您輕鬆監視這些作業的進度。

### <a name="images"></a>映像

映像提供一個可靠的方式來部署模型以及使用模型時所需的所有元件。 映像包含下列項目：

* 模型。
* 評分指令碼或應用程式。 您可以使用此指令碼來傳送輸入給模型，並傳回模型的輸出。
* 模型或評分指令碼或應用程式所需的相依性。 例如，您可以會包含列出 Python 套件相依性的 Conda 環境檔案。

Azure Machine Learning 可以建立兩種類型的映像：

* **FPGA 映像**：在部署至 Azure 雲端的現場可程式化閘陣列時使用。
* **Docker 映像**：在部署至 FPGA 以外的計算目標時使用。 例如，Azure 容器執行個體與 Azure Kubernetes Service。

Azure Machine Learning 服務提供基底映像，預設會使用。 您也可以提供您自己的自訂映像。

### <a name="image-registry"></a>映像登錄

映像會在編目**映像登錄**工作區中。 您可以提供額外的中繼資料標記當您建立映像，以便您可以查詢以尋找您的映像。

如需建立映像的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

如需使用自訂映像部署模型的範例，請參閱 <<c0> [ 如何部署模型使用自訂的 Docker 映像](how-to-deploy-custom-docker-image.md)。

### <a name="deployment"></a>部署

部署會具現化您的模型為 web 服務可以裝載在雲端中或 IoT 模組進行整合式的裝置的部署。

#### <a name="web-service-deployments"></a>Web 服務部署

已部署的 Web 服務可以使用 Azure 容器執行個體、Azure Kubernetes Service 或 FPGA。 您可以建立服務從您的模型、 指令碼和相關聯的檔案。 這些都封裝在圖中，可提供 web 服務的執行的階段環境。 映像包含經過負載平衡的 HTTP 端點，可接收傳送至 Web 服務的評分要求。

如果已選擇啟用 Application Insight 遙測或模型遙測收集功能，Azure 將可以透過此功能協助您監視 Web 服務部署。 遙測資料只有您才能存取，而且會儲存在您的 Application Insights 與儲存體帳戶執行個體中。

如果已經啟用自動調整功能，Azure 將會自動調整您的部署。

如需將模型部署為 Web 服務的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

#### <a name="iot-module-deployments"></a>IoT 模組部署

已部署的 IoT 模組是一個 Docker 容器，其中包含您的模型與相關聯的指令碼或應用程式，以及任何額外的相依性。 您可以在 edge 裝置上使用 Azure IoT Edge 部署這些模組。

如果您已經啟用監視功能，Azure 就會從 Azure IoT Edge 模組內部的模型收集遙測資料。 遙測資料只有您才能存取，而且會儲存在您的儲存體帳戶執行個體中。

Azure IoT Edge 會確保模組正在執行，並監視裝載模組的裝置。

### <a name="ml-pipelines"></a>ML 管線

您可以使用機器學習管線來建立和管理結合多個機器學習階段的工作流程。 例如，管線可能包含資料準備、 模型訓練、 模型部署和評分推斷/階段。 每個階段都可以包含多個步驟，這些步驟各自都可以在各種計算目標中自動執行。

如需有關機器學習管線與此服務的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md)。

### <a name="logging"></a>記錄

開發解決方案時，請在 Python 指令碼中使用 Azure Machine Learning Python SDK 以記錄任意計量。 在該回合之後，請查詢計量以判斷該回合是否產生您要部署的模型。

### <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning 服務，請參閱：

* [什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)
* [建立 Azure 機器學習服務工作區](setup-create-workspace.md)
* [教學課程 (第 1 部分)：將模型定型](tutorial-train-models-with-aml.md)
