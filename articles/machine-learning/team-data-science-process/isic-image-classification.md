---
title: 使用適用於電腦視覺的 Azure Machine Learning (AML) 套件 (AMLPCV) 和 Team Data Science Process (TDSP) 進行影像分類 | Microsoft Docs
description: 說明如何使用 TDSP (Team Data Science Process) 和 AMLPCV 進行影像分類
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 18/05/2018
ms.author: xibingao
ms.openlocfilehash: cfb49f08a245fc08ba3fe78333e801ea2d358c4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367940"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>使用適用於電腦視覺的 Azure Machine Learning (AML) 套件 (AMLPCV) 和 Team Data Science Process (TDSP) 進行皮膚癌影像分類

## <a name="introduction"></a>簡介

本文說明如何使用[適用於電腦視覺的 Azure Machine Learning 套件](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) (AMLPCV) 來定型、測試及部署**影像分類**模型。 此範例會在 [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation) 中使用 TDSP 結構和範本。 本逐步解說中提供完整範例。 它會使用 [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) 作為深度學習架構，並且在[資料科學 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU 機器上執行定型。 部署會使用 Azure ML Operationalization CLI。

許多電腦視覺領域中的應用都可用影像分類問題的角度來構思。 其中包括建置可解答問題的模型，例如：「某物件是否存在於影像中？」 (物件可以是小狗、車子或船隻)，或是更複雜的問題，像是：「此病患的視網膜掃描顯示的眼睛疾病嚴重性等級為何？」 AMLPCV 可簡化影像分類的資料處理與管線模型。 

## <a name="link-to-github-repository"></a>連結至 GitHub 存放庫
我們在此提供範例的相關摘要文件。 在 [GitHub 網站](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)上可以找到更詳盡的文件。

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>使用 AMLPCV 逐步執行 Team Data Science Process (TDSP)

本逐步解說採用 [Team Data Science Process (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 生命週期。

本逐步解說涵蓋下列生命週期步驟：

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1.資料取得](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
影像分類工作會使用 ISIC 資料集。 ISIC (International Skin Imaging Collaboration) 是學界與業界之間的一項合作，旨在推動數位皮膚影像的應用，以期能研究並降低黑色素瘤的死亡率。 [ISIC 封存檔](https://isic-archive.com/#images)包含超過 13000 張標示為良性或惡性的皮膚病灶影像。 我們可從 ISIC 封存檔下載影像範例。

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2.模型化](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
在建立模型的步驟中，會執行下列子步驟。 

<b>2.1 資料集建立</b><br>

若要在 AMLPCV 中產生資料集物件，請提供影像在本機磁碟上的根目錄。 

<b>2.2 影像視覺效果和註釋</b><br>

將資料集物件中的影像視覺化，並視需要更正某些標籤。

<b>2.3 影像增強</b><br>

使用 [imgaug](https://github.com/aleju/imgaug) 程式庫中說明的轉換來增強資料集物件。

<b>2.4 DNN 模型定義</b><br>

定義用於定型步驟中的模型架構。 AMLPCV 支援六個不同的定型式深度類神經網路模型：AlexNet、Resnet-18、Resnet-34、Resnet-50、Resnet-101 和 Resnet-152。

<b>2.5 分類器定型</b><br>

使用預設或自訂參數定型類神經網路。

<b>2.6 評估和視覺效果</b><br>

子步驟提供的功能可在獨立的測試資料集上評估定型模型的效能。 評估計量包括精確度、查準率及查全率，以及 ROC 曲線。

這些子步驟在對應的 Jupyter Notebook 中有詳細說明。 我們也提供了調整參數 (例如學習率、迷你批次大小和中輟率) 以進一步改善模型效能的指引。

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3.部署](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

此步驟會將產生自模型建立步驟的模型運算化。 它會導入運算化的必要條件和設定。 最後，也會說明 Web 服務的運用。 透過本教學課程，您將可了解如何使用 AMLPCV 建置深度學習模型，並在 Azure 中將模型運算化。

## <a name="next-steps"></a>後續步驟
進一步閱讀[適用於電腦視覺的 Azure Machine Learning 套件 (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) 和 [Team Data Science Process (TDSP)](https://aka.ms/tdsp) 的相關文件。


## <a name="references"></a>參考

* [適用於電腦視覺的 Azure Machine Learning 套件 (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [資料科學 VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

