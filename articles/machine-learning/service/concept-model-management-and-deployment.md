---
title: 管理、註冊、部署和監視 ML 模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning Service 來部署、管理及監視模型，以持續改善這些模型。 您可以在本機電腦上或從其他來源中，部署使用 Azure Machine Learning Service 來定型的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59275431"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>使用 Azure Machine Learning Service 來管理、部署及監視模型

在本文中，您會了解如何使用 Azure Machine Learning Service 來部署、管理及監視模型，以持續改善這些模型。 您可以在本機電腦上或從其他來源中，部署使用 Azure Machine Learning 所訓練的模型。 

下圖說明完整的部署工作流程：[![Azure Machine Learning 的部署工作流程](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

部署工作流程包含下列步驟：
1. 在 Azure Machine Learning Service 工作區所裝載的登錄中**註冊模型**
1. **註冊映像**，以將具有評分指令碼之模型與可攜式容器中的相依性配對 
1. **部署**映像為雲端或邊緣裝置中的 Web 服務
1. **監視和收集資料**
1. **更新**部署已使用新的映像。

每個步驟都可以單獨執行或當成單一部署命令的一部分來執行。 此外，您還可以將部署整合到 **CI/CD 工作流程**，如本圖所示。

[![' Azure Machine Learning 持續整合/持續部署 (CI/CD) 週期 '](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>步驟 1：註冊模型

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。
 
已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 您也可以在可於搜尋模型時使用的註冊期間提供額外中繼資料標記。 Azure Machine Learning 服務支援可使用 Python 3 載入的任何模型。 

您無法刪除映像正在使用的模型。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

如需註冊以 Pickle 格式儲存之模型的範例，請參閱[教學課程：將映像分類模型定型](tutorial-deploy-models-with-aml.md)。

如需使用 ONNX 模型的詳細資訊，請參閱 [ONNX 和 Azure Machine Learning](how-to-build-deploy-onnx.md) 文件。

## <a name="step-2-register-image"></a>步驟 2：註冊映像

映像可讓您部署可靠的模型，以及使用模型所需的所有元件。 映像包含下列項目：

* 模型
* 評分引擎
* 評分檔案或應用程式
* 對模型評分所需的任何相依性

映像也可以包含用於記錄和監視的 SDK 元件。 SDK 記錄資料可以用來微調或重新訓練模型 (包含模型的輸入和輸出)。

Azure Machine Learning 支援最熱門架構，但一般而言，任何可使用 PIP 安裝的架構可以運作。

建立工作區時，因此為該工作區使用的其他數個其他 Azure 資源。
用來建立預設的映像的所有物件會都儲存在您的工作區中的 Azure 儲存體帳戶。 您可以在建立映像時提供額外的中繼資料標記。 映像登錄也會儲存中繼資料標記，並可透過查詢標記尋找您的映像。

您也可以使用自訂映像，可用來上傳至 Azure Container Registry 和 Azure Machine Learning 服務所使用。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#configureimage)的設定和註冊映像一節。

## <a name="step-3-deploy-image"></a>步驟 3：部署映像

您可以將已註冊的映像部署至雲端或邊緣裝置。 部署程序會建立監視、負載平衡和自動調整您模型所需的所有資源。 在部署期間提供安全性資產，可以使用憑證驗證保護對已部署服務的存取。 您也可以升級現有的部署，以使用較新的映像。

Web 服務部署亦為可搜尋。 例如，您可以搜尋特定模型或映像的所有部署。

[![推斷的目標](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

您可以將映像部署至雲端中的下列部署目標：

* Azure 容器執行個體
* Azure Kubernetes Service
* Azure FPGA 電腦
* Azure IoT Edge 裝置

部署您的服務時，會自動負載平衡推斷要求，而且會縮放叢集以符合任何隨需的爆增。 [您服務的相關遙測可被擷取](how-to-enable-app-insights.md)到與您工作區相關的 Azure Application Insights 服務。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#deploy)的部署一節。

## <a name="step-4-monitor-models-and-collect-data"></a>步驟 4：監視模型並收集資料

有用於模型記錄和資料擷取的 SDK 可供使用，因此您可以監視模型的輸入、輸出和其他相關資料。 資料會儲存為您工作區 Azure 儲存體帳戶中的 Blob。

若要搭配使用 SDK 與您的模型，請將 SDK 匯入至評分指令碼或應用程式。 您接著可以使用 SDK 來記錄資料 (例如參數、結果或輸入詳細資料)。

如果您決定每次部署映像時都啟用模型資料收集，則會自動佈建擷取資料所需的詳細資料 (例如個人 Blob 存放區的認證)。

> [!Important]
> Microsoft 看不到從您模型所收集的資料。 資料會直接傳送至您工作區的 Azure 儲存體帳戶。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。

## <a name="step-5-update-the-deployment"></a>步驟 5：更新部署

更新模型不會自動註冊。 同樣地，註冊新映像不會自動更新以舊版映像建立的部署。 相反地，您必須手動註冊模型，註冊映像，然後更新模型。 如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#update)的更新一節。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure Machine Learning 服務[部署模型的方式和位置](how-to-deploy-and-where.md)。 如需部署範例，請參閱[教學課程：部署 Azure Container Instances 中的影像分類模型](tutorial-deploy-models-with-aml.md)。

了解如何建立[使用部署為 Web 服務之模型](how-to-consume-web-service.md)的用戶端應用程式和服務。
