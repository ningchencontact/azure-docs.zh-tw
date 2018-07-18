---
title: 什麼是 FPGA？ – Project Brainwave – Azure Machine Learning
description: 了解如何使用 FPGA 為模型和深度類神經網路加速。
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5ed94c3b750c927ec48959c12388bd22de3d3df4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261945"
---
# <a name="what-is-fpga-and-project-brainwave"></a>什麼是 FPGA 和 Project Brainwave？

本文所介紹的是現場可程式化閘陣列 (FPGA)，並說明 FPGA 如何與 Azure Machine Learning 整合以提供即時 AI。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 與CPU、GPU、ASIC 的比較

FPGA 包含可程式化邏輯區塊的陣列，以及可重新設定互連的階層，以便在製造之後再以不同方式設定這些區塊。

與其他晶片相比，FPGA 結合了可編程性和效能：

![Azure Machine Learning FPGA 比較](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **中央處理器 (CPU)** 是一般用途的處理器。 CPU 效能不適合圖形和影片處理。
- **圖形處理器 (GPU)** 具有平行處理能力，且是 AI 運算的熱門選擇。 以 GPU 平行處理的映像轉譯速度比 CPU 更快。
- **應用程式專屬積體電路 (ASIC)** 為自訂電路，例如 Google 的 TensorFlow 處理器。 雖然其為效率最高的晶片，但 ASIC 卻缺乏彈性。
- **FPGA** 可提供接近 ASIC 的效能，也能提供之後再加以重新設定的靈活度，Azure 所提供的即為此類。

現在每個新的 Azure 伺服器都搭載了 FPGA。 Microsoft 已將 FPGA 使用在 Bing 的搜尋順位排序、類神經網路 (DNN) 評估以及軟體定義網路 (SDN) 加速上。 這類 FPGA 實作可減少延遲，同時釋出 CPU 以用於其他工作。

## <a name="project-brainwave-on-azure"></a>Azure 的 Project Brainwave

Project Brainwave 是以 Intel FPGA 裝置為基礎所設計的硬體架構，用於加快即時 AI 的運算速度。 這種已採用 FPGA 的架構相當經濟實惠，若運用此種架構，定型的類神經網路便能以最快的速度執行，且可降低延遲。 Project Brainwave 可以在 FPGA 上平行處理預先定型的 DNN，相應放大您的服務。

- 效能

    FPGA 可以實現低延遲的即時推斷請求。 批次處理意即把請求分成更小的部分並饋送至處理器，藉此改善硬體使用率。 批次處理效率不高，而且可能會導致延遲。 Brainwave 不需要批次處理，因此延遲度比 CPU 和 GPU 低 10 倍。

- 彈性

    FPGA 可以重新設定為不同類型的機器學習模型。 擁有此種彈性，就更易於依據最佳的數值有效位數和所用的記憶體模型來加速應用程式。

    目前正在定期開發新的機器學習技術，Project Brainwave 的硬體設計也在迅速發展。 由於 FPGA 可重新設定，因此可與 AI 演算法快速變化的需求保持同步。

- 調整

    Microsoft Azure 是 FPGA 全球最大的雲端投資。 您可以在 Azure 的縮放基礎結構上執行 Brainwave。

目前提供有 Project Brainwave 與 Azure Machine Learning 整合的預覽版本。 也提供有限的預覽，能將 Project Brainwave 推上前線，讓您可以在自己的業務和設施中運用其所帶來的運算速度。

在目前的預覽中，Brainwave 僅限於 Intel FPGA 硬體上的 TensorFlow 部署和 ResNet50 架構類神經網路，用於影像分類和辨識。 也有計畫要支援更多資源庫模型和其他架構。

以下為在 Project Brainwave 架構上使用 FPGA 的案例：

- 自動化光學檢查系統。 請參閱[即時 AI：Microsoft 發表 Project Brainwave 的預覽 (英文)](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)。
- 地表覆蓋對應。 請參閱[如何使用 FPGA 進行深度學習推斷，以在 TB 為單位的空照圖影像上執行地表覆蓋對應](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/) (英文)。

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>如何將 Web 服務部署到 FPGA？

使用 ResNet50 作為 Featurizer，在 Azure 中建立影像識別服務的高階流程則如下所示：

1. ResNet50 已部署在 Brainwave 內。 您可以使用 TensorFlow 建立其他圖表 (日期輸入、分類等)，並使用服務定義 json 檔案來定義管線 ([輸入] -> [特徵化] -> [分類])。 將定義和圖表壓縮為 zip 檔案，然後將 zip 檔案上傳到 Azure Blob 儲存體。
2. 使用 Azure ML 模型管理 API 與 Blob 儲存體中的 zip 檔案註冊模型。
3. 使用 Azure ML 模型管理 API 部署帶有註冊模型的服務。

在[使用 Azure Machine Learning 在 FPGA 上將模型部署為 Web 服務 (英文)](how-to-deploy-fpga-web-service.md) 一文中，可深入了解此程序。


## <a name="start-using-fpga"></a>開始使用 FPGA

Azure Machine Learning 硬體加速模型可讓您將定型的 DNN 模型部署到 Azure 雲端中的 FPGA。 若要開始使用，請參閱：

- [在 FPGA 上將模型部署為 Web 服務](how-to-deploy-fpga-web-service.md)
- [Project Brainwave 提供的 Microsoft Azure Machine Learning 硬體加速模型](https://github.com/azure/aml-real-time-ai)。

## <a name="next-steps"></a>後續步驟

[在 FPGA 上將模型部署為 Web 服務](how-to-deploy-fpga-web-service.md)

[了解如何安裝 FPGA SDK](reference-fpga-package-overview.md)

[超大規模硬體：Azure + FPGA 頂級規模的 ML：2018 組建 (英文影片)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[深入了解 Microsoft FPGA 架構的可設定雲端 (英文影片)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft 推出即時 AI 的 Project Brainwave (英文)](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)