---
title: 使用 Azure Machine Learning 設計工具執行批次預測 (預覽)
titleSuffix: Azure Machine Learning
description: 了解如何使用設計工具來定型模型和設定批次預測管線。 將管線部署為可從任何 HTTP 程式庫觸發的參數化 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: c496e57ea5f4b5b7fcda4f9c43c511488ef3c246
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509432"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>使用 Azure Machine Learning 設計工具執行批次預測
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在此操作說明中，您將了解如何使用設計工具來定型模型，以及設定批次預測管線和 Web 服務。 批次預測可讓您對大型資料集的定型模型進行連續和隨選評分，並選擇性地將其設定為可從任何 HTTP 程式庫觸發的 Web 服務。 

若要使用 SDK 來設定批次評分服務，請參閱隨附的[操作說明](how-to-run-batch-predictions.md)。

在此操作說明中，您將了解下列工作：

> [!div class="checklist"]
> * 在管線中建立基本 ML 實驗
> * 建立參數化批次推斷管線
> * 以手動方式或從 REST 端點管理和執行管線

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

1. 建立[工作區](tutorial-1st-experiment-sdk-setup.md)。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。

此操作說明假設您具備在設計工具中建置簡單管線的基本知識。 如需設計工具的導引簡介，請完成[教學課程](tutorial-designer-automobile-price-train-score.md)。 

## <a name="create-a-pipeline"></a>建立管線

若要建立批次推斷管線，首先必須要有機器學習實驗。 若要建立此實驗，請瀏覽至工作區中的 [設計工具]  索引標籤，然後選取 [易於使用的預建模組]  選項以建立新的管線。

![設計工具首頁](media/how-to-run-batch-predictions-ui/ui-batch-scoring-1.png)

以下是供示範用的簡單機器學習模型。 其資料是從 Azure 開放資料集糖尿病資料建立的已註冊資料集。 如需從 Azure 開放資料集註冊資料集的相關資訊，請參閱[操作說明](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)一節。 資料分成定型集和驗證集，系統會定型和評分促進式決策樹。 管線必須至少執行一次，才能夠建立推斷管線。 按一下 [執行]  按鈕以執行管線。

![建立簡單實驗](media/how-to-run-batch-predictions-ui/ui-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>建立批次推斷管線

在管線執行後，[執行]  和 [發佈]  旁會有一個名為「建立推斷管線」  的新選項可供使用。 按一下下拉式清單，並選取 [批次推斷管線]  。

![建立批次推斷管線](media/how-to-run-batch-predictions-ui/ui-batch-scoring-5.png)

結果將是預設批次推斷管線。 這包括適用於原始管線實驗設定的節點、用來評分原始資料的節點，以及用來對原始管線的原始資料進行評分的節點。

![預設批次推斷管線](media/how-to-run-batch-predictions-ui/ui-batch-scoring-6.png)

您可以新增其他節點，以變更批次推斷程序的行為。 在此範例中，您會新增一個節點，以在評分之前從輸入資料中隨機取樣。 建立**資料分割和取樣**節點，並將它放在原始資料和評分節點之間。 接著，按一下**資料分割和取樣**節點，以取得設定和參數的存取權。

![新節點](media/how-to-run-batch-predictions-ui/ui-batch-scoring-7.png)

*取樣率*參數會控制要從原始資料集中進行隨機取樣的資料百分比。 此參數對於頻繁的調整很有幫助，因此您可將其啟用作為管線參數。 管線參數可在執行階段變更，而且可以在從 REST 端點重新執行管線時指定於承載物件中。 

若要啟用此欄位作為管線參數，請按一下欄位上方的省略符號，然後按一下 [新增至管線參數]  。 

![樣本設定](media/how-to-run-batch-predictions-ui/ui-batch-scoring-8.png)

接著，請為參數指定名稱和預設值。 此名稱將用來識別參數，並將其指定於 REST 呼叫中。

![管線參數](media/how-to-run-batch-predictions-ui/ui-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>部署批次推斷管線

現在您已經準備好部署管線。 按一下 [部署]  按鈕，這會開啟用來設定端點的介面。 按一下下拉式清單，並選取 [新增 PipelineEndpoint]  。

![管線部署](media/how-to-run-batch-predictions-ui/ui-batch-scoring-10.png)

請為端點指定名稱和選擇性的描述。 在底部附近，您會看到您使用預設值 0.8 設定的 `sample-rate` 參數。 在準備就緒後，按一下 [部署]  。

![設定端點](media/how-to-run-batch-predictions-ui/ui-batch-scoring-11.png)

## <a name="manage-endpoints"></a>管理端點 

部署完成後，請移至 [端點]  索引標籤，然後按一下您剛才建立的端點名稱。

![端點連結](media/how-to-run-batch-predictions-ui/ui-batch-scoring-12.png)

此畫面會顯示特定端點下所有已發佈的管線。 按一下您的推斷管線。

![推斷管線](media/how-to-run-batch-predictions-ui/ui-batch-scoring-13.png)

管線詳細資料頁面會顯示管線的詳細執行歷程記錄和連接字串資訊。 按一下 [執行]  按鈕，以建立管線的手動執行。

![管線詳細資料](media/how-to-run-batch-predictions-ui/ui-batch-scoring-14.png)

在執行設定中，您可以提供執行的描述，並變更任何管線參數的值。 這次，請以 0.9 的採樣速率重新執行推斷管線。 按一下 [執行]  以執行管線。

![管線執行](media/how-to-run-batch-predictions-ui/ui-batch-scoring-15.png)

[取用]  索引標籤會包含用來重新執行管線的 REST 端點。 若要進行 Rest 呼叫，您將需要 OAuth 2.0 Bearer-type 驗證標頭。 若要進一步了解如何設定對工作區進行驗證以及如何進行參數化 REST 呼叫，請參閱下列[教學課程章節](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>後續步驟

依照設計工具[教學課程](tutorial-designer-automobile-price-train-score.md)的指示來定型和部署迴歸模型。