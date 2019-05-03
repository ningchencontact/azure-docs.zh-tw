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
ms.openlocfilehash: 18a80af8422e30ce3e87395449fca7b5f6a73762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025025"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps:使用 Azure Machine Learning Service 來管理、部署及監視模型

在本文中，您會了解如何使用 Azure Machine Learning Service 來部署、管理及監視模型，以持續改善這些模型。 您可以在本機電腦上或從其他來源中，部署使用 Azure Machine Learning 所訓練的模型。 

下圖說明完整的部署工作流程：[![Azure Machine Learning 的部署工作流程](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps / 部署工作流程包含下列步驟：
1. 在 Azure Machine Learning Service 工作區所裝載的登錄中**註冊模型**
1. **使用**雲端、 在 IoT 裝置，或適用於 Power BI 分析中的 web 服務中的模型。
1. **監視和收集資料**
1. **更新**部署已使用新的映像。

獨立或作為單一命令的一部分，可以執行每個步驟。 此外，您可以建立**CI/CD 工作流程**如下圖所示。

[![' Azure Machine Learning 持續整合/持續部署 (CI/CD) 週期 '](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.youtube.com/embed/0MaHb070H_8]

## <a name="step-1-register-model"></a>步驟 1：註冊模型

模型註冊可讓您在 Azure 雲端自己的工作區中儲存模型和控制模型版本。 模型註冊可讓您更輕鬆地組織和追蹤已定型模型。
 
已註冊的模型是透過名稱和版本來識別。 每次註冊與現有模型名稱相同的模型時，登錄都會遞加版本。 您也可以在可於搜尋模型時使用的註冊期間提供額外中繼資料標記。 Azure Machine Learning 服務支援可以載入使用 Python 3.5.2 或更高版本的任何模型。

您無法刪除作用中的部署中所使用的模型。

如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#registermodel)的註冊模型一節。

如需註冊以 Pickle 格式儲存之模型的範例，請參閱[教學課程：將映像分類模型定型](tutorial-deploy-models-with-aml.md)。

## <a name="step-2-use-the-model"></a>步驟 2：使用模型

機器學習服務模型可用來當做 web 服務，IoT Edge 在裝置上，或從服務，例如 Power BI 的分析。

### <a name="web-service"></a>Web 服務

您可以使用您的模型中**web 服務**以下列計算目標：

* Azure 容器執行個體
* Azure Kubernetes Service

若要部署為 web 服務模型，您必須提供下列項目：

* 模型或模型集團。
* 若要使用此模型所需的相依性。 例如，一個接受要求，並叫用的模型、 conda 相依性，指令碼等。
* 部署組態，以描述方式和位置，將模型部署。

如需詳細資訊，請參閱 <<c0> [ 部署模型](how-to-deploy-and-where.md)。

### <a name="iot-edge-devices"></a>IoT Edge 裝置

您可以使用模型與 IoT 裝置，透過**Azure IoT Edge 模組**。 IoT Edge 模組部署到硬體裝置，可在裝置上的推斷。

如需詳細資訊，請參閱 <<c0> [ 部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支援的資料分析使用機器學習服務模型。 如需詳細資訊，請參閱 <<c0> [ 在 Power BI （預覽） 中的 Azure Machine Learning 整合](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="step-3-monitor-models-and-collect-data"></a>步驟 3：監視模型並收集資料

監視可讓您了解哪些資料傳送至您的模型，以及它所傳回的預測。

這項資訊可協助您了解如何使用您的模型。 收集輸入的資料也可能用於訓練模型的未來的版本。

如需詳細資訊，請參閱[如何啟用模型資料收集](how-to-enable-data-collection.md)。

## <a name="step-4-update-the-deployment"></a>步驟 4：更新部署

部署必須明確地更新。 如需詳細資訊，請參閱[部署模型](how-to-deploy-and-where.md#update)的更新一節。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure Machine Learning 服務[部署模型的方式和位置](how-to-deploy-and-where.md)。 如需部署範例，請參閱[教學課程：部署 Azure Container Instances 中的影像分類模型](tutorial-deploy-models-with-aml.md)。

了解如何建立[使用部署為 Web 服務之模型](how-to-consume-web-service.md)的用戶端應用程式和服務。
