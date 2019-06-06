---
title: MLOps:管理、 部署及監視 ML 模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務的 MLOps： 部署、 管理及監視您的模型持續進行改善。 您可以在本機電腦上或從其他來源中，部署使用 Azure Machine Learning Service 來定型的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 0eaf48f57c3011222b71a63d703e1ccec7aca001
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692830"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps:使用 Azure Machine Learning Service 來管理、部署及監視模型

在這篇文章，了解如何使用 Azure Machine Learning 服務來管理您的模型的生命週期。 Azure Machine Learning 使用機器學習服務作業 (MLOps) 的方法，進而改善品質和您的機器學習解決方案的一致性。 Azure Machine Learning 服務提供下列 MLOps 功能：

* 與 Azure 的管線整合。 定義您模型的連續整合和部署工作流程。
* 模型登錄，以維護多個版本的定型模型。
* 模型驗證。 自動驗證您的定型的模型，然後選取 將其部署到生產環境的最佳組態。
* 將您的模型部署為 web 服務在雲端中，在本機，或 IoT Edge 裝置。
* 監視您已部署的模型效能，因此您可以改善磁碟機下一版的模型。

若要聽到更 MLOps 和它們如何套用在 Azure Machine Learning 服務背後的概念上，請觀賞下列影片。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>與 Azure 的管線整合

您可以使用 Azure 管線來建立定型模型的持續整合程序。 在典型的案例中，當資料科學家會檢查到 Git 存放庫，針對專案中，變更 Azure 管線會開始定型執行。 然後可以檢查執行結果，以查看 定型模型的效能特性。 您也可以建立管線，以部署為 web 服務模型。

[Azure Machine Learning 的延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)可讓您更輕鬆地使用 Azure 的管線。 它可為 Azure 管線提供下列增強功能：

* 定義服務連線時，可讓工作區選取項目。
* 可讓發行訓練管線中所建立的定型模型所要觸發的管線。

如需有關如何使用 Azure Machine Learning 中使用 Azure 管線的詳細資訊，請參閱 <<c0> [ 搭配 Azure 管線的 ML 模型的連續整合和部署](/azure/devops/pipelines/targets/azure-machine-learning)文章和[Azure 機器學習服務 MLOps](https://aka.ms/mlops)存放庫。

## <a name="convert-and-optimize-models"></a>轉換並最佳化模型

轉換您的模型[開啟類神經網路交換](https://onnx.ai)(ONNX) 可能會改善效能。 平均而言，將轉換成 ONNX 可以產生 2x 效能提升。

使用 Azure Machine Learning 服務上 ONNX 資訊，請參閱[建立，並加速 ML 模型](concept-onnx.md)文章。

## <a name="register-models"></a>註冊模型

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。

> [!TIP]
> 您也可以註冊外部 Azure 機器學習服務訓練的模型。
 
已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 您也可以在可於搜尋模型時使用的註冊期間提供額外中繼資料標記。 Azure Machine Learning 服務支援可以載入使用 Python 3.5.2 或更高版本的任何模型。

您無法刪除作用中的部署中所使用的模型。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

如需註冊以 Pickle 格式儲存之模型的範例，請參閱[教學課程：將映像分類模型定型](tutorial-deploy-models-with-aml.md)。

## <a name="package-and-debug-models"></a>封裝和偵錯模型

之前將模型部署到生產環境，它會封裝成 Docker 映像。 在大部分情況下，建立映像會自動在背景中在部署期間。 對於進階案例，您可以手動指定映像。

如果您遇到部署問題時，您可以部署在本機開發環境中的疑難排解和偵錯。

如需詳細資訊，請參閱 <<c0> [ 部署模型](how-to-deploy-and-where.md#registermodel)並[疑難排解部署](how-to-troubleshoot-deployment.md)。

## <a name="validate-and-profile-models"></a>驗證和分析模型

Azure Machine Learning 服務可以使用程式碼剖析以判斷要部署您的模型時使用的理想 CPU 和記憶體設定。 模型驗證會在此程序，使用您提供的資料進行分析的程序。

## <a name="use-models"></a>使用模型

定型的機器學習服務模型可以部署為 web 服務在雲端中或在本機開發環境。 您也可以將模型部署到 Azure IoT Edge 裝置中。 部署可用 CPU、 GPU 或欄位可程式化閘陣列 (FPGA) 來推斷。 您也可以使用從 Power BI 的模型。

當使用模型做為 web 服務或 IoT Edge 裝置，您會提供下列項目：

* 用來提交給服務/裝置的資料評分的模型。
* 項目指令碼。 此指令碼接受要求，使用模型來評分資料，並傳回回應。
* 描述模型和項目指令碼所需的相依性的 conda 環境檔案。
* 任何其他資產，例如文字、 資料等所需的模型，以及項目指令碼。

這些資產封裝成 Docker 映像，並部署為 web 服務或 IoT Edge 模組。

（選擇性） 您可以使用下列參數來進一步微調部署：

* 啟用 GPU:用來啟用 GPU 支援 Docker 映像中。 映像必須使用 Microsoft Azure 服務，例如 Azure Container Instances、 Azure Kubernetes Service、 Azure Machine Learning 計算，或在 Azure 虛擬機器上。
* 額外的 docker 檔案的步驟：包含額外的 Docker 步驟，來建立 Docker 映像時執行的檔案。
* 基底映像：若要使用作為基底映像的自訂映像。 如果您不使用自訂映像，Azure Machine Learning 服務被提供基底映像。

您也可以提供目標部署平台的設定。 比方說，VM 系列類型、 可用的記憶體和核心部署至 Azure Kubernetes Service 時數。

建立映像時，也會加入 Azure Machine Learning 服務所需的元件。 比方說，所需的資產執行 web 服務和 IoT Edge 進行互動。

> [!NOTE]
> 您無法修改或變更的 web 伺服器或使用 Docker 映像中的 IoT Edge 元件。 Web 伺服器設定及測試並受到 Microsoft 的 IoT Edge 元件，則會使用 azure Machine Learning 服務。

### <a name="web-service"></a>Web 服務

您可以使用您的模型中**web 服務**以下列計算目標：

* Azure 容器執行個體
* Azure Kubernetes Service
* 本機開發環境

若要部署為 web 服務模型，您必須提供下列項目：

* 模型或模型集團。
* 若要使用此模型所需的相依性。 例如，一個接受要求，並叫用的模型、 conda 相依性，指令碼等。
* 部署組態，以描述方式和位置，將模型部署。

如需詳細資訊，請參閱 <<c0> [ 部署模型](how-to-deploy-and-where.md)。

### <a name="iot-edge-devices"></a>IoT Edge 裝置


您可以使用模型與 IoT 裝置，透過**Azure IoT Edge 模組**。 IoT Edge 模組部署到硬體裝置，可別推斷或計分，在裝置上的模型。

如需詳細資訊，請參閱 <<c0> [ 部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支援的資料分析使用機器學習服務模型。 如需詳細資訊，請參閱 <<c0> [ 在 Power BI （預覽） 中的 Azure Machine Learning 整合](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="monitor-and-collect-data"></a>監視及收集資料

監視可讓您了解哪些資料傳送至您的模型，以及它所傳回的預測。

這項資訊可協助您了解如何使用您的模型。 收集輸入的資料也可能用於訓練模型的未來的版本。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure Machine Learning 服務[部署模型的方式和位置](how-to-deploy-and-where.md)。 如需部署範例，請參閱[教學課程：部署 Azure Container Instances 中的影像分類模型](tutorial-deploy-models-with-aml.md)。

了解如何建立[搭配 Azure 管線的 ML 模型的連續整合和部署](/azure/devops/pipelines/targets/azure-machine-learning)。 

了解如何建立[使用部署為 Web 服務之模型](how-to-consume-web-service.md)的用戶端應用程式和服務。
