---
title: "在 Azure 中使用小組資料科學程序預測與 word 內嵌 Twitter 情緒 |Microsoft 文件"
description: "所需的步驟執行您的資料科學專案。"
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
ms.openlocfilehash: 20bc3f31897cec4a3cec9ca409062229133102f5
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>預測與 word 內嵌 Twitter 情緒使用小組資料科學程序

本文章將示範如何使用有效率地共同作業_Word2Vec_ word 內嵌演算法和_人氣特定字組內嵌 (SSWE)_演算法來預測與 Twitter 情緒[Azure Machine Learning](../preview/index.yml)。 如需有關預測 Twitter 情緒極性的詳細資訊，請參閱[MachineLearningSamples TwitterSentimentPrediction 儲存機制](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction)GitHub 上。 促進有效的小組共同作業，在資料科學專案上的索引鍵是標準化的結構和文件的專案，以建立的資料科學生命週期。 [小組資料科學程序 (TDSP)](overview.md)提供這種類型的結構化[生命週期](lifecycle.md)。 

建立與資料科學專案_TDSP 範本_提供的標準的架構，Azure 機器學習服務專案。 先前，TDSP 小組發行[TDSP 專案結構和範本的 GitHub 儲存機制](https://github.com/Azure/Azure-TDSP-ProjectTemplate)。 現在使用具現化機器學習服務專案[Azure 機器學習 TDSP 範本](https://github.com/amlsamples/tdsp)已啟用。 如需指示，請參閱 < 如何使用[TDSP 結構專案 TDSP 範本](../preview/how-to-use-tdsp-in-azure-ml.md)Azure Machine Learning 中。 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter 情緒極性範例

本文會使用範例，示範如何具現化並執行機器學習服務專案。 這個範例會使用在 Azure 機器學習 Workbench TDSP 結構和範本。 中提供完整的範例[本逐步解說](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md)。 模型化工作預測 （正數或負數） 的人氣極性使用推文中的文字。 本文概述逐步解說中所述的資料模型化工作。 逐步解說將說明下列工作：

- 資料瀏覽、 定型和部署的機器學習模型，就會處理預測問題使用案例概觀中所述。 [Twitter 情緒資料](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)用於這些工作。
- 執行使用此專案從 Azure Machine Learning TDSP 範本專案。 針對專案執行和報表，請使用 TDSP 生命週期。
- Operationalization 直接從 Azure Machine Learning Azure 容器服務中的方案。

專案會反白顯示 Azure 機器學習的下列功能：

- 具現化及使用 TDSP 結構。
- Azure 機器學習工作臺 中的程式碼執行。
- 容易使用 Docker 和 Kubernetes 容器服務中實施。

## <a name="team-data-science-process"></a>Team Data Science Process
若要執行此範例中，您可以使用 TDSP 專案結構和文件範本 Azure 機器學習工作臺 中。 此範例會實作[TDSP 生命週期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)，如下圖所示：

![TDSP 生命週期](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

根據 Azure 機器學習工作臺 中建立 TDSP 專案[這些指示](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)，如下圖所示：

![在 Azure 機器學習 Workbench 建立 TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[資料擷取和準備](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
在此範例中的第一個步驟是下載 sentiment140 資料集，並將資料分成定型集和測試資料集。 Sentiment140 資料集包含推文中的實際內容 （含移除表情符號）。 資料集也包含每個推文極性 (負數 = 0 時，正 = 4) 使用中性推文中移除。 資料分割之後，定型資料 1.3 百萬個資料列而且測試的資料有 320,000 資料列。


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[開發模型](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

此範例的下一個步驟是開發模型的資料。 模型化工作分成三個部分：

- 功能工程： 使用不同的字組內嵌演算法產生模型的功能。 
- 模型建立： 訓練不同的模型來預測輸入文字的人氣。 這些模型的範例包括_羅吉斯迴歸_和_梯度促進_。
- 模型評估： 評估測試資料的定型的模型。


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[特徵設計](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec 和 SSWE 演算法會用來產生 word 內嵌。 


#### <a name="word2vec-algorithm"></a>Word2Vec 演算法

Word2Vec 演算法會略過字母組模型中使用。 此模型會說明本文由 Tomas Mikolov box。「[分散式的單字和片語以及其複合性表示法。類神經資訊處理系統中的進階功能。](https://arxiv.org/abs/1310.4546)" 2013.

略過觀察模型是淺層的類神經網路。 輸入是編碼為一個熱向量，用來預測字附近的目標文字。 如果**V**是詞彙的大小，則輸出層的大小是__C * V__其中 C 是內容視窗的大小。 下圖顯示略過字母組模型為基礎的架構：

![略過觀察模型](./media/predict-twitter-sentiment/skip-gram-model.PNG)

詳細的 Word2Vec 演算法和略過觀察模型的機制等已超出此範例的範圍。 如需詳細資訊，請參閱下列參考：

- [02_A_Word2Vec.py 參考 TensorFlow 範例程式碼](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
- [Word2vec 確切方式運作？](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [附註的雜訊 Contrastive 估計和負的取樣](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>人氣特定字組內嵌的演算法
SSWE 演算法會嘗試解決其中的文字與類似的內容，並相反的極性可以有類似的 word 向量 Word2Vec 演算法的弱點。 相似之處，可能會造成不精確地執行的工作，例如情緒分析 Word2Vec 演算法。 SSWE 演算法會嘗試處理此弱點句子極性和 word 的內容納入其損失函數。

此範例會使用 SSWE 演算法的變化，如下所示：

- 這兩個原始_ngram_和損毀_ngram_當做輸入使用。
- A 排名樣式鉸鏈遺失函式以用於遺失語法和語意遺失。
- Ultimate 損失函數是語法遺失和語意損失的加權的組合。
- 為了簡單起見，只有語意交叉 entropy 是用來做為損失函數。

此範例會顯示在甚至更簡單的損失函數，SSWE 內嵌的效能會優於 Word2Vec 內嵌。 下圖顯示用來產生 人氣特有的斷內嵌 convolutional 模型：

![由 SSWE 啟發的類神經網路模型](./media/predict-twitter-sentiment/embedding-model-2.PNG)

在定型程序完成之後，以 tab 分隔值 (TSV) 格式的兩個內嵌檔案會產生的模型化階段。

如需 SSWE 演算法的詳細資訊，請參閱 Duyu Tang 的 box。「[學習人氣特定斷 Twitter 情緒分類為內嵌](http://www.aclweb.org/anthology/P14-1146)。 」 計算 Linguistics (1) 的關聯。 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[模型的建立](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Word 向量利用 SSWE 或 Word2Vec 演算法產生之後，會將定型分類模型來預測實際的人氣極性。 兩種類型的功能： Word2Vec 和 SSWE，會套用至兩個模型： 梯度促進模型和羅吉斯迴歸模型。 因此，四個不同的模型會將定型。


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[模型評估](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
會將定型模型之後，模型用來測試 Twitter 文字資料，並評估每個模型的效能。 此範例會評估下列四種模型：

- 透過 SSWE 內嵌的漸層停駐 Boosting。
- 透過 SSWE 內嵌羅吉斯迴歸。
- 透過 Word2Vec 內嵌的漸層停駐 Boosting。
- 透過 Word2Vec 內嵌羅吉斯迴歸。

在下圖顯示四個模型的效能比較：

![接收者作業系統特性 (ROC) 模型比較](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

使用曲線 (AUC) 度量下方的區域比較模型時，梯度促進 SSWE 功能模型會提供最佳的效能。


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[部署](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

最後一個步驟是部署在 Azure 容器服務的叢集上的 web 服務，定型的人氣預測模型。 這個範例會使用梯度促進模型使用 SSWE 內嵌的演算法為定型的模型。 實施環境會佈建 Docker 和 Kubernetes 叢集中管理 web 服務部署，如下圖所示： 

![Kubernetes 儀表板](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

如需有關實施程序的詳細資訊，請參閱[部署為 web 服務的 Azure 機器學習模型](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)。

## <a name="conclusion"></a>結論

在本文中，您學會如何使用 Word2Vec 和人氣特定字組內嵌的演算法來定型 word 內嵌模型。 擷取的內嵌用做為特徵來定型數個模型來預測 Twitter 文字資料的人氣分數。 SSWE 功能搭配梯度促進模型會提供最佳的效能。 使用 Azure Machine Learning 工作臺 」 然後為容器服務中的即時 web 服務部署模型。


## <a name="references"></a>參考

* [資料科學的小組流程](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [如何在 Azure Machine Learning 中使用 Team Data Science Process (TDSP)](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning 的 TDSP 專案範本](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [美國收入資料集從 UCI ML 儲存機制](https://archive.ics.uci.edu/ml/datasets/adult)
* [使用 TDSP 範本 biomedical 實體辨識](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov，Tomas，box。「 分散式的單字和片語以及其複合性表示法。進階神經資訊處理系統中 」。2013.](https://arxiv.org/abs/1310.4546)
* [Tang Duyu，box。「 學習人氣特有的斷內嵌 Twitter 情緒分類 」。ACL (1)。2014.](http://www.aclweb.org/anthology/P14-1146)
