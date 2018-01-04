---
title: "使用小組資料科學程序-Azure word 內嵌預測 Twitter 情緒 |Microsoft 文件"
description: "執行資料科學專案所需的步驟"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: fe1c87df40102a62e1e0c8873b25fa3df7d743bc
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="predict-twitter-sentiment-with-word-embeddings-using-the-team-data-science-process"></a>使用小組資料科學程序的字組內嵌預測 Twitter 情緒

本文將說明如何使用時有效率地共同作業**Word2Vec** word 內嵌演算法和**人氣特定字組內嵌 (SSWE) 演算法**預測與 Twitter 情緒[Azure Machine Learning](../preview/index.yml)。 如需其他詳細資料，預測的工作上 twitter 情緒極性，請參閱[儲存機制](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction)。 促進有效的小組共同作業，在資料科學專案上的索引鍵是標準化的結構與專案建立的資料科學生命週期的文件。 [小組資料科學程序 (TDSP)](overview.md)提供此類結構化[生命週期](lifecycle.md)。 

建立與資料科學專案**TDSP 範本**此標準化的架構提供 Azure 機器學習服務專案。 先前，發行 TDSP 小組有[TDSP 專案結構和範本的 GitHub 儲存機制](https://github.com/Azure/Azure-TDSP-ProjectTemplate)。 現在使用具現化 Azure 機器學習服務專案建立[TDSP 結構和文件範本 Azure Machine Learning](https://github.com/amlsamples/tdsp)已啟用。 如需如何在 Azure 機器學習中使用 TDSP 結構和範本的指示，請參閱[結構與資料科學的小組流程範本的專案](../preview/how-to-use-tdsp-in-azure-ml.md)。 


## <a name="the-sentiment-polarity-sample"></a>情緒極性範例

範例會示範如何具現化並執行機器學習服務專案使用資料科學的小組流程結構，並已在此提供範本中 Azure 機器學習工作工作台[逐步解說](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md)。 預測 （正數或負數） 的人氣極性為模型工作使用推文中的文字。 本文概述資料模型化涵蓋在本逐步解說中的工作。 逐步解說將說明下列工作：

- 機器學習模型的資料探勘、訓練和部署，可處理「使用案例概觀」中所述的預測問題。 基於此目的， [Twitter 情緒資料](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)用。
- 使用 Azure Machine Learning 中此專案的 Team Data Science Process (TDSP) 範本，在 Azure Machine Learning 中執行專案。 針對專案執行和報表，請使用 TDSP 生命週期。
- 直接從 Azure Container Services 中的 Azure Machine Learning 將解決方案運算化。

專案會反白顯示數個的功能 Azure Machine Learning 中，這類 TDSP 結構具現化及使用、 執行程式碼在 Azure 機器學習工作工作台，以及使用 Docker 和 Kubernetes Azure 容器服務中的簡單實施。

## <a name="team-data-science-process"></a>Team Data Science Process
您可以使用 TDSP 專案結構和文件範本來執行此範例。 它會依照[TDSP 生命週期]((https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md))。 專案根據所提供的指示建立[這裡](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)。

![TDSP 生命週期](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

![具現化 TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[資料取得與認知](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
在此範例中的第一個步驟是下載 sentiment140 資料集，並將它分割成定型集和測試資料集。 Sentiment140 資料集包含 （具有移除表情符號） 推文中的實際內容，以及每個推文中的極性 (負數 = 0 時，正 = 4)，以移除中性推文。 產生的定型資料分割，當具有 1.3 百萬個資料列，且測試資料 320 k 資料列。


## <a name="modelinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[模型化](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

此範例的一部分會進一步分成三個部分：
 
- **功能工程**對應至產生的功能使用不同的字組內嵌的演算法。 
- **模型建立**處理不同模型的訓練類似_羅吉斯迴歸_和_梯度促進_預測輸入文字的人氣。 
- **模型評估**測試資料上套用定型的模型。


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[特徵設計](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec 和 SSWE 是內嵌的演算法來產生 word 內嵌的單字。 


#### <a name="word2vec"></a>Word2Vec

Word2Vec 演算法適用於 Skipgram 模式。 產生 word 內嵌的這種方式會說明本文所 Tomas Mikolov 等等：[分散式文字表示法和片語和其複合性。類神經資訊處理系統中的進階功能。2013.](https://arxiv.org/abs/1310.4546).

略過字母組是淺層的類神經網路。 它的輸入是編碼為一個熱向量，這是用來預測字附近目標文字。 如果**V**是詞彙的大小，則輸出層的大小會是__C * V__其中 C 是內容視窗的大小。 略過字母組基礎架構會如下圖所示：

![略過觀察模型](./media/predict-twitter-sentiment/skip-gram-model.PNG)

***略過觀察模型***

Word2vec 演算法並略過觀察模型的詳細的機制等已超出此範例的範圍。 讀取器所需的更多有關其運作方式的詳細資料可參閱下列參考：

- [程式碼 02_A_Word2Vec.py 參考 TensorFlow 範例](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)
- [向量的文字表示法](https://www.tensorflow.org/tutorials/word2vec)
- [Word2vec 確切方式運作？](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [附註的雜訊 Contrastive 估計和負的取樣](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)


#### <a name="sswe"></a>SSWE
**人氣特定字組內嵌 (SSWE) 演算法**嘗試克服 Word2vec 演算法與類似的內容，and 相反的極性可以有類似的 word 向量的弱點。 此相似性，表示 Word2vec 可能不執行正確的工作，例如情緒分析。 SSWE 演算法會嘗試處理此弱點句子極性和 word 的內容納入其損失函數。

這個範例會使用 SSWE 的變化。 SSWE 使用這兩個原始 ngram 而損毀的 ngram 做為輸入，而且它會使用排名樣式推銷遺失語法和語意遺失的損失函數。 最終的損失函數是加權的遺失語法和語意遺失的組合。 為了簡單起見，只有語意交叉 entropy 做為損失函數。 您稍後看到，即使有這個簡單的損失函數 SSWE 內嵌的效能勝過 Word2Vec 內嵌。

下圖顯示您在此範例中使用 SSWE 啟發的類神經網路模型：

![ROC 模型比較](./media/predict-twitter-sentiment/embedding-model-2.PNG)

***Convolutional 產生人氣特有的斷內嵌模型。***


在定型程序完成之後，以 TSV 格式的兩個內嵌檔案會產生的模型化階段。

如需 SSWE 演算法的詳細資訊，請參閱文件由 Duyu Tang 等等：[學習人氣特定 Twitter 情緒分類為內嵌的文字。ACL (1)。2014.](http://www.aclweb.org/anthology/P14-1146) 


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[模型的建立](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
一旦已使用其中一個 SSWE 或 Word2vec 演算法產生 word 向量下, 一個步驟是定型分類模型來預測實際的人氣極性。 兩種類型的功能，Word2Vec 與 SSWE，則會套用到兩個模型，GBM 模型和羅吉斯迴歸模型。 定型因此四種不同的模型。


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[模型評估](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
現在您可以使用四種測試資料中的上一個步驟中定型的模型來評估模型的效能。 

1. 漸層停駐 Boosting 透過 SSWE 內嵌
2. 透過 SSWE 內嵌羅吉斯迴歸
3. 漸層停駐 Boosting 透過 Word2Vec 內嵌
4. 透過 Word2Vec 內嵌羅吉斯迴歸

![ROC 模型比較](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

SSWE 功能 GBM 模型就是最佳使用 AUC 度量。


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[部署](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

此組件會將定型的人氣預測模型 （SSWE 內嵌 + GBM 模型） 部署到 Azure 容器服務 (ACS) 中的叢集上的 web 服務。 運算化環境會在叢集中佈建 Docker 和 Kubernetes，以管理 Web 服務部署。 您可以在[這裡](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)找到有關運算化程序的進一步資訊。

![kubenetes_dashboard](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)


## <a name="conclusion"></a>結論

解釋如何 word 內嵌使用定型模型 Word2Vec 和 SSWE 演算法的詳細資料，並擷取的內嵌用做為特徵來定型數個模型來預測 Twitter 文字資料的人氣分數。 梯度促進式樹狀結構模型的人氣特定用語 Embeddings(SSWE) 功能會提供最佳的效能。 然後為即時 web 服務使用 Azure 機器學習工作的工作台 Azure 容器服務中部署此模型。


## <a name="references"></a>參考

* [資料科學的小組流程](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [如何在 Azure Machine Learning 中使用 Team Data Science Process (TDSP)](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning 的 TDSP 專案範本](https://aka.ms/tdspamlgithubrepo)
* [Azure ML 工作工作台](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [UCI ML存放庫中的美國所得資料集](https://archive.ics.uci.edu/ml/datasets/adult)
* [使用 TDSP 範本 biomedical 實體辨識](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov，Tomas，box。Distributed representations of words and phrases and their compositionality.類神經資訊處理系統中的進階功能。2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu，box。「 學習人氣特有的斷內嵌 Twitter 情緒分類 」。ACL (1)。2014.](http://www.aclweb.org/anthology/P14-1146)