---
title: MLOps管理、部署、& 監視 ML 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning MLOps：部署、管理及監視模型，以持續改進它們。 您可以在本機電腦上或從其他來源中，部署使用 Azure Machine Learning 所訓練的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 98a3102d47504b40a6b62eb329b508468947ca79
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035472"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning"></a>MLOps使用 Azure Machine Learning 管理、部署和監視模型

在本文中，您將瞭解如何使用 Azure Machine Learning 來管理模型的生命週期。 Azure Machine Learning 使用 Machine Learning 作業（MLOps）方法，可改善機器學習解決方案的品質和一致性。 

Azure Machine Learning 提供下列 MLOps 功能：

- **從任何地方部署 ML 專案**
- **監視 ml 應用程式的操作和 ML 相關問題**-比較定型和推斷之間的模型輸入、探索模型特定計量，以及提供 ML 基礎結構的監視和警示。
- **捕捉建立 ML 生命週期的端對端審核記錄所需的資料**，包括正在發佈模型的物件、變更的原因，以及模型部署或用於生產環境中的時間。
- **使用 Azure Machine Learning 和 Azure DevOps 自動化端對端 ML 生命週期**，以便經常更新模型、測試新模型，以及持續推出新的 ml 模型以及其他應用程式和服務。

若要深入瞭解 MLOps 背後的概念以及如何將其套用到 Azure Machine Learning，請觀看下列影片。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>從任何地方部署 ML 專案

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>將您的定型流程轉換成可重現的管線
使用 Azure Machine Learning 中的 ML 管線，將模型定型程式中涉及的所有步驟結合在一起，從資料準備到功能解壓縮到超參數微調到模型評估。

如需詳細資訊，請參閱[ML 管線](concept-ml-pipelines.md)。

### <a name="register-and-track-ml-models"></a>註冊和追蹤 ML 模型

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。

> [!TIP]
> 已註冊的模型是組成模型的一或多個檔案的邏輯容器。 例如, 如果您的模型儲存在多個檔案中, 您可以將它們註冊為 Azure Machine Learning 工作區中的單一模型。 註冊之後, 您就可以下載或部署已註冊的模型, 並接收所有已註冊的檔案。
 
已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 您也可以在可於搜尋模型時使用的註冊期間提供額外中繼資料標記。 Azure Machine Learning 支援可使用 Python 3.5.2 或更高版本載入的任何模型。

> [!TIP]
> 您也可以註冊在 Azure Machine Learning 外部定型的模型。

您無法刪除使用中部署的已註冊模型。
如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

### <a name="package-and-debug-models"></a>封裝和調試模型

將模型部署到生產環境之前，它會封裝成 Docker 映射。 在大部分情況下，映射建立會在部署期間自動在背景中進行。 在 advanced 案例中，您可以手動指定映射。

如果您在部署時遇到問題，您可以在本機開發環境上部署，以進行疑難排解和偵錯工具。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)和[疑難排解部署](how-to-troubleshoot-deployment.md)。

### <a name="validate-and-profile-models"></a>驗證和分析模型

Azure Machine Learning 可以流量分析來判斷部署模型時所要使用的理想 CPU 和記憶體設定。 模型驗證會在此程式中進行，並使用您為分析程式提供的資料。

### <a name="convert-and-optimize-models"></a>轉換和優化模型

將模型轉換成[Open Neural Network Exchange](https://onnx.ai) （ONNX）可能會改善效能。 平均來說，轉換成 ONNX 可能會增加2倍的效能。

如需 ONNX 與 Azure Machine Learning 的詳細資訊，請參閱[建立和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

定型的機器學習模型可以部署為雲端中的 web 服務，或在您的開發環境中。 您也可以將模型部署到 Azure IoT Edge 裝置。 部署可以使用 CPU、GPU 或可現場程式化閘道陣列（FPGA）來進行推斷。 您也可以使用 Power BI 的模型。

使用模型做為 web 服務或 IoT Edge 裝置時，您會提供下列專案：

* 用來對提交給服務/裝置的資料進行評分的模型。
* 輸入腳本。 此腳本會接受要求，使用模型來對資料進行評分，並傳迴響應。
* Conda 環境檔案，描述模型和專案腳本所需的相依性。
* 模型和專案腳本所需的任何其他資產，例如文字、資料等等。

這些資產會封裝成 Docker 映射，並部署為 web 服務或 IoT Edge 模組。

（選擇性）您可以使用下列參數來進一步調整部署：

* 啟用 GPU：用來啟用 Docker 映射中的 GPU 支援。 映射必須用於 Microsoft Azure 服務，例如 Azure 容器實例、Azure Kubernetes Service、Azure Machine Learning 計算或 Azure 虛擬機器。
* 額外的 docker 檔案步驟：檔案，其中包含建立 Docker 映射時要執行的其他 Docker 步驟。
* 基底映射：要當做基底映射使用的自訂映射。 如果您未使用自訂映射，則 Azure Machine Learning 會提供基底映射。

您也會提供目標部署平臺的設定。 例如，VM 系列類型、可用記憶體，以及部署到 Azure Kubernetes Service 時的核心數目。

建立映射時，也會新增 Azure Machine Learning 所需的元件。 例如，執行 web 服務和與 IoT Edge 互動所需的資產。

> [!NOTE]
> 您無法修改或變更 Docker 映射中使用的 web 伺服器或 IoT Edge 元件。 Azure Machine Learning 使用 web 伺服器設定，並 IoT Edge Microsoft 所測試及支援的元件。

#### <a name="web-service"></a>Web 服務

您可以在**web 服務**中使用您的模型，並搭配下列計算目標：

* Azure 容器執行個體
* Azure Kubernetes Service
* 本機開發環境

若要將模型部署為 web 服務，您必須提供下列專案：

* 模型或模型的集團。
* 使用模型所需的相依性。 例如，接受要求並叫用模型、conda 相依性等的腳本。
* 部署設定，描述部署模型的方式和位置。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT Edge 裝置

您可以透過**Azure IoT Edge 模組**，使用 IoT 裝置的模型。 IoT Edge 模組會部署到裝置上啟用推斷或模型評分的硬體裝置。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支援使用機器學習模型來進行資料分析。 如需詳細資訊，請參閱[Power BI 中的 Azure Machine Learning 整合（預覽）](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>監視 ML 應用程式的操作和 ML 相關問題

監視可讓您瞭解哪些資料要傳送至您的模型，以及它所傳回的預測。

此資訊可協助您瞭解模型的使用方式。 收集的輸入資料可能也有助於定型模型的未來版本。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>獲取 ML 生命週期的端對端審核線索

Azure ML 讓您能夠追蹤所有 ML 資產的端對端審核記錄。 具體而言：

- Azure ML 會[與 Git 整合](how-to-set-up-training-targets.md#gitintegration)，以追蹤您的程式碼來自哪個存放庫/分支/認可的資訊。
- [AZURE ML 資料集](how-to-create-register-datasets.md)可協助您追蹤和版本資料。
- Azure ML 執行歷程記錄會儲存用來定型模型之程式碼、資料和計算的快照集。
- Azure ML 模型登錄會捕捉與您的模型相關聯的所有中繼資料（如果其部署狀況良好，則會在部署時進行定型）。

## <a name="automate-the-end-to-end-ml-lifecycle"></a>自動化端對端 ML 生命週期 

您可以使用 GitHub 和 Azure Pipelines 來建立可訓練模型的連續整合程式。 在典型的案例中，當資料科學家將變更簽入專案的 Git 存放庫時，Azure 管線就會開始執行定型。 然後，可以檢查執行的結果，以查看定型模型的效能特性。 您也可以建立管線，將模型部署為 web 服務。

[Azure Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)模組可讓您更輕鬆地使用 Azure Pipelines。 它提供 Azure Pipelines 的下列增強功能：

* 在定義服務連接時啟用工作區選取。
* 可讓發行管線由定型管線中建立的定型模型觸發。

如需有關搭配 Azure Machine Learning 使用 Azure Pipelines 的詳細資訊，請參閱[使用 Azure Pipelines 的持續整合和部署 ML 模型一](/azure/devops/pipelines/targets/azure-machine-learning)文和[Azure Machine Learning MLOps](https://aka.ms/mlops)存放庫。

## <a name="next-steps"></a>後續步驟

深入瞭解[您可以使用 Azure Machine Learning 部署模型的方式和位置](how-to-deploy-and-where.md)。 如需部署的範例，請[參閱教學課程：在 Azure 容器實例](tutorial-deploy-models-with-aml.md)中部署映射分類模型。

瞭解如何[使用 Azure Pipelines 建立 ML 模型的持續整合和部署](/azure/devops/pipelines/targets/azure-machine-learning)。 

了解如何建立[使用部署為 Web 服務之模型](how-to-consume-web-service.md)的用戶端應用程式和服務。
