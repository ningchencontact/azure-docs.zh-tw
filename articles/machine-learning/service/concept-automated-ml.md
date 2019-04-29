---
title: 選取與微調自動化 ML 演算法
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務如何自動為您挑選演算法，以及如何藉由使用您提供來為模型選取最佳演算法的參數與準則，從其中產生模型來節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 620dbd22613df37fdc3c20e34906684446b2251f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821179"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化機器學習是一個程序，可使用所定義的目標功能來取得定型資料，並逐一查看演算法和功能選取項目的組合，以根據定型分數自動為您的資料選取最佳模型。 傳統的機器學習模型開發程序會耗費大量資源，而且需要投入大量的網域知識和時間來執行並比較數十個模型的結果。 自動化機器學習會藉由產生從您為實驗定義的目標和條件約束中進行微調的模型來簡化此程序，例如，執行實驗的時間，或是要將哪些模型列入封鎖清單。

## <a name="how-it-works"></a>運作方式

1. 您設定嘗試要解決的機器學習服務問題類型。 支援受監督的學習類別：
   + 分類
   + 迴歸
   + 預測

   雖然自動化機器學習一般可供使用，但**預測功能仍處於公開預覽狀態。**

   查看 Azure Machine Learning 可在定型時嘗試的[模型清單](how-to-configure-auto-train.md#select-your-experiment-type)。

1. 您指定用於將資料定型的來源與格式。 資料必須要加上標籤，而且儲存在您的開發環境或 Azure Blob 儲存體中。 如果資料儲存在開發環境上，它必須位於與您的定型指令碼相同的目錄。 此目錄會複製到您為定型選取的計算目標。

    在定型指令碼中，可以將資料讀入 Numpy 陣列或 Pandas 資料框架。

    您可以設定選取定型和驗證資料的分割選項，或可以指定不同的定型和驗證資料集。

1. 設定用來將模型定型的[計算目標](how-to-set-up-training-targets.md)。

1. 設定自動化機器學習設定。 這可以控制當 Azure Machine Learning 逐一查看不同的模型、超參數設定，以及在決定最佳模型時要查看哪些計量時會使用的參數。

1. 提交定型回合。

在定型期間，Azure Machine Learning 服務會建立數個管道，這些管道分別會嘗試不同的演算法與參數。 在達到您提供的反覆項目限制之後，或達到您為計量指定的目標值時，它隨即停止。

[![自動化的機器學習服務](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

您可以檢查所記錄的回合資訊，其中包含回和執行期間所收集的計量。 定型回合也會產生 Python 序列化物件 (`.pkl` 檔案)，其中包含模型與資料前置處理。


## <a name="next-steps"></a>後續步驟

查看範例並了解如何使用自動化機器學習服務來建置模型：

+ [教學課程：使用自動化的 Azure Machine Learning 自動將分類模型定型](tutorial-auto-train-models.md)

+ [Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

+ [在遠端資源使用自動定型](how-to-auto-train-remote.md)

+ [設定自動定型的設定](how-to-configure-auto-train.md)
