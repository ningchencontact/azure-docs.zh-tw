---
title: 計算目標： 定型和部署模型的位置
titleSuffix: Azure Machine Learning service
description: 定義您想要用來定型或部署您使用 Azure Machine Learning 服務的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806033"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>在 Azure Machine Learning 服務中的計算目標為何？ 

A**計算目標**是指定的計算資源環境您其中執行訓練指令碼或主機服務部署。 此位置可能是您的本機或雲端式計算資源。 使用計算目標方便您之後變更您的計算環境，而不必變更程式碼。  

在一般模型開發生命週期中，您可能會：
1. 開始開發和實驗上少量的資料。 在這個階段，我們建議您的本機環境 （本機電腦或雲端為基礎的 VM） 作為計算目標。 
2. 相應增加為較大的資料，或不要散發使用其中一種訓練[訓練的計算目標](#train)。  
3. 準備您的模型之後，將它部署至 web 主控的環境或使用其中一種將 IoT 裝置[部署的計算目標](#deploy)。

您使用的計算目標的計算資源會附加至[工作區](concept-workspace.md)。 計算的工作區的使用者會共用在本機電腦以外的資源。

## <a name="train"></a> 定型的計算目標

Azure Machine Learning 服務有不同的支援跨不同的計算資源。  您也可以附加您自己的計算資源，雖然支援各種案例而有所不同。

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

深入了解[設定和使用計算目標進行模型定型](how-to-set-up-training-targets.md)。

## <a name="deploy"></a>部署目標

下列的計算資源可用來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

了解[何處以及如何將模型部署到計算目標](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning 計算 (managed)

建立受控的計算資源，並由 Azure Machine Learning 服務來管理。 此計算被最適合機器學習工作負載。 Azure Machine Learning 計算是自 2019 5 月 30 日起僅受控的計算。 其他受管理的計算資源可能在未來新增。

訓練和批次推斷 （預覽），您可以使用 Azure Machine Learning 計算。  與此計算資源中，您必須：

* 單一或多重 node 叢集
* 自動調整每次提交執行 
* 自動的叢集管理和作業排程 
* 同時支援 CPU 和 GPU 資源

您可以在 Azure 入口網站，使用 SDK，或使用 CLI 建立 Azure Machine Learning 計算執行個體。 建立時自動是您的工作區，不同於其他種類的計算目標的一部分。

## <a name="unmanaged-compute"></a>非受控的計算

非受控的計算目標會在*不*受 Azure Machine Learning 服務。 您建立這種類型的外部 Azure Machine Learning 計算目標，然後將它附加至您的工作區。 非受控的計算資源可能需要額外的步驟，讓您以維護或改善的機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [設定計算目標，來訓練模型](how-to-set-up-training-targets.md)
* [將模型部署到計算目標](how-to-deploy-and-where.md)