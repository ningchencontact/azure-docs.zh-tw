---
title: 評分模型：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的 「 評分模型 」 模組，利用定型的分類或迴歸模型來產生預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029261"
---
# <a name="score-model-module"></a>Score Model module

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組，利用定型的分類或迴歸模型來產生預測。

## <a name="how-to-use"></a>使用方式

1. 新增**評分模型**模組至您的實驗。

2. 將定型的模型和包含新的輸入的資料的資料集的連接。 

    資料應該是與您使用的定型模型的類型相容的格式。 輸入資料集的結構描述通常也應符合用來定型模型的資料的結構描述。

3. 執行實驗。

## <a name="results"></a>結果

當您產生一組分數，使用後[評分模型](./score-model.md):

+ 若要產生一組用來評估模型的精確度 （效能） 的度量。  您可以連接來評分資料集會[評估模型](./evaluate-model.md)， 
+ 以滑鼠右鍵按一下模組並選取**視覺化**來查看結果的範例。
+ 將結果儲存至資料集。

分數或預測的值可以是許多不同的格式，視模型和輸入的資料而定：

- 針對分類模型，[評分模型](./score-model.md)輸出類別，以及預測值之機率的預測的值。
- 對於迴歸模型，請[評分模型](./score-model.md)產生只預測的數字值。
- 影像分類模型而言，分數可以是映像或布林值，指出是否找到特定特徵中物件的類別。

## <a name="publish-scores-as-a-web-service"></a>發佈為 web 服務的分數

計分的一般用途是預測性 web 服務的一部分傳回的輸出。 如需詳細資訊，請參閱有關如何建立在 Azure Machine Learning 實驗為基礎的 web 服務的本教學課程：


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 