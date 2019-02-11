---
title: Machine Learning Studio 常見問題集 (FAQ)
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio：常見問題集，涵蓋計費、功能，以及適用於簡化預測性模型化之雲端服務的限制。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462274"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure Machine Learning Studio 常見問題集：功能和限制
以下是有關 Azure Machine Learning 的一些常見問題和對應解答，而 Azure Machine Learning 是適合透過 Web 服務開發預測性模型和運作方案的雲端服務。 

## <a name="general-questions"></a>一般問題
**什麼是 Machine Learning Studio？**

Machine Learning Studio 是您可使用網頁瀏覽器存取的拖放畫布環境。 Machine Learning Studio 在視覺化結構介面中裝載一組模組，協助您以實驗的形式建置端對端資料科學工作流程。

如需 Machine Learning Studio 的詳細資訊，請參閱 [什麼是 Machine Learning Studio？](what-is-ml-studio.md)

**什麼是 Machine Learning API 服務？**

Machine Learning API 服務可讓您將預測模型 (例如 Machine Learning Studio 內建的預測模型) 部署為可調整、容錯的 Web 服務。 Machine Learning API 服務所建立的 Web 服務是 REST API，此類 API 提供的介面可用於外部應用程式與您的預測性分析模型之間的通訊。

如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。

**傳統 Web 服務會在哪裡列出？新型 (Azure Resource Manager 架構) Web 服務會在哪裡列出？**

使用傳統部署模型建立的 Web 服務和使用新型 Azure Resource Manager 部署模型建立的 Web 服務會在 [Microsoft Azure Machine Learning Web 服務](https://services.azureml.net/)入口網站中列出。

傳統 Web 服務也會在 [Web 服務] 索引標籤的 [Machine Learning Studio](http://studio.azureml.net) 中列出。

## <a name="azure-machine-learning-questions"></a>Azure Machine Learning 問題
**Azure Machine Learning Web 服務是什麼？**

Machine Learning Web 服務可在應用程式與機器學習服務工作流程計分模型之間提供介面。 外部應用程式可以使用 Azure Machine Learning 即時與 Machine Learning 工作流程計分模型通訊。 Machine Learning Web 服務呼叫會將預測結果傳回外部應用程式。 若要進行 Web 服務呼叫，您可以傳遞在部署 Web 服務時所建立的 API 金鑰。 Machine Learning Web 服務以 REST 為基礎，這是一種常見的 Web 程式設計專案架構。

Azure Machine Learning 有兩種 Web 服務類型：

* 要求回應服務 (RRS)：一種低延遲、調整性高的服務，針對使用 Machine Learning Studio 建立和部署的無狀態模型提供介面。
* 批次執行服務 (BES)：一種非同步的服務，可為一批資料記錄進行計分。

有數種方法可以使用 REST API 和存取 Web 服務。 例如，您可以使用當您部署 Web 服務時為您產生的範例程式碼，在 C#、R 或 Python 中寫入應用程式。

可用的範例程式碼位於：
- Azure Machine Learning Web 服務入口網站中 Web 服務的 [取用] 頁面
- Machine Learning Studio 中 Web 服務儀表板中的 [API 說明] 頁面

您也可以使用為您建立並可在 Machine Learning Studio 中的 Web 服務儀表板取得的範例 Microsoft Excel 活頁簿。

**Azure Machine Learning 的主要更新為何？**

如需最新更新，請參閱 [Azure Machine Learning 新增功能](../../active-directory/fundamentals/whats-new.md)。

## <a name="import-and-export-data-for-machine-learning"></a>匯入和匯出 Machine Learning 的資料
**機器學習服務支援何種資料來源？**

將資料下載至 Machine Learning Studio 實驗的方法有三種︰

- 以資料集的形式上傳本機檔案
- 使用模組從雲端資料服務匯入資料
- 匯入從另一個實驗儲存的資料集

若要深入了解支援的檔案格式，請參閱 [將訓練資料匯入 Azure Machine Learning Studio](import-data.md)。

### <a id="ModuleLimit"></a>我的模組適用多大的資料集？
Machine Learning Studio 中的模組對常見使用案例支援最多 10 GB 的密集數值資料的資料集。 如果模組接受一個以上的輸入，10 GB 值是所有輸入的大小總計。 您也可以使用 Hive 或 Azure SQL Database 查詢來取樣較大型資料集，或者在擷取前使用「以計數學習」前置處理。  

下列資料類型可以在功能正規化期間擴充為較大型資料集，並限制在小於 10 GB：

* 疏鬆
* 類別
* 字串
* 二進位資料

下列模組限制在小於 10 GB 的資料集：

* Recommender 模組
* 合成少數類過採樣技術 (SMOTE) 模組
* 指令碼模組：R、Python、SQL
* 輸出資料大小可以大於輸入資料大小的模組，例如聯結或功能雜湊
* 當反覆運算數目非常大時的交叉驗證、微調模型超參數、序數迴歸和一對多的多類別

### <a id="UploadLimit"></a>資料上傳的限制為何？
針對大於 2 GB 的資料集，請將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 Azure HDInsight，而不要直接從本機檔案上傳。

**可以從 Amazon S3 讀取資料嗎？**

如果您有少量的資料，而且想要透過 HTTP URL 公開，則您可以使用[匯入資料][import-data]模組。 若為較大量的資料，請先傳送至 Azure 儲存體，然後使用[匯入資料][import-data]模組將它帶入實驗中。
<!--

<SEE CLOUD DS PROCESS>
-->

**有內建的影像輸入功能嗎？**

您可以在[匯入影像][image-reader]參考中了解影像輸入功能。

## <a name="modules"></a>模組
**我在尋找的演算法、資料來源、資料格式或資料轉換作業不在 Azure Machine Learning Studio 中。我有哪些選擇？**

您可以前往[使用者意見反應論壇](https://go.microsoft.com/fwlink/?LinkId=404231)，檢視我們所追蹤的功能要求。 如果您要找的功能已有人要求，請投票給該要求。 如果您要找的功能不存在，請建立新要求。 您也可以在此論壇中檢視您的要求狀態。 我們會密切追蹤此清單，並經常更新功能可用性的狀態。 此外，您可以利用對 R 和 Python 的內建支援，視需要建立自訂轉換。

**是否可將我現有的程式碼放入 Machine Learning Studio 中？**

是的，您可以在 Machine Learning Studio 中放入現有的 R 或 Python 程式碼，使用 Azure Machine Learning 學習模組在相同的實驗中加以執行，然後透過 Azure Machine Learning 將解決方案部署為 Web 服務。 如需詳細資訊，請參閱[透過 R 擴展您的實驗](extend-your-experiment-with-r.md)和[在 Azure Machine Learning Studio 中執行 Python 機器學習服務指令碼](execute-python-scripts.md)。

**可使用 [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) 之類來定義模型嗎？**

否，不支援預測模型標記語言 (PMML)。 您可以使用自訂 R 和 Python 程式碼來定義模組。

**在我的實驗中可以平行執行多少個模組？**  

您可以在實驗中最多平行執行 4 個模組。

## <a name="data-processing"></a>資料處理
**有能力在實驗內將資料以互動方式視覺化嗎 (R 視覺效果外)？**

按一下模組的輸出，即可將資料視覺化並取得統計資料。

**在瀏覽器中預覽結果或資料時，資料列和資料行的數目很有限。原因為何？**

因為大量資料可能會傳送至瀏覽器，限制資料大小是為了防止 Machine Learning Studio 變慢。 若要將所有資料/結果視覺化，最好是下載資料並使用 Excel 或其他工具。

## <a name="algorithms"></a>演算法
**Machine Learning Studio 中支援哪些現有的演算法？**

Machine Learning Studio 提供頂級演算法，例如 Scalable Boosted Decision 樹、Bayesian Recommendation 系統、Deep Neural Networks 和 Decision Jungles (由 Microsoft Research 開發)。 此外也包含可調整的開放原始碼機器學習套件，例如 Vowpal Wabbit。 Machine Learning Studio 支援多類別與二進位分類、迴歸和叢集的機器學習演算法。 請參閱[機器學習服務單元][machine-learning-modules]的完整清單。

**您會自動建議我的資料適用的機器學習演算法嗎？**

不會，但 Machine Learning Studio 有各種方法可比較每個演算法的結果，以決定適合您的問題的演算法。

**針對從提供的演算法中挑選，是否有任何準則？**

請參閱 [如何選擇演算法](algorithm-choice.md)。

**提供的演算法是以 R 或 Python 撰寫？**

否，這些演算法大部分是以編譯的語言的撰寫，以提供更好的效能。

**有提供演算法之任何詳細資料嗎？**

文件提供一些演算法的相關資訊，並說明可供微調的參數，以針對您的使用最佳化演算法。  

**線上學習是否有任何支援？**

否，目前只支援以程式設計方式進行重新訓練。

**可以使用內建模組來視覺化類神經網路模型的層級嗎？**

沒有。

**可以以 C# 或其他語言建立自己的模組嗎？**

目前，您只能使用 R 來建立新的自訂模組。

## <a name="r-module"></a>R 模組
**Machine Learning Studio 中可使用什麼 R 套件？**

Machine Learning Studio 目前支援 400 個以上的 CRAN 套件，以下是所有內含套件的[目前清單](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)。 此外，若要了解如何自行擷取此清單，請參閱 [透過 R 擴展您的實驗](extend-your-experiment-with-r.md) 。 如果您要的套件不在此清單中，請在 [使用者意見反映論壇](https://go.microsoft.com/fwlink/?LinkId=404231)提供套件名稱。

**是否可以建置自訂的 R 模組？**

是，如需詳細資訊，請參閱 [在 Azure Machine Learning 中撰寫自訂 R 模組](custom-r-modules.md) 。

**是否有 R 適用的 REPL 環境？**

否，Studio 中沒有 R 適用的 Read-Eval-Print-Loop (REPL) 環境。

## <a name="python-module"></a>Python 模組
**是否可以建置自訂的 Python 模組？**

目前不可以，但是您可以使用一或多個[執行 Python 指令碼][python]模組來取得相同的結果。

**是否有 Python 適用的 REPL 環境？**

您可以在 Machine Learning Studio 中使用 Jupyter Notebook。 如需詳細資訊，請參閱 [介紹 Azure Machine Learning Studio 中的 Jupyter Notebook](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)。

## <a name="web-service"></a>Web 服務

**如何以程式設計方式重新訓練 Azure Machine Learning 模型？**

使用重新訓練 API。 如需詳細資訊，請參閱 [以程式設計方式重塑機器學習模型](retrain-models-programmatically.md)。 [Microsoft Azure Machine Learning 重新訓練示範](https://azuremlretrain.codeplex.com/)也會提供範例程式碼。

**可以在本機或在沒有網際網路連線的應用程式中部署模型嗎？**

沒有。

**所有 Web 服務是否有預期的基準延遲？**

請參閱 [Azure 訂用帳戶限制](../../azure-subscription-service-limits.md)。

**何時該以「批次執行服務」的形式執行預測模型？何時該以要求回應服務的形式執行？**

「要求回應服務 (RRS)」是低延遲性的高階 Web 服務，可用來為從實驗環境建立並部署的無狀態模型提供介面。 批次執行服務 (BES) 是可為一批資料記錄進行非同步評分的服務。 BES 的輸入就像 RRS 使用的資料輸入。 主要差異在於，BES 會從各種來源讀取記錄區塊，例如 Azure Blob 儲存體、Azure 表格儲存體、Azure SQL Database、HDInsight (Hive 查詢) 和 HTTP 來源。 如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。

**如何為已部署的 Web 服務更新模型？**

若要為已部署的服務更新預測模型，請修改並重新執行用來撰寫和儲存已訓練模型的實驗。 在您有新版的已訓練模型後，Machine Learning Studio 會詢問您是否要更新 Web 服務。 如需有關如何更新已部署 Web 服務的詳細資訊，請參閱[部署 Machine Learning Web 服務](publish-a-machine-learning-web-service.md)。

您也可以使用重新訓練 API。
如需詳細資訊，請參閱 [以程式設計方式重塑機器學習模型](retrain-models-programmatically.md)。 [Microsoft Azure Machine Learning 重新訓練示範](https://azuremlretrain.codeplex.com/)也會提供範例程式碼。

**如何監控部署在實際執行環境中的 Web 服務？**

部署預測模型之後，您可以從 Azure Machine Learning Web 服務入口網站加以監視。 每個已部署的服務都有其本身的儀表板，您可以在此處檢視該服務的監控資訊。 如需如何管理已部署 Web 服務的詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)和[管理 Azure Machine Learning 工作區](manage-workspace.md)。

**是否有哪個位置，可讓我查看我的 RRS/BES 輸出？**

針對 RRS，通常您可在 Web 服務回應查看此結果。 您可以也將結果寫入至 Azure Blob 儲存體。 BES 的輸出預設會寫入至 Blob。 您也可以使用[匯出資料][export-data]模組，將輸出寫入資料庫或資料表。

**只能從 Machine Learning Studio 中建立的模型來建立 Web 服務嗎？**

不，您也可以直接使用 Jupyter Notebook 和 RStudio 來建立 Web 服務。

**哪裡可以找到有關錯誤碼的詳細資訊？**

如需錯誤碼與說明的清單，請參閱 [Machine Learning 模組錯誤碼](https://msdn.microsoft.com/library/azure/dn905910.aspx) 。

**什麼是 Web 服務的延展性？**

目前的預設端點是每個端點上佈建 20 個並行 RRS 要求。 您可以將每個端點的並行要求調整為 200 個，也可以將每個 Web 服務的端點調整為 10,000 個，如[調整 Web 服務](scaling-webservice.md)所述。 對於 BES，每個端點一次可以處理 40 個要求，超過 40 個的其他要求則會排入佇列。 這些排入佇列的要求會在佇列清空後自動執行。

**R 作業會分散於節點嗎？**

沒有。  

**我可以將多少資料用於訓練？**

Machine Learning Studio 中的模組對常見使用案例支援最多 10 GB 的密集數值資料的資料集。 如果模組接受一個以上的輸入，則所有輸入的大小總計為 10 GB。 您也可以取樣較大型資料集，方法是透過 Hive 查詢或 Azure SQL Database 查詢，或在擷取前利用[以計數學習][counts]模組進行前置處理。  

下列資料類型可以在功能正規化期間擴充為較大型資料集，並限制在小於 10 GB：

* 疏鬆
* 類別
* 字串
* 二進位資料

下列模組限制在小於 10 GB 的資料集：

* Recommender 模組
* 合成少數類過採樣技術 (SMOTE) 模組
* 指令碼模組：R、Python、SQL
* 輸出資料大小可以大於輸入資料大小的模組，例如聯結或功能雜湊
* 當反覆運算數目非常大時的交叉驗證、微調模型超參數、序數迴歸和一對多的多類別

針對大於幾 GB 的資料集，將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 HDInsight，而不要直接從本機檔案上傳。

**是否有任何向量大小限制嗎？**

每個資料列和資料行的限制在 .NET 的最大 Int 限制：2,147,483,647。

**可以調整用來執行 Web 服務的虛擬機器大小嗎？**

沒有。  

## <a name="security-and-availability"></a>安全性和可用性
**根據預設，哪些人可以存取 Web 服務的 HTTP 端點？如何限制對此端點的存取？**

部署 Web 服務之後，我們會建立該服務的預設端點。 該預設端點可使用其 API 金鑰進行呼叫。 您可以從 Web 服務入口網站或使用 Web 服務管理 API 以程式設計方式新增更多具有專屬金鑰的端點。 必須要有存取金鑰，才能呼叫 Web 服務。 如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。

**如果找不到我的 Azure 儲存體帳戶，會發生什麼情況？**

Machine Learning Studio 依賴使用者提供的 Azure 儲存體帳戶，才能在執行工作流程時儲存中繼資料。 此儲存體帳戶會在建立工作區時提供給 Machine Learning Studio。 建立工作區之後，如果刪除儲存體帳戶，而且不再能找到，工作區會停止運作，並且該工作區中的所有實驗將會失敗。

如果您不小心刪除儲存體帳戶，請使用與所刪除儲存體帳戶相同的區域中的相同名稱來重新建立儲存體帳戶。 在那之後，重新同步處理存取金鑰。

**如果我的儲存體帳戶存取金鑰未同步，會發生什麼情況？**

Machine Learning Studio 依賴使用者提供的 Azure 儲存體帳戶，才能在執行工作流程時儲存中繼資料。 此儲存體帳戶是在建立工作區時提供給 Machine Learning Studio，並且存取金鑰會與該工作區相關聯。 建立工作區之後，如果變更存取金鑰，該工作區便無法再存取儲存體帳戶。 它會停止運作，而該工作區中的所有實驗將會失敗。

如果您變更了儲存體帳戶存取金鑰，請使用 Azure 入口網站在工作區中重新同步處理存取金鑰。  


## <a name="billing-questions"></a>計費問題

如需計費和定價資訊，請參閱 [Machine Learnin 定價](https://azure.microsoft.com/pricing/details/machine-learning/)。


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
