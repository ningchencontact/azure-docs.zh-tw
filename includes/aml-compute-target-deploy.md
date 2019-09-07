---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 0f519471ad9f3d451964ed70afea268946c14e59
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390583"
---
| 計算目標 | 用於 | GPU 支援 | FPGA 支援 | 描述 |
| ----- | ----- | ----- | ----- | ----- |
| [本機&nbsp;web&nbsp;服務](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和疑難排解。 硬體加速取決於使用本機系統中的程式庫。
| [筆記本 VM&nbsp;web&nbsp;服務](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和疑難排解。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 即時推斷 |  [是](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [是](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |用於大規模生產環境部署。 提供已部署服務的快速回應時間和自動調整。 不支援透過 Azure Machine Learning SDK 進行叢集自動調整。 若要變更 AKS 叢集中的節點，請在 Azure 入口網站中使用 AKS 叢集的 UI。 AKS 是視覺化介面唯一可用的選項。 |
| [Azure 容器執行個體](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 測試或開發 | &nbsp;  | &nbsp; | 針對需要少於 48 GB RAM 的低規模 CPU 型工作負載，使用。 |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | 預覽批&nbsp;次推斷 | &nbsp; | &nbsp;  | 在無伺服器計算上執行批次評分。 支援一般和低優先順序的 Vm。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | 預覽IoT&nbsp;模組 |  &nbsp; | &nbsp; | 在 IoT 裝置上部署和提供 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | 是 | 在 IoT 裝置上部署和提供 ML 模型。 |

> [!NOTE]
> 雖然計算目標（例如本機、筆記本 VM 和 Azure Machine Learning 計算）支援用於定型和實驗的 GPU，但只有在 Azure Kubernetes Service 上才支援使用 GPU 進行推斷。