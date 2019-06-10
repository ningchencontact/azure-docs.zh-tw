---
title: 計算目標
titleSuffix: Azure Machine Learning service
description: 計算目標可讓您指定您執行您的定型指令碼或主機服務部署的計算資源。 此位置可能是您的本機或雲端式計算資源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755346"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>什麼是 Azure Machine Learning 服務中的計算目標？ 

計算目標可讓您指定您執行您的定型指令碼或主機服務部署的計算資源。 此位置可能是您的本機或雲端式計算資源。

計算目標，讓您輕鬆變更計算環境，而不需要變更您的程式碼。  典型的模型開發生命週期中：

* 開始開發/測試少量的資料。 在這個階段，我們建議使用本機環境。 例如，您的本機電腦或雲端式虛擬機器。
* 相應增加您的訓練，在較大的資料集，或執行分散式訓練課程使用其中一種[訓練目標](#train)。  
* 將部署至數個 web 主控的環境中，或使用其中一種的 IoT 裝置[部署目標](#deploy)。

您使用的計算目標的計算資源會附加至[工作區](concept-workspace.md)。 計算的工作區的使用者會共用在本機電腦以外的資源。

## <a name="train"></a> 訓練目標

Azure Machine Learning 服務有不同的支援跨不同的計算資源。  您也可以附加您自己的計算資源，不過支援的各種情節可能會有所不同，詳述如下：

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>部署目標

下列的計算資源可用來裝載您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>受控的運算

建立受控的計算資源，並由 Azure Machine Learning 服務來管理。 此計算被最適合機器學習工作負載。 Azure Machine Learning 計算是自 2019 5 月 30 日起僅受控的計算。 其他受管理的計算資源可能在未來新增。

### <a name="amlcompute"></a> Azure Machine Learning 計算

訓練和批次推斷 （預覽），您可以使用 Azure Machine Learning 計算。  與此計算資源中，您必須：

* 單一或多重 node 叢集
* 自動調整每次提交執行 
* 自動的叢集管理和作業排程 
* 同時支援 CPU 和 GPU 資源

您可以使用下列其中一項來建立 Azure Machine Learning 計算執行個體：

* Azure 入口網站
* Azure Machine Learning SDK
* Azure CLI

所有其他計算資源必須建立工作區外，並接著附加到它。

## <a name="unmanaged-compute"></a>非受控的計算

非受控的計算資源*不*受 Azure Machine Learning 服務。 您建立這種類型的外部 Azure Machine Learning 計算，然後將它附加至您的工作區。 非受控的計算資源可能需要額外的步驟，讓您以維護或改善的機器學習工作負載的效能。

## <a name="next-steps"></a>後續步驟

* [設定用於定型模型的計算目標](how-to-set-up-training-targets.md)
* [部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)