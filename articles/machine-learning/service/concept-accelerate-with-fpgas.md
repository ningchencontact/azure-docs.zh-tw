---
title: 什麼是 FPGA 和 Project Brainwave？
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 上使用 FPGA 為模型和深度類神經網路加速。 本文將介紹可現場程式化閘道陣列 (FPGA)，以及 Azure Machine Learning 服務如何在您將模型部署至 Azure FPGA 時提供即時人工智慧 (AI)。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 1/10/2019
ms.custom: seodec18
ms.openlocfilehash: 716de6d491be753c5c32c0a2774404140010f72c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821223"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>什麼是 FPGA 和 Project Brainwave？

本文將介紹可現場程式化閘道陣列 (FPGA)，以及 Azure Machine Learning 服務如何在您將模型部署至 Azure FPGA 時提供即時人工智慧 (AI)。

FPGA 包含可程式化邏輯區塊的陣列，以及可重新設定互連的階層。 互連可讓您在製造之後以不同方式設定這些區塊。 與其他晶片相比，FPGA 結合了可程式性和效能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 與CPU、GPU、ASIC 的比較

下列圖表示範 FPGA 與其他處理器的比較方式。

![Azure Machine Learning 服務 FPGA 比較圖](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|處理器||描述|
|---|:-------:|------|
|應用程式特定的積體電路|ASIC|自訂電路 (例如，Google 的 TensorFlow 處理器 (TPU)) 可提供最高效率。 它們無法隨著您需求的變更加以重新設定。|
|現場可程式化閘陣列|FPGA|FPGA (例如 Azure 上所提供的那些陣列) 可提供接近 ASIC 的效能。 它們也會隨著時間而具有彈性且可重新設定，以實作新邏輯。|
|圖形處理器|GPU|這是頗受歡迎的 AI 計算選擇。 由於 GPU 能提供平行處理功能，因而使得它在轉譯影像的速度會比 CPU 還快。|
|中央處理器|CPU|一般用途的處理器，其效能不適合圖形和影片處理。|

## <a name="project-brainwave-on-azure"></a>Azure 的 Project Brainwave

[Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) \(英文\) 是 Microsoft 所提供的硬體架構。 它會以 Intel 的 FPGA 裝置為基礎，讓資料科學家和開發人員可用來加速即時 AI 計算。 這個具有 FPGA 功能的架構可提供效能、彈性和擴展能力，並且可在 Azure 上使用。

FPGA 可以實現低延遲的即時推斷請求。 不需要非同步要求 (批次處理)。 批次處理可能會造成延遲 (因為需要處理更多資料)。 使用類神經處理器的 Project Brainwave 實作不需要批次處理；因此，其延遲比 CPU 和 GPU 處理器低好幾倍。

### <a name="reconfigurable-power"></a>可重新設定的電源
您可以針對不同類型的機器學習模型重新設定 FPGA。 擁有此種彈性，就更易於依據最佳的數值有效位數和所用的記憶體模型來加速應用程式。 由於 FPGA 可重新設定，因此您可以與 AI 演算法快速變化的需求保持同步。

### <a name="whats-supported-on-azure"></a>Azure 上所支援的項目
Microsoft Azure 是 FPGA 全球最大的雲端投資。 您可以在 Azure 的縮放基礎結構上執行 Project Brainwave。

目前，Project Brainwave 支援：
+ 影像分類和辨識案例
+ TensorFlow 部署
+ DNN：ResNet 50、ResNet 152、VGG-16、SSD-VGG 和 DenseNet-121
+ Intel FPGA 硬體 

使用這個具有 FPGA 功能的硬體架構，已定型的類神經網路執行速度會更快，延遲也較低。 Project Brainwave 可以在 FPGA 上平行處理預先定型的深度類神經網路 (DNN)，相應放大您的服務。 DNN 可以預先定型為深度 Featurizer 以傳輸學習，也可以使用更新過的加權來微調。

### <a name="scenarios-and-applications"></a>案例和應用程式

Project Brainwave 會與 Azure Machine Learning 整合。 Microsoft 使用 FPGA 來評估 DNN、Bing 搜尋順位排序，以及軟體定義網路 (SDN) 加速來減少延遲，同時釋放 CPU 來執行其他工作。

以下為在 Project Brainwave 架構上使用 FPGA 的案例：
+ [自動化光學檢查系統](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [地表覆蓋對應](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>部署到 Azure 上的 FPGA

若要在 Azure 中建立影像辨識服務，您可以使用支援的 DNN 作為功能化器，以將服務部署至 Azure FPGA：

1. 使用[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 來建立服務定義。 服務定義是根據 TensorFlow 來描述圖形管線 (輸入、功能化器和分類器) 的檔案。 部署命令會自動將定義和圖表壓縮為 ZIP 檔案，然後將 ZIP 檔案上傳到 Azure Blob 儲存體。 DNN 已部署於 Project Brainwave 以便在 FPGA 上執行。

1. 使用 SDK 搭配 Azure Blob 儲存體中的 ZIP 檔案來註冊模型。

1. 使用 SDK 來部署含有已註冊模型的服務。

若要開始將定型的 DNN 模型部署到 Azure 雲端的 FPGA，請參閱[在 FPGA 上將模型部署為 Web 服務](how-to-deploy-fpga-web-service.md)。


## <a name="next-steps"></a>後續步驟

請參閱這些影片與部落格：

+ [超大規模資料庫的硬體：Azure + FPGA 上大規模的 ML：組建 2018 (影片)](https://www.youtube.com/watch?v=BMgQAHIx2eY) \(英文\)

+ [深入了解 Microsoft FPGA 架構的可設定雲端 (英文影片)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [適用於即時 AI 的 Project Brainwave：專案首頁](https://www.microsoft.com/research/project/project-brainwave/)
