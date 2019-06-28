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
ms.date: 05/30/2019
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331658"
---
| 計算目標 | 使用量 | GPU 支援 | FPGA 支援 | 描述 |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 測試/偵錯 | 或許 | &nbsp; | 適用於有限的測試和疑難排解。 硬體加速，取決於使用 本機系統中的 程式庫。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 即時推斷 |  [yes](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [yes](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |適用於大規模生產環境部署。 提供快速的回應時間和自動調整已部署的服務。 透過 Azure Machine Learning SDK 不支援叢集自動調整。 若要變更在 AKS 叢集中的節點，請為您的 AKS 叢集，在 Azure 入口網站中使用 UI。 AKS 會是唯一可用的視覺介面的選項。 |
| [Azure 容器執行個體 (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 測試或開發人員 | &nbsp;  | &nbsp; | 適用於低規模，以 CPU 為基礎的工作負載需要 < 48 GB RAM |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | （預覽）批次&nbsp;推斷 | 是 | &nbsp;  | 執行批次評分上無伺服器計算。 支援一般 vm 和低優先順序 Vm。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | （預覽）IoT&nbsp;模組 |  &nbsp; | &nbsp; | 部署與提供在 IoT 裝置上的 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | 是 | 部署與提供在 IoT 裝置上的 ML 模型。 |
