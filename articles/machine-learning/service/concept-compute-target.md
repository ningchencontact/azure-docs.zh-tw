---
title: 計算目標：定型和部署模型的位置
titleSuffix: Azure Machine Learning service
description: 定義您想要使用 Azure Machine Learning 服務定型或部署模型的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: b2e4c9160ccad66b04dd30979f0568da68836d76
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860599"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Azure Machine Learning 服務中的計算目標是什麼？ 

**計算目標**是指定的計算資源/環境，您可以在其中執行定型腳本或裝載您的服務部署。 此位置可能是您的本機電腦或雲端式計算資源。 使用計算目標可讓您更輕鬆地在稍後變更計算環境，而不需要變更您的程式碼。  

在典型的模型開發生命週期中，您可能會：
1. 從開發和實驗少量的資料開始。 在這個階段，我們建議您的本機環境（本機電腦或雲端式 VM）做為您的計算目標。 
2. 相應增加至較大的資料，或使用其中一個[訓練計算目標](#train)進行分散式訓練。  
3. 當您的模型就緒之後，請將它部署到具有其中一個[部署計算目標](#deploy)的 web 主控環境或 IoT 裝置。

您用於計算目標的計算資源會附加至[工作區](concept-workspace.md)。 工作區的使用者會共用本機電腦以外的計算資源。

## <a name="train"></a>訓練計算目標

Azure Machine Learning 服務在不同的計算資源上有不同的支援。  您也可以附加自己的計算資源，但支援不同的案例可能會有所不同。

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

深入瞭解如何[設定及使用計算目標來進行模型定型](how-to-set-up-training-targets.md)。

## <a name="deploy"></a>部署目標

下列計算資源可以用來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

瞭解[將模型部署至計算目標的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算（受控）

受管理的計算資源是由 Azure Machine Learning 服務來建立和管理。 此計算已針對機器學習工作負載進行優化。 Azure Machine Learning 計算是 2019 5 月30日的唯一受控計算。 未來可能會新增其他受控計算資源。

您可以使用 Azure Machine Learning 計算進行定型和批次推斷（預覽）。  使用此計算資源，您可以：

* 單一或多節點叢集
* 每次提交執行時自動調整 
* 自動叢集管理和作業排程 
* 同時支援 CPU 和 GPU 資源

您可以使用 SDK 或使用 CLI，在 Azure 入口網站或[工作區登陸頁面（預覽）](https://ml.azure.com)中建立 Azure Machine Learning 計算實例。 建立時，它會自動屬於您的工作區，而不像其他類型的計算目標。

## <a name="unmanaged-compute"></a>非受控計算

非受控計算目標*不*受 Azure Machine Learning 服務管理。 您會在 Azure Machine Learning 外部建立這種類型的計算目標，然後將它附加至您的工作區。 非受控計算資源可能需要額外的步驟，才能維護或改善機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [設定計算目標以將模型定型](how-to-set-up-training-targets.md)
* [將您的模型部署到計算目標](how-to-deploy-and-where.md)