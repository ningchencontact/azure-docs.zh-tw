---
title: 雲端的機器學習服務：詞彙與架構
titleSuffix: Azure Machine Learning service
description: 了解組成 Azure Machine Learning 服務的架構、技術與概念。 您也會了解使用服務的一般工作流程，以及 Azure Machine Learning 服務所使用的 Azure 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9e910fb3bc75c285986871627d875296f1a2a746
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577317"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Azure Machine Learning 服務的運作方式：架構和概念

本文說明 Azure Machine Learning 服務的架構與概念。 服務的主要元件以及使用服務時的一般工作流程顯示在下圖中：

[![Azure Machine Learning 服務架構與工作流程](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

工作流程一般遵循下列順序：

1. 使用 **Python** 開發機器學習服務定型指令碼。
1. 建立並設定**計算目標**。
1. **提交指令碼**至設定的計算目標以在該環境中執行。 在訓練期間，可以從**資料存放區**讀取或寫入指令碼。 而執行的記錄會在**工作區**中儲存為**執行**，並歸類在**實驗**之下。
1. **查詢實驗**取得目前和過去回合的記錄計量。 如果計量未指出所要的結果，請回到步驟 1 並逐一查看您的指令碼。
1. 找到令人滿意的回合之後，請在**模型登錄**中註冊保存的模型。
1. 開發評分指令碼。
1. **建立映像**並在**映像登錄**中註冊該映像。
1. **將映像** 部署為 Azure 中的 **Web 服務**。


> [!NOTE]
> 雖然本文定義了 Azure Machine Learning 服務使用的詞彙與概念，但並沒有定義 Azure 平台的詞彙與概念。 如需有關 Azure 平台技術的詳細資訊，請參閱 [Microsoft Azure 詞彙](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)。

## <a name="workspace"></a>工作區

工作區是 Azure Machine Learning 服務的最上層資源。 它可以在您使用 Azure Machine Learning 服務時，提供集中式位置以處理您建立的所有成品。

工作區會保留一份可用於定型模型的計算目標清單。 它也會保留定型執行記錄，包括記錄、計量、輸出，以及指令碼快照集。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型。

您要在工作區註冊模型。 您要使用註冊的模型與評分指令碼建立映像。 接著，您可以將映像部署到 Azure 容器執行個體、Azure Kubernetes Service 或現場可程式化閘陣列 (FPGA) 中，作為 REST 型 HTTP 輸出。 您也可以將映像部署到 Azure IoT Edge 裝置作為模組。

您可以建立多個工作區，而且每個工作區都可由多人共用。 當您共用工作區時，您可以將使用者指派給下列角色來控制其存取權：

* 擁有者
* 參與者
* 讀取者

如需有關這些角色的詳細資訊，請參閱 <<c0> [ 管理的存取權的 Azure Machine Learning 工作區](how-to-assign-roles.md)文章。

建立新的工作區時，會自動建立工作區使用的幾個 Azure 資源：

* [Azure 容器登錄](https://azure.microsoft.com/services/container-registry/)：註冊您在定型期間及部署模型時使用的 Docker 容器。
* [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)：用來作為工作區的預設資料存放區。
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：儲存與模型有關的監視資訊。
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：儲存計算目標所使用的密碼與工作區所需的其他機密資訊。

> [!NOTE]
> 除了建立新的版本之外，您也可以使用現有的 Azure 服務。

下圖說明工作區的分類：

[![工作區分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="experiment"></a>實驗

實驗是從指定之指令碼的許多回合組成的群組。 它一律屬於某個工作區。 當您提交回合時，必須提供一個實驗名稱。 回合的資訊會儲存在該實驗底下。 如果提交回合並指定不存在的實驗名稱，就會自動以新指定的名稱建立一個新的實驗。

如需使用實驗的範例，請參閱[快速入門：開始使用 Azure Machine Learning 服務](quickstart-run-cloud-notebook.md)。

## <a name="model"></a>模型

簡單來說，模型是可接受輸入並產生輸出的一段程式碼。 機器學習服務模型的建立作業涵蓋選取演算法、提供資料給演算法，以及調整超參數。 定型是一種反覆進行的程序，可產生定型的模型，其中會封裝模型在定型程序期間學到項目。

模型是由 Azure Machine Learning 中的回合所產生的。 您也可以使用在 Azure Machine Learning 外部定型的模型。 您可以在 Azure Machine Learning 服務工作區中註冊模型。

Azure Machine Learning 服務與架構無關。 建立模型時，您可以使用任何常用的機器學習服務架構，例如 Scikit-learn、XGBoost、PyTorch、TensorFlow、Chainer 與 Microsoft Cognitive Toolkit (先前稱為 CNTK)。

定型模型的範例，請參閱[教學課程：使用 Azure Machine Learning 服務將映像分類模型定型](tutorial-train-models-with-aml.md)。

### <a name="model-registry"></a>模型登錄

模型登錄可在您的 Azure Machine Learning services 工作區中持續追蹤所有模型。

模型是透過名稱與版本來識別的。 每次註冊與現有模型名稱相同的模型時，登錄都會假設它是一個新版本。 版本會累加，新模型則會以相同的名稱來註冊。

註冊模型時，您可以提供額外的中繼資料標記，然後在搜尋模型時使用這些標記。

您無法刪除映像正在使用的模型。

如需模型註冊的範例，請參閱[使用 Azure Machine Learning 將映像分類模型定型](tutorial-train-models-with-aml.md)。

## <a name="run-configuration"></a>回合組態

回合組態是定義指令碼在指定的計算目標中應如何執行的一組指示。 此組態包含一組豐富的行為定義，例如是否使用現有的 Python 環境，或使用依據規格建立的 Conda 環境。

回合組態可保存在檔案 (位於包含您定型指令碼的目錄內) 中，或者可以建構為記憶體內部物件並用來提交回合。

如需回合組態的範例，請參閱[選取及使用計算目標將模型定型](how-to-set-up-training-targets.md)。

## <a name="datastore"></a>資料存放區

資料存放區是 Azure 儲存體帳戶的儲存體抽象概念。 資料存放區可以使用 Azure Blob 容器或 Azure 檔案共用作為後端儲存體。 每個工作區都有預設資料存放區，而且您可以註冊額外的資料存放區。

使用 Python SDK API 或 Azure Machine Learning CLI 在資料存放區中儲存及擷取檔案。

## <a name="compute-target"></a>計算目標

計算目標是用來執行定型指令碼，或裝載服務部署的計算資源。 支援的計算目標包括：

| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| 您的本機電腦 | ✓ | &nbsp; |
| Azure Machine Learning Compute | ✓ | &nbsp; |
| Azure 中的 Linux VM</br>(例如資料科學虛擬機器) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Apache Spark for HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(現場可程式化閘陣列) | &nbsp; | ✓ |

計算目標會附加至工作區。 本機電腦以外的計算目標會由工作區的使用者共用。

### <a name="managed-and-unmanaged-compute-targets"></a>受控和非受控計算目標

* **受控**：由 Azure Machine Learning 服務所建立和管理的計算目標。 這些計算目標已針對機器學習工作負載進行最佳化。 Azure Machine Learning Compute 是截至 2018 年 12 月 4 日為止，唯一的受控計算目標。 未來可能會新增其他受控計算目標。

    您可以使用 Azure 入口網站、Azure Machine Learning SDK 或 Azure CLI，直接透過工作區來建立機器學習計算執行個體。 其他所有計算目標則必須在工作區外建立，然後再與其連結。

* **非受控**：*不是*由 Azure Machine Learning 服務所管理的計算目標。 您可能需要在 Azure Machine Learning 外建立它們，然後再將其連結到您的工作區，才能使用它們。 非受控的計算目標可能需要額外的步驟來進行維護，或針對機器學習工作負載提升效能。

如需有關選取計算目標以進行定型的資訊，請參閱[選取並使用計算目標以將模型定型](how-to-set-up-training-targets.md)。

如需有關選取計算目標以進行部署的資訊，請參閱[使用 Azure Machine Learning 服務部署模型](how-to-deploy-and-where.md)。

## <a name="training-script"></a>定型指令碼

您可以指定包含定型指令碼與相關聯檔案的目錄，以將模型定型。 您也可以指定用來儲存定型期間所收集資訊的實驗名稱。 在定型期間，整個目錄會複製到定型環境 (計算目標)，然後啟動回合組態指定的指令碼。 目錄的快照集也會儲存在工作區中的實驗底下。

如需範例，請參閱[教學課程：使用 Azure Machine Learning 服務將映像分類模型定型](tutorial-train-models-with-aml.md)。

## <a name="run"></a>執行

回合是包含下列資訊的記錄：

* 與回合有關的中繼資料 (時間戳記、持續時間等等)
* 由您的指令碼記錄的計量
* 由實驗自動收集，或由您明確上傳的輸出檔案
* 包含您指令碼之目錄的快照 (執行之前)

當您提交指令碼以將模型定型時，就會產生回合。 回合可以有 0 或多個子回合。 例如，最上層回合可能有兩個子回合，其中每個可能都有自己的子回合。

如需檢視定型模型所產生之回合的範例，請參閱[快速入門：開始使用 Azure Machine Learning 服務](quickstart-run-cloud-notebook.md)。

## <a name="snapshot"></a>快照

提交回合時，Azure Machine Learning 會將包含指令碼的目錄壓縮成 zip 檔案，然後傳送至計算目標。 接著會將 zip 檔案解壓縮並在該處執行指令碼。 Azure Machine Learning 也會將 zip 檔案以快照方式儲存在回合記錄中。 任何擁有工作區存取權的人都能瀏覽回合記錄並下載快照集。

## <a name="activity"></a>活動

活動代表長時間執行的作業。 下列作業為活動範例：

* 建立或刪除計算目標
* 在計算目標上執行指令碼

活動可透過 SDK 或 Web UI 提供通知，方便您輕鬆監視這些作業的進度。

## <a name="image"></a>映像

映像提供一個可靠的方式來部署模型以及使用模型時所需的所有元件。 映像包含下列項目：

* 模型。
* 評分指令碼或應用程式。 您可以使用此指令碼來傳送輸入給模型，並傳回模型的輸出。
* 模型或評分指令碼或應用程式所需的相依性。 例如，您可以會包含列出 Python 套件相依性的 Conda 環境檔案。

Azure Machine Learning 可以建立兩種類型的映像：

* **FPGA 映像**：在部署至 Azure 雲端的現場可程式化閘陣列時使用。
* **Docker 映像**：在部署至 FPGA 以外的計算目標時使用。 例如，Azure 容器執行個體與 Azure Kubernetes Service。

如需建立映像的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

### <a name="image-registry"></a>映像登錄

映像登錄可記錄從您的模型建立的映像。 您可以在建立映像時提供額外的中繼資料標記。 映像登錄會儲存中繼資料標記，而且您可以透過查詢標記尋找您的映像。

## <a name="deployment"></a>部署

部署是指在可能裝載於雲端的 Web 服務，或在用於整合式裝置部署的 IoT 模組中將影像具現化。

### <a name="web-service"></a>Web 服務

已部署的 Web 服務可以使用 Azure 容器執行個體、Azure Kubernetes Service 或 FPGA。 您可以從封裝您模型、指令碼與相關聯檔案的映像建立服務。 映像包含經過負載平衡的 HTTP 端點，可接收傳送至 Web 服務的評分要求。

如果已選擇啟用 Application Insight 遙測或模型遙測收集功能，Azure 將可以透過此功能協助您監視 Web 服務部署。 遙測資料只有您才能存取，而且會儲存在您的 Application Insights 與儲存體帳戶執行個體中。

如果已經啟用自動調整功能，Azure 將會自動調整您的部署。

如需將模型部署為 Web 服務的範例，請參閱[在 Azure 容器執行個體中部署映像分類模型](tutorial-deploy-models-with-aml.md)。

### <a name="iot-module"></a>IoT 模組

已部署的 IoT 模組是一個 Docker 容器，其中包含您的模型與相關聯的指令碼或應用程式，以及任何額外的相依性。 您可以在 edge 裝置上使用 Azure IoT Edge 部署這些模組。

如果您已經啟用監視功能，Azure 就會從 Azure IoT Edge 模組內部的模型收集遙測資料。 遙測資料只有您才能存取，而且會儲存在您的儲存體帳戶執行個體中。

Azure IoT Edge 會確保模組正在執行，並監視裝載模組的裝置。

## <a name="pipeline"></a>管線

您可以使用機器學習管線來建立和管理結合多個機器學習階段的工作流程。 例如，管線可能包含資料準備、模型訓練、模型部署和推斷階段。 每個階段都可以包含多個步驟，這些步驟各自都可以在各種計算目標中自動執行。

如需有關機器學習管線與此服務的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md)。

## <a name="logging"></a>記錄

開發解決方案時，請在 Python 指令碼中使用 Azure Machine Learning Python SDK 以記錄任意計量。 在該回合之後，請查詢計量以判斷該回合是否產生您要部署的模型。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning 服務，請參閱：

* [什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)
* [建立 Azure 機器學習服務工作區](setup-create-workspace.md)
* [教學課程：將模型定型](tutorial-train-models-with-aml.md)
* [使用 Resource Manager 範本建立工作區](how-to-create-workspace-template.md)