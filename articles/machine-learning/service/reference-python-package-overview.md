---
title: 適用於 Azure Machine Learning 的 Python 套件
description: 了解適用於 Azure Machine Learning 使用者的 Python 套件。
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7a8f9e9d757aaab04706dd3b7dc2b962b3a04130
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833258"
---
# <a name="python-packages-for-azure-machine-learning"></a>適用於 Azure Machine Learning 的 Python 套件

從 Microsoft Azure Machine Learning 了解專屬的 Python 套件。 您可以使用這些程式庫和函數搭配其他開放原始碼或協力廠商套件。但若要使用專屬的套件，您的 Python 程式碼必須針對服務或在提供解譯器的電腦上執行。

Azure Machine Learning 套件是**適用於 Azure Machine Learning 的 Python 可安裝 pip 的延伸模組**，可讓資料科學家和 AI 開發人員快速建置和部署高度精確的機器學習和各種網域的深入學習模型。

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>適用於電腦視覺的 Azure ML 套件

您可以使用適用於電腦視覺的 Azure ML 套件，建置、微調和部署影像分類、物件偵測和影像相似度的深入學習模型。

針對此套件嘗試以下後續步驟：
1. [下載](https://aka.ms/aml-packages/vision/download)套件。
1. 閱讀[安裝文件](https://aka.ms/aml-packages/vision)。
1. 使用 Jupyter Notebook [建置和部署電腦視覺模型](how-to-build-deploy-image-classification-models.md)。
1. 瀏覽此套件的[參考文件](https://aka.ms/aml-packages/vision)。

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>適用於預測的 Azure ML 套件

您可以使用適用於預測的 Azure ML 套件，針對財務和需求預測案例，建立並部署時間序列預測模型。

針對此套件嘗試以下後續步驟：
1. [下載](https://aka.ms/aml-packages/forecasting/download)套件。
1. 閱讀[安裝文件](https://aka.ms/aml-packages/forecasting)。
1. 使用 Jupyter Notebook [建置和部署預測模型](how-to-build-deploy-forecast-models.md)。
1. 瀏覽此套件的[參考文件](https://aka.ms/aml-packages/forecasting)。

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>適用於文字分析的 Azure ML 套件

您可以使用適用於文字分析的 Azure ML 套件，建置文字分類、自訂實體擷取和字組內嵌的深入學習模型。

針對此套件嘗試以下後續步驟：
1. [下載](https://aka.ms/aml-packages/text/download)套件。
1. 閱讀[安裝文件](https://aka.ms/aml-packages/text)。
1. 使用 Jupyter Notebook [建置和部署文字分類模型](how-to-build-deploy-text-classification-models.md)。
1. 瀏覽此套件的[參考文件](https://aka.ms/aml-packages/text)。

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (FPGA 加速)

資料科學家和 AI 開發人員可以使用 Azure Machine Learning 硬體加速套件，利用量化版本的 ResNet 50 將影像功能化、根據這些功能為分類器定型，然後在 Azure 上，將模型部署至[現場可程式化閘陣列 (FPGA)](concept-accelerate-with-fpgas.md)，以達到超低延遲推斷。

針對此套件嘗試以下後續步驟：
1. [下載](https://aka.ms/aml-real-time-ai-package)套件。
1. 閱讀[安裝文件](reference-fpga-package-overview.md)。
1. [將模型部署到 FPGA](how-to-deploy-fpga-web-service.md)。

