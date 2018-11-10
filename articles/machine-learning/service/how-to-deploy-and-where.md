---
title: 使用 Azure Machine Learning 服務如何部署模型 | Microsoft Docs
description: 了解您可以使用 Azure Machine Learning 來部署模型的不同方式。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209440"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務部署模型

Azure Machine Learning 服務提供數種部署定型模型的方式。 在此文件中，了解如何將模型部署為 Azure 雲端中的 Web 服務，或部署到 IoT 邊緣裝置。

您可以將模型部署到下列計算目標：

- [Azure Container Instances (ACI)](#aci)：快速部署。 適合用於開發或測試。
- [Azure Kubernetes Service (AKS)](#aks)：適合用於大規模生產環境部署。 提供自動調整和快速的回應時間。
- [Azure IoT Edge](#iotedge)：在 IoT 裝置上部署模型。 裝置上會發生推斷。
- [現場可程式化閘陣列 (FPGA)](#fpga)：超低的即時推斷延遲。

此文件的其餘部分會詳細討論每個選項。

## <a id="aci"></a>Azure Container Instances

如果符合下列一或多個條件，請使用 Azure 容器執行個體來將您的模型部署為 REST API 端點：
- 您需要快速部署及驗證模型。 ACI 部署會在 5 分鐘內完成。
- 您想要將模型部署在在開發或測試環境中。 ACI 可讓您在每一訂用帳戶部署 20 個容器群組。 如需詳細資訊，請參閱 [Azure 容器執行個體的配額和區域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)文件。

如需詳細資訊，請參閱[將模型部署到 Azure 容器執行個體](how-to-deploy-to-aci.md)文件。

## <a id="aks"></a>Azure Kubernetes Service

針對大規模生產環境案例，請使用 Azure Kubernetes Service (AKS)。 您可以使用現有的 AKS 叢集，或使用 Azure Machine Learning SDK、CLI 或 Azure 入口網站建立新的叢集。

建立 AKS 叢集是工作區的一次性程序。 您可以重複使用此叢集進行多個部署。 如果刪除叢集，就必須在下次需要部署時建立新的叢集。

Azure Kubernetes Service 提供以下功能：

* 自動調整
* 記錄
* 模型資料收集
* 快速的 Web 服務回應時間

建立 AKS 叢集的程序大約需要 20 分鐘。

如需詳細資訊，請參閱[將模型部署到 Azure Kubernetes Service](how-to-deploy-to-aks.md) 文件。

## <a id="iotedge"></a>Azure IoT Edge

若使用 IoT 裝置，在裝置上執行評分會比將資料傳送到雲端進行評分還快。 透過 Azure IoT Edge，您可以在邊緣裝置上裝載您的模型。 如果您需要下列的一或多個功能，請將您的模型部署到 IoT Edge：
- 即使沒有雲端連線，也可以在本機處理優先工作
- 處理因太大而無法快速從雲端提取的產生資料
- 透過在本機裝置中或接近本機裝置的情報啟用即時處理
- 配合資料隱私權的相關需求 

如需詳細資訊，請參閱[部署到 Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) 文件。

如需 IoT Edge 服務的詳細資訊，請參閱 [Azure IoT Edge 文件](https://docs.microsoft.com/azure/iot-edge/)。


## <a id="fpga"></a>現場可程式化閘陣列 (FPGA)

Project Brainwave 提供的硬體加速模型，使系統對於即時推斷要求能達到超低延遲。 Project Brainwave 加速了 Azure 雲端中部署在現場可程式化閘陣列上的深度類神經網路 (DNN)。 常用的 DNN 可供取得的形式有轉送學習的特徵化對象，或從您自己的資料定型所得之權重的可自訂項目。

如需詳細資訊，請參閱[部署到 FPGA](how-to-deploy-fpga-web-service.md) 文件。

## <a name="next-steps"></a>後續步驟

若要了解如何將模型部署到特定計算目標的詳細資訊，請參閱下列文件：

* [將模型部署到 Azure 容器執行個體](how-to-deploy-to-aci.md)
* [將模型部署到 Azure Kubernetes Service](how-to-deploy-to-aks.md)
* [將模型部署到 Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [將模型部署到 FPGA](how-to-deploy-fpga-web-service.md)
