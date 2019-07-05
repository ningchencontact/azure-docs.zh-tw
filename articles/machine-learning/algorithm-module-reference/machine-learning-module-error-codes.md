---
title: 模組錯誤進行疑難排解
titleSuffix: Azure Machine Learning service
description: 使用錯誤代碼的 Azure Machine Learning Studio 中的模組例外狀況的疑難排解
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5ce3d86d37940f66065debce30b348371b7aa6af
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568237"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>例外狀況和錯誤代碼的演算法和模組參考

深入了解的錯誤訊息和例外狀況代碼，您可能會遇到 Azure Machine Learning Studio 中使用模組。 

若要解決此問題，尋找這篇文章，了解常見的原因中的錯誤訊息。 有兩種方式可以在 Studio 中取得完整的錯誤訊息文字：  
 
- 按一下連結**檢視輸出記錄檔**右窗格中捲動至底部。 詳細的錯誤訊息會顯示在視窗的最後兩行。  
  
- 選取模組發生錯誤，然後按一下紅色的 X。會顯示相關的錯誤文字。  
  
如果錯誤訊息文字不是很有幫助，傳送給我們的內容和任何所需的新增或變更的相關資訊。 您可以提交的意見反應錯誤 > 主題，或請造訪[Azure Machine Learning STUDIO 論壇](https://aka.ms/aml-forum-studio)並張貼的問題。  


## <a name="error-0001"></a>錯誤 0001  
 如果找不到一或多個指定的資料行的資料集，就會發生例外狀況。  
  
 如果資料行選取項目對於模組，但選取的資料行不存在於輸入資料集，您會收到這個錯誤。 如果您以手動方式輸入資料行名稱，或如果資料行選取器已提供的建議的資料行，不存在於您的資料集執行實驗時，可能會發生此錯誤。  
  
**解決方案：** 再次瀏覽 模組擲回這個例外狀況，並驗證資料行名稱正確無誤，然後執行存在的所有參考的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|找不到一或多個指定的資料行|  
|名稱或索引資料行"{0}「 找不到|  
|名稱或索引資料行"{0}"不存在於 「{1}"|  
 

## <a name="error-0002"></a>錯誤 0002  
 如果一或多個參數無法剖析或從已轉換成指定的類型必須由目標方法的型別時，發生例外狀況。  
  
 當您指定做為輸入的參數和實值型別不同於預期的類型，無法執行隱含轉換，可在 Azure Machine Learning 中發生此錯誤。  
  
**解決方案：** 檢查模組的需求，並判斷需要之值的類型 （字串、 整數、 雙精確度，依此類推。）  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析參數|  
|無法剖析"{0}"參數|  
|無法剖析 （轉換） 」{0}「 參數 」{1}"|  
|轉換失敗 」{0}「 參數 」{1}"to"{2}」|  
|轉換失敗 」{0}"參數值"{1}的 「 來源 」{2}"to"{3}」|  
|無法將值轉換 」{0}"中的資料行 」{1}的 「 來源 」{2}"to"{3}」 使用的格式 」{4}」 提供|  
  

## <a name="error-0003"></a>錯誤 0003  
 發生例外狀況，如果有一個或多個輸入是 null 或空白。  
  
 如果任何輸入或模組的參數是 null 或空白，您會在 Azure Machine Learning 中收到此錯誤。  這可能會發生錯誤，比方說，當您未輸入任何參數的值。 如果您選擇的資料集有遺漏值或空的資料集時，它也可能發生。  
  
**解決方案：**
 
+ 開啟產生例外狀況的模組，並確認已指定所有的輸入。 請確定所有必要的指定輸入。 
+ 請確定會從 Azure 儲存體載入的資料可供存取，且帳戶名稱或索引鍵已不進行變更。  
+ 請檢查遺漏的值或 null 值的輸入的資料。
+ 如果使用資料來源上的查詢，請確認您所預期的格式傳回資料。 
+ 檢查拼字錯誤或其他資料的規格中的變更。
  
|例外狀況訊息|  
|------------------------|  
|一或多個輸入是 null 或空白|  
|輸入 「{0}"是 null 或空白|  
  

## <a name="error-0004"></a>錯誤 0004  
 如果參數小於或等於特定值，就會發生例外狀況。  
  
 如果訊息中的參數值低於界限值所需的模組來處理資料，您會收到此錯誤在 Azure Machine Learning 中。  
  
**解決方案：** 再次瀏覽 模組擲回例外狀況，並修改的參數必須大於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數應該大於界限值。|  
|參數"{0}"值應該大於{1}。|  
|參數"{0}"has 值"{1}"應該大於 {2}|  
  


## <a name="error-0005"></a>錯誤 0005  
 如果參數小於特定值，就會發生例外狀況。  
  
 您將會收到此錯誤在 Azure Machine Learning 中，如果訊息中的參數值低於或等於模組來處理資料所需的界限值。  
  
**解決方案：** 再次瀏覽 模組擲回例外狀況，並修改的參數必須大於或等於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數應該大於或等於界限值。|  
|參數"{0}"值應該大於或等於{1}。|  
|參數"{0}"has 值"{1}"應該大於或等於{2}。|  
  

## <a name="error-0006"></a>錯誤 0006  
 如果參數大於或等於指定的值，就會發生例外狀況。  
  
 您會在 Azure Machine Learning 中收到這個錯誤訊息中的參數是否大於或等於模組來處理資料所需的界限值。  
  
**解決方案：** 再次瀏覽 模組擲回例外狀況，並修改參數必須早於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數不相符。 其中一個參數應該小於另一個。|  
|參數"{0}"值應該小於參數"{1}"值。|  
|參數"{0}"has 值"{1}"應該小於{2}。|  
  

## <a name="error-0007"></a>錯誤 0007  
 如果參數大於特定值，就會發生例外狀況。  
  
 如果在模組的屬性，您已指定為大於允許的值，您會在 Azure Machine Learning 中收到這個錯誤。 例如，您可以指定支援的日期範圍之外的資料，或可能只有三個資料行可用時，使用五個資料行。 
 
 如果您要指定兩組必須以某種方式相符的資料，您也可能會看到此錯誤。 例如，如果您要重新命名資料行，並由索引指定的資料行時，您提供的名稱數目必須符合資料行索引的數目。 另一個範例可能會使用兩個資料行，其中的資料行必須有相同數目的資料列的數學運算。 
  
**解決方案：**
 
 + 開啟模組有問題，並檢閱任何數值屬性的設定。
 + 請確定任何參數值落在支援的值範圍內的該屬性。
 + 如果模組接受多個輸入，請確定輸入相同的大小。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 請檢查是否已變更的資料集或資料來源。 有時候使用舊版的資料值將會失敗之後已變更的資料行數目、 資料行資料類型或資料的大小。  
  
|例外狀況訊息|  
|------------------------|  
|參數不相符。 其中一個參數應該小於或等於另一個。|  
|參數"{0}"值應該要小於或等於參數"{1}"值。|  
|參數"{0}"has 值"{1}"應該小於或等於{2}。|  
  

## <a name="error-0008"></a>錯誤 0008  
 如果參數不在範圍內，就會發生例外狀況。  
  
 如果訊息中的參數是模組來處理資料所需的界限之外，您會收到這個錯誤 Azure Machine Learning 中。  
  
 如果您嘗試使用，例如，顯示此錯誤[加入資料列](add-rows.md)結合兩個具有不同數目的資料行的資料集。  
  
**解決方案：** 再次瀏覽 模組擲回例外狀況，並修改此參數指定的範圍內。  
  
|例外狀況訊息|  
|------------------------|  
|參數值不是指定的範圍內。|  
|參數"{0}"值不在範圍內。|  
|參數"{0}"值應該是範圍內的 [{1}， {2}]。|  
  

## <a name="error-0009"></a>錯誤 0009  
 指定 Azure 儲存體帳戶名稱或容器名稱不正確時，就會發生例外狀況。  
  
當您指定參數，針對 Azure 儲存體帳戶，但無法解析的名稱或密碼時，可在 Azure Machine Learning Studio 中發生此錯誤。 有許多原因，可能會發生密碼或帳戶名稱的錯誤：
 
 + 此帳戶是錯誤的類型。 使用 Machine Learning Studio 的使用不支援一些新的帳戶類型。 請參閱[匯入資料](import-data.md)如需詳細資訊。
 + 您輸入不正確的帳戶名稱
 + 帳戶已不存在
 + 儲存體帳戶的密碼有誤，或已變更
 + 您未指定容器名稱，或容器不存在
 + 您完全未指定檔案路徑 （blob 路徑）
   
**解決方案：**

當您嘗試手動輸入帳戶名稱、 密碼或容器路徑時，通常會發生這類問題。 我們建議您使用的新精靈[匯入資料](import-data.md)模組，可協助您查閱，並檢查名稱。

也請檢查是否已刪除的帳戶、 容器或 blob。 您可以使用另一個 Azure 儲存體公用程式來驗證，帳戶名稱和密碼已輸入正確，然後在容器已存在。 

Azure Machine learning 不支援某些較新的帳戶類型。 比方說，新的 「 經常性 」 或者 「 冷 」 儲存體類型無法用於機器學習服務。 傳統儲存體帳戶和儲存體帳戶建立為 「 一般用途 」 正常運作。

如果指定 blob 的完整路徑，請確認路徑指定為**容器/blobname**，並在帳戶中存在的容器和 blob。  
  
 路徑不應包含斜線。 比方說 **/容器或 blob**不正確，而且應做為輸入**容器或 blob**。  

  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶名稱或容器名稱不正確。|  
|Azure 儲存體帳戶名稱 」{0}"或容器名稱"{1}「 不正確; 格式的容器或 blob 容器名稱。|  
  

## <a name="error-0010"></a>錯誤 0010  
 如果輸入資料集有應該符合但卻不符的資料行名稱，就會發生例外狀況。  
  
 如果訊息中的資料行索引有不同的資料行名稱在兩個輸入資料集，您會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方案：** 使用[編輯中繼資料](edit-metadata.md)或修改要具有相同的資料行名稱，指定資料行索引的原始資料集。  
  
|例外狀況訊息|  
|------------------------|  
|具有對應的索引，在輸入資料集中的資料行具有不同的名稱。|  
|資料行名稱不是相同的資料行{0}（以零起始） 的輸入資料集 ({1}和{2}分別)。|  
  

## <a name="error-0011"></a>錯誤 0011  
 如果，發生例外狀況傳遞資料行集引數不適用於任何資料集資料行。  
  
 如果指定的資料行選取項目不符合任何一欄中指定的資料集，您會在 Azure Machine Learning 中收到此錯誤。  
  
 如果您未選取的資料行和至少一個資料行是為了讓模組能，您也可以取得此錯誤。  
  
**解決方案：** 因此，它會套用至資料集的資料行，請修改模組中的資料行選取項目。  
  
 如果模組需要您選取特定的資料行，例如 [標籤] 資料行中，確認已選取正確的資料行。  
  
 如果選取不適當的資料行，將它們移除，然後重新執行實驗。  
  
|例外狀況訊息|  
|------------------------|  
|指定的資料行集不適用於任何資料集資料行。|  
|指定資料行集"{0}」 不適用於任何資料集資料行。|  
  

## <a name="error-0012"></a>錯誤 0012  
 如果無法建立類別的執行個體，以傳遞的引數的集合，就會發生例外狀況。  
  
**解決方案：** 此錯誤可採取的動作，使用者並不會在未來版本中已被取代。  
  
|例外狀況訊息|  
|------------------------|  
|未定型模型，第一次定型模型。|  
|未定型的模型 ({0})，使用定型的模型。|  
  

## <a name="error-0013"></a>錯誤 0013  
 如果傳遞給模組的學習因子是無效的類型，就會發生例外狀況。  
  
 定型的模型，已連線的評分模型與不相容時，就會發生此錯誤。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**解決方案：**

判斷產生的訓練單元的學習因子的型別，並判斷適合學習，評分模型。 

如果模型已定型使用任何特製化的訓練單元，定型的模型僅連線到對應的特製化計分模組。 


|模型類型|訓練模組| 評分模組|
|----|----|----|
|任何分類器|[定型模型](train-model.md) |[評分模型](score-model.md)|
|任何迴歸模型|[定型模型](train-model.md) |[評分模型](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|例外狀況訊息|  
|------------------------|  
|傳遞的學習類型無效。|  
|學習模組 「{0}"有無效的類型。|  


## <a name="error-0014"></a>錯誤 0014  
 如果資料行的唯一值的計數大於允許的就會發生例外狀況。  
  
 當資料行包含太多唯一值時，就會發生此錯誤。  比方說，如果您指定資料行視為類別的資料，您可能會看到此錯誤，但要讓處理作業完成的資料行中有太多唯一值。 如果有兩個輸入中的唯一值數目不相符，您也可能會看到此錯誤。   
  
**解決方案：**

開啟模組產生錯誤，並識別做為輸入的資料行。 對某些模組，您可以以滑鼠右鍵按一下資料集輸入，然後選取**視覺化**若要取得個別資料行，包括唯一值數目以及其散發統計資料。

針對您想要使用的群組或分類的資料行，採取步驟來減少資料行中的唯一值數目。 您可以減少在不同的方式，根據資料行的資料類型。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到符合您案例的解析度？ 在本主題，其中包含產生錯誤，而資料類型的模組名稱和基數的資料行上，您可以提供意見反應。 我們將使用的資訊，以提供更鎖定目標的常見案例的疑難排解步驟。   
  
|例外狀況訊息|  
|------------------------|  
|資料行的唯一值的數目大於允許的。|  
|資料行中的唯一值的數字:"{0}"超過 tuple 計數{1}。|  
  

## <a name="error-0015"></a>錯誤 0015  
 如果資料庫連接失敗，就會發生例外狀況。  
  
 如果您輸入不正確的 SQL 帳戶名稱、 密碼、 資料庫伺服器或資料庫名稱，或如果無法建立與資料庫連線，因為資料庫或伺服器有問題，您會收到這個錯誤。  
  
**解決方案：** 請確認，帳戶名稱、 密碼、 資料庫伺服器和資料庫已輸入正確，然後指定的帳戶具有正確的層級的權限。 確認資料庫目前可以存取。  
  
|例外狀況訊息|  
|------------------------|  
|建立資料庫連接時發生錯誤。|  
|建立資料庫連接時發生錯誤： {0}。|  
  


## <a name="error-0016"></a>錯誤 0016  
 如果傳遞給模組的輸入資料集應該有相容的資料行類型，但卻不符，就會發生例外狀況。  
  
 如果傳入兩個或多個資料集資料行的類型不相容，您會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方案：** 使用[編輯中繼資料](edit-metadata.md)或修改原始的輸入資料集<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 若要確保資料行的類型相容。  
  
|例外狀況訊息|  
|------------------------|  
|具有對應的索引，在輸入資料集中的資料行沒有不相容的類型。|  
|資料行{0}和{1}不相容。|  
|資料行項目類型不相容的資料行{0}（以零起始） 的輸入資料集 ({1}和{2}分別)。|  
  

## <a name="error-0017"></a>錯誤 0017  
 如果選取的資料行使用目前的模組不支援的資料類型，就會發生例外狀況。  
  
 例如，您可能會收到此錯誤在 Azure Machine Learning 中如果您的資料行選取範圍包含具有無法處理的模組，例如數學作業中，字串資料行或分數資料行所在的類別特徵資料行的資料類型的資料行必要的。  
  
**解決方案：**
 1. 找出問題所在的資料行。
 2. 檢閱模組的需求。
 3. 修改資料行，使其符合需求。 您可能需要進行變更，根據資料行和轉換您正嘗試使用多個下列模組：
    + 使用[編輯中繼資料](edit-metadata.md)來變更資料行資料類型，或將功能從變更資料行的使用數值、 類別至非類別等等。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 最後的手段，您可能需要修改原始的輸入資料集。

> [!TIP]
> 找不到符合您案例的解析度？ 在本主題，其中包含產生錯誤，而資料類型的模組名稱和基數的資料行上，您可以提供意見反應。 我們將使用的資訊，以提供更鎖定目標的常見案例的疑難排解步驟。 
  
|例外狀況訊息|  
|------------------------|  
|無法處理目前的類型資料行。 模組不支援的類型。|  
|無法處理類型的資料行{0}。 模組不支援的類型。|  
|無法處理資料行"{1}"類型的{0}。 模組不支援的類型。|  
|無法處理資料行"{1}"類型的{0}。 模組不支援的類型。 參數名稱： {2}|  
  

## <a name="error-0018"></a>錯誤 0018  
 如果輸入資料集不正確，就會發生例外狀況。  
  
**解決方案：** 在 Azure Machine Learning 中可能出現此錯誤在許多內容中，因此不單一的解析度。 一般情況下，此錯誤表示做為輸入至模組所提供的資料有錯誤的資料行數目或資料類型不符的模組的需求。 例如:  
  
-   模組需要標籤資料行，但沒有資料行標示為標籤，或您有尚未選取標籤資料行。  
  
-   此模組需要資料是類別，但您的資料為數值。  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   資料是格式錯誤。  
  
-   匯入的資料包含無效的字元，不正確的值，或移出範圍值。  
-   資料行是空的或包含太多的遺漏值。  
  
 若要判斷的需求，以及您資料的可能方式，檢閱將要使用的資料集做為輸入之模組的 [說明] 主題。  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->上也提供本文中使用的原始碼。  
  
|例外狀況訊息|  
|------------------------|  
|資料集不是有效的。|  
|{0} 包含無效的資料。|  
|{0} 和{1}應該是明智的一致的資料行。|  
  

## <a name="error-0019"></a>錯誤 0019  
 如果資料行必須包含已排序的值，但並不會發生例外狀況。  
  
 如果指定的資料行值的順序是，您會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方案：** 排序資料行的值，以手動方式修改輸入資料集，然後重新執行該模組。  
  
|例外狀況訊息|  
|------------------------|  
|不會排序資料行中的值。|  
|資料行中的值"{0}」 不會排序。|  
|資料行中的值 」{0}"的資料集 」{1}」 不會排序。|  
  

## <a name="error-0020"></a>錯誤 0020  
 如果某些資料集傳遞到模組中的資料行數目太小，就會發生例外狀況。  
  
 您會收到這個錯誤，Azure Machine Learning 中如果沒有足夠的資料行已選取的模組。  
  
**解決方案：** 再次瀏覽該模組，並確定該資料行選取器選取的資料行的正確數目。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集中的資料行數目小於允許最少。|  
|輸入資料集中的資料行數目小於允許最少的{0}資料行。|  
|輸入資料集中的資料行數目 」{0}"小於允許最少的{1}資料行。|

## <a name="error-0021"></a>錯誤 0021  
 如果某些資料集傳遞到模組中的資料列數目太小，就會發生例外狀況。  
  
 出現此錯誤在 Azure Machine Learning 中有時沒有足夠的資料列集中要執行指定的作業。 比方說，您可能會看到此錯誤，如果輸入資料集是空的或如果您嘗試執行需要一些有效的資料列的最小數目的作業。 這類作業可以包括 （但不限於） 群組或分類根據統計方法，特定類型的分類收納，和以計數學習。  
  
**解決方案：**
 
 + 開啟的模組會傳回錯誤，然後檢查輸入的資料集和模組屬性。 
 + 請確認輸入的資料集不是空白，且有足夠的資料列的資料必須符合模組 [說明] 中所述的需求。  
 + 如果您的資料從外部來源載入，請確定資料來源可用，且沒有任何錯誤，或變更會造成匯入程序，以取得較少的資料列的資料定義中。
 + 如果您正在執行的上游資料的模組，可能會影響的資料類型或數目的值，例如清除、 分割或聯結作業的作業，請檢查這些作業，以判斷傳回的資料列數目的輸出。  



## <a name="error-0022"></a>錯誤 0022  
 如果輸入資料集中選取的資料行數目不等於預期的數目，就會發生例外狀況。  
  
 下游的模組或作業都需要特定數目的資料行或輸入，並提供資料行或輸入太少或太多時，會發生在 Azure Machine Learning 中的此錯誤。 例如:  
  
-   您指定的單一標籤資料行或索引鍵資料行，並不小心選取多個資料行。  
  
-   您要重新命名資料行，但比資料行提供更多或較少的名稱。  
  
-   在來源或目的地的資料行數目已變更，或不符合模組所使用的資料行數目。  
  
-   值的逗號分隔清單提供為輸入，但值數目不符，或不支援多個輸入。  
  
**解決方案：** 再次瀏覽該模組，並檢查，確定已選取正確的資料行數目的資料行選取項目。 確認上游的模組的輸出，且下游作業的需求。  
  
 如果您使用其中一個資料行選取範圍選項，可以選取多個資料行 （資料行索引，所有的功能，全都是數字等），驗證所選取項目傳回的資料行的確切數目。  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 請確認數目或類型的上游資料行已不進行變更。  
  
 如果您使用建議的資料集來定型模型，請記得推薦需要有限的數目的資料行，對應到使用者項目組 」 或 「 使用者-項目-排名。 移除其他資料行，定型模型，或分割建議資料集之前。 如需詳細資訊，請參閱 <<c0> [ 分割資料](split-data.md)。  
  
|例外狀況訊息|  
|------------------------|  
|選取輸入資料集中的資料行數目不等於預期的數目。|  
|選取輸入資料集中的資料行數目不等於{0}。|  
|資料行選取模式 」{0}」 提供的輸入資料集不等於所選資料行數目{1}。|  
|資料行選取模式 」{0}」 必須為提供{1}資料行選取輸入資料集，但{2}是/所提供的資料行。|  



## <a name="error-0023"></a>錯誤 0023  
 如果目前的培訓講師模組的輸入資料集的目標資料行無效，就會發生例外狀況。  
  
 如果目標資料行 （如 所選取的模組參數中） 不是有效的資料類型，包含所有遺漏的值，或未如預期般的類別，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 再次瀏覽的模組輸入檢查標籤/目標資料行的內容。 請確定它並沒有完全為遺漏值。 如果模組預期目標資料行是類別，請確定有一個以上的相異值的目標資料行中。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集具有不支援目標資料行。|  
|輸入資料集具有不支援的目標資料行"{0}」。|  
|輸入資料集具有不支援的目標資料行"{0}"類型的學習模組{1}。|  
 

## <a name="error-0024"></a>錯誤 0024  
如果資料集不包含標籤資料行，就會發生例外狀況。  

 此模組需要標籤資料行和資料集並沒有標籤資料行時，就會發生此錯誤，Azure Machine Learning 中。 比方說，評估計分的資料集通常需要標籤資料行是用來計算精確度的度量。  
 
它也可能發生標籤資料行出現在資料集，但未偵測到正確的 Azure Machine Learning。
  
**解決方案：**

+ 開啟模組產生錯誤，並決定是否出現 [標籤] 資料行。 資料行的名稱或資料類型並不重要，只要資料行包含單一結果 （或相依變數），您嘗試預測。 如果您不確定哪一個資料行標籤，尋找一個通用的名稱這類*類別*或是*目標*。 
+  如果資料集不包含標籤資料行，就可能標籤資料行已明確或不小心移除上游。 它也可能是資料集不是上游的評分模組的輸出。
+ 若要明確地標示為標籤資料行的資料行，加入[編輯中繼資料](edit-metadata.md)模組，並連接資料集。 只選取標籤資料行，然後選取**標籤**從**欄位**下拉式清單中。 
+ 如果您選擇錯誤的資料行則做為標籤，您可以選取**清除標籤**從**欄位**修正 資料行上的 中繼資料。 
  
|例外狀況訊息|  
|------------------------|  
|在資料集中沒有標籤資料行。|  
|沒有標籤資料行在"{0}」。|  
  

## <a name="error-0025"></a>錯誤 0025  
 如果資料集中沒有分數資料行，就會發生例外狀況。  
  
 如果評估模型的輸入不包含有效的分數，就會發生此錯誤，Azure Machine Learning 中的資料行。 例如，使用者會嘗試評估資料集，才能使用正確的定型模型，評分或分數資料行明確上游卸除。 如果兩個資料集的分數資料行不相容，也會發生這個例外狀況。 例如，您可能會嘗試比較的線性迴歸輸入變數與二元分類器的精確度。  
  
**解決方案：** 再次瀏覽至 評估模型的輸入，並檢查如果它包含一或多個分數資料行。 如果不是，不評分資料集或分數資料行已被卸除上游的模組中。  
  
|例外狀況訊息|  
|------------------------|  
|在資料集中沒有分數資料行。|  
|中沒有分數資料行"{0}」。|  
|中沒有分數資料行"{0}」，由所產生 「{1}"。 評分資料集使用學習模組的正確型別。|  
  

## <a name="error-0026"></a>錯誤 0026  
 如果不允許具有相同名稱的資料行，就會發生例外狀況。  
  
 如果多個資料行具有相同的名稱，就會發生此錯誤，Azure Machine Learning 中。 您可能會收到此錯誤的其中一個方法是，如果資料集沒有標頭資料列和資料行名稱會自動指派：Col0 Col1、 等等。  
  
**解決方案：** 如果資料行具有相同的名稱，插入[編輯中繼資料](edit-metadata.md)之間的輸入資料集和模組的模組。 使用中的資料行選取器[編輯中繼資料](edit-metadata.md)選取要重新命名資料行輸入到新的名稱**新的資料行名稱**文字方塊中。  
  
|例外狀況訊息|  
|------------------------|  
|引數中指定的相等資料行名稱。 模組不允許相同的資料行名稱。|  
|等於引數中的資料行名稱 」{0}"和"{1}「 不允許。 指定不同的名稱。|  
  

## <a name="error-0027"></a>錯誤 0027  
 如果兩個物件時必須是相同的大小，但不會發生例外狀況。  
  
 這是常見的錯誤，Azure Machine Learning 中，而且可能由多個條件。  
  
**解決方案：** 沒有任何特定解決方式。 不過，您可以檢查在下列情況：  
  
-   如果您要重新命名資料行，請確定每個清單 （輸入資料行和新名稱的清單） 有相同數目的項目。  
  
-   如果您要聯結或串連兩個資料集，請確定它們有相同的結構描述。  
  
-   如果您要聯結兩個具有多個資料行的資料集，請確定索引鍵資料行有相同的資料類型，並選取選項**允許重複的項目，並保留選取範圍中的資料行順序**。  
  
|例外狀況訊息|  
|------------------------|  
|傳遞的物件大小不一致。|  
|大小 」{0}"和大小不一致的 「{1}"。|  
  

## <a name="error-0028"></a>錯誤 0028  
 在案例中的事件，是當資料行集包含重複的資料行名稱，且不允許發生的例外狀況。  
  
 重複資料行名稱; 時，會發生此錯誤，Azure Machine Learning 中也就是不是唯一的。  
  
**解決方案：** 如果任何資料行有相同名稱，加入的執行個體[編輯中繼資料](edit-metadata.md)之間的輸入資料集和模組引發錯誤。 使用中的資料行選取器[編輯中繼資料](edit-metadata.md)選取要重新命名，並輸入新的資料行名稱，到資料行**新的資料行名稱**文字方塊中。 如果您要重新命名多個資料行，請確認您輸入的值**新的資料行名稱**是唯一的。  
  
|例外狀況訊息|  
|------------------------|  
|資料行集包含重複的資料行名稱。|  
|名稱"{0}"重複。|  
|名稱"{0}"中有重複的"{1}」。|  
  

## <a name="error-0029"></a>錯誤 0029  
 如果傳遞無效的 URI 時，就會發生例外狀況。  
  
 如果傳遞無效的 URI 時，就會發生此錯誤，Azure Machine Learning 中。  如果下列任一條件成立，您會收到這個錯誤:、 或。  
  
-   公用或 SAS URI 提供 Azure Blob 儲存體進行讀取或寫入包含錯誤。  
  
-   時間範圍的 sas 已過期。  
  
-   Web URL，透過 HTTP 來源代表檔案或回送 URI。  
  
-   透過 HTTP 的 Web URL 包含格式不正確的 URL。  
  
-   無法解析遠端來源 URL。  
  
**解決方案：** 再次瀏覽該模組，並確認 URI 的格式。 如果資料來源是透過 HTTP 的 Web URL，請確認預期的來源不是檔案或回送 URI (localhost)。  
  
|例外狀況訊息|  
|------------------------|  
|傳遞無效的 Uri。|  
  

## <a name="error-0030"></a>錯誤 0030  
 不可以下載檔案時，萬一發生例外狀況。  
  
 不可以下載檔案時，就會發生這個例外狀況，在 Azure Machine Learning 中。 您會收到這個例外狀況，從 HTTP 來源嘗試的讀取三 （3） 重試後失敗時嘗試。  
  
**解決方案：** 確認 HTTP 來源的 URI 正確，而且目前可透過網際網路存取站台。  
  
|例外狀況訊息|  
|------------------------|  
|無法下載檔案。|  
|下載檔案時發生錯誤： {0}。|  
  

## <a name="error-0031"></a>錯誤 0031  
 如果資料行集中的資料行數目少於所需，就會發生例外狀況。  
  
 如果選取的資料行數目少於所需，就會發生此錯誤，Azure Machine Learning 中。  如果未選取的資料行數目所需的最小，您會收到這個錯誤。  
  
**解決方案：** 使用程式中加入資料行選取其他資料行**資料行選取器**。  
  
|例外狀況訊息|  
|------------------------|  
|資料行集中的資料行數目小於所需。|  
|{0} 應指定資料行。 指定的資料行的實際數目是{1}。|  

## <a name="error-0032"></a>錯誤 0032  
 如果引數不是數字，就會發生例外狀況。  
  
 如果引數為雙精度浮點數或 NaN，您會在 Azure Machine Learning 中收到此錯誤。  
  
**解決方案：** 修改指定的引數，使用有效的值。  
  
|例外狀況訊息|  
|------------------------|  
|引數不是數字。|  
|「{0}"不是數字。|  
  

## <a name="error-0033"></a>錯誤 0033  
 如果引數是無限大，就會發生例外狀況。  
  
 如果引數為無限，就會發生此錯誤，Azure Machine Learning 中。 如果引數就是，您會收到這個錯誤`double.NegativeInfinity`或`double.PositiveInfinity`。  
  
**解決方案：** 修改指定的引數必須是有效的值。  
  
|例外狀況訊息|  
|------------------------|  
|引數必須是有限。|  
|「{0}"不是有限。|  
  

## <a name="error-0034"></a>錯誤 0034  
 如果指定的使用者-項目配對存在一個以上的評等，就會發生例外狀況。  
  
 如果使用者-項目組都有一個以上的評等值，Azure Machine Learning 中的這個錯誤就會發生在建議中。  
  
**解決方案：** 確定使用者-項目組擁有只有單一分級值。  
  
|例外狀況訊息|  
|------------------------|  
|在資料集中的值有一個以上的評等。|  
|多個使用者評等{0}和 項目{1}評比預測資料表中。|  
  

## <a name="error-0035"></a>錯誤 0035  
 如果未提供特徵為指定的使用者或項目，就會發生例外狀況。  
  
 您嘗試使用推薦模型進行評分，但找不到的特徵向量，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**

Matchbox recommender 已使用使用者特徵或項目特徵時，必須符合特定需求。  此錯誤表示的特徵向量遺漏使用者或您提供做為輸入的項目。  您必須確定該的特徵向量可用於每個使用者或項目資料。  
  
 比方說，如果您在定型建議模型使用功能，例如使用者的年齡、 居住地或收入，但現在想要建立分數的並未出現在訓練期間，您必須提供一些對等的功能集的新使用者 (亦即 「 年齡 」、 「 位置，以及年收入值） 才能進行適當預測新的使用者。 
 
 如果您沒有任何功能，這些使用者，請考慮特性工程設計以產生適當的功能。  例如，如果您還沒有個別的使用者存留期或收入值，您可能會產生大約值，若要使用的使用者群組。 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > 解析不適用於您的案例？ 您可以在本文中傳送意見反應，並提供案例，包括資料行中的模組和資料列數目的相關資訊。 我們將使用這項資訊，以提供更詳細的疑難排解步驟，在未來。
   
|例外狀況訊息|  
|------------------------|  
|不提供任何特徵給必要的使用者或項目。|  
|功能的{0}需要但未提供。|  
  

## <a name="error-0036"></a>錯誤 0036  
 如果指定的使用者或項目提供多個特徵向量，就會發生例外狀況。  
  
 如果特徵向量定義一次以上，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 請確定特徵向量不可以定義一次以上。  
  
|例外狀況訊息|  
|------------------------|  
|使用者或項目的特徵定義重複。|  
|特徵定義重複{0}。|  
  

## <a name="error-0037"></a>錯誤 0037  
 如果指定多個標籤資料行，但只允許一個，就會發生例外狀況。  
  
 如果是新的 [標籤] 資料行選取一個以上的資料行，就會發生此錯誤，Azure Machine Learning 中。 最受監督的學習演算法需要單一資料行標示為目標或標籤。  
  
**解決方案：** 請務必選取為新的 [標籤] 資料行的單一資料行。  
  
|例外狀況訊息|  
|------------------------|  
|指定多個標籤資料行。|  
  

## <a name="error-0038"></a>錯誤 0038  
 如果預期的元素數目應該是精確的值，就會發生例外狀況，但不是。  
  
 如果預期的項目數目應該是精確的值，就會發生這個錯誤，Azure Machine Learning 中的，但不是。  如果項目數不等於預期的值有效，您會收到這個錯誤。  
  
**解決方案：** 修改輸入來擁有正確的項目數目。  
  
|例外狀況訊息|  
|------------------------|  
|元素數目不正確。|  
|中的項目數"{0}"無效。|  
|中的項目數 」{0}「 不等於有效數目{1}項目。|  
  

## <a name="error-0039"></a>錯誤 0039  
 如果作業失敗，就會發生例外狀況。  
  
 內部作業無法完成時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 這個錯誤起因於多個條件，而且沒有任何特定的補救方法。  
 下表包含這個錯誤，後面接著特定條件的描述的一般訊息。 
 
 如果沒有詳細資料可供使用，[傳送意見反應](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)，並提供模組產生的錯誤和相關的條件的相關資訊。
  
|例外狀況訊息|  
|------------------------|  
|作業失敗。|  
|完成作業時發生： {0}。|  
  

## <a name="error-0040"></a>錯誤 0040  
 呼叫已被取代的模組時，就會發生例外狀況。  
  
 呼叫已被取代的模組時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 取代已被取代的模組支援。 請參閱改為使用哪一個模組的相關資訊的模組輸出記錄檔。  
  
|例外狀況訊息|  
|------------------------|  
|存取已被取代的模組。|  
|模組 「{0}"已被取代。 使用模組"{1}」 改為。|  
 

## <a name="error-0041"></a>錯誤 0041  
 呼叫已被取代的模組時，就會發生例外狀況。  
  
 呼叫已被取代的模組時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 使用支援的工具組來取代已被取代的模組。 這項資訊應該顯示在模組輸出記錄檔。  
  
|例外狀況訊息|  
|------------------------|  
|存取已被取代的模組。|  
|模組 「{0}"已被取代。 使用 「 模組 」{1}」 要求的功能。|  
 

## <a name="error-0042"></a>錯誤 0042  
 不可能轉換為其他類型的資料行時，就會發生例外狀況。  
  
 不可能將資料行轉換成指定的型別時，就會發生此錯誤，Azure Machine Learning 中。  如果模組需要特定資料類型，例如日期時間、 文字、 浮點數或整數，但不可能將現有的資料行轉換成所需的類型，您會收到這個錯誤。  
 
例如，您可以選取資料行，並嘗試將它轉換成數值資料類型，用於數學運算，並收到這個錯誤，如果資料行包含無效的資料。 

如果您嘗試使用包含浮點數或許多唯一值做為類別的資料行的資料行，您可能會收到此錯誤的另一個原因。 
  
**解決方案：**

+ 開啟產生的錯誤，此模組的說明頁面，並確認資料類型需求。
+ 檢閱輸入資料集中的資料行的資料類型。
+ 檢查產生所謂的無結構描述資料來源中的資料。
+ 請檢查遺漏的值或特殊字元，可能會封鎖所需的資料類型轉換的資料集。 
    + 數值資料類型應該一致： 例如，檢查是否有浮點數的整數資料行中。
    + 尋找文字字串或數字的資料行中的 NA 值。 
    + 布林值可以轉換成適當的表示，根據所需的資料類型而定。
    + 檢查非 unicode 字元、 定位字元或控制字元的文字資料行
    + 日期時間資料應該一致，才能避免發生模型錯誤，但清除可能會很複雜，而許多格式。 請考慮使用 <!--the [Execute R Script](execute-r-script.md) or -->[執行 Python 指令碼](execute-python-script.md)模組執行的清除作業。  
+ 如有必要，修改輸入資料集中的值，使資料行可以順利轉換。 修改可能包含分類收納、 截斷或四捨五入的作業、 刪除極端值或插補遺漏值。 請參閱以下文章中的 machine learning 中的一些常見的資料轉換案例：
    + [清除遺漏的資料](clean-missing-data.md)
    + [將資料標準化，](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> 解決不明確，或不適用您的案例？ 您可以在本文中傳送意見反應，並提供的案例，包括模組和資料行的資料類型的相關資訊。 我們將使用這項資訊，以提供更詳細的疑難排解步驟，在未來。  
  
|例外狀況訊息|  
|------------------------|  
|不允許轉換。|  
|無法轉換類型的資料行{0}類型的資料行{1}。|  
|無法轉換資料行"{2}"的型別{0}類型資料行來{1}。|  
|無法轉換資料行"{2}"的型別{0}資料行 」{3}"類型的{1}。|  
  

## <a name="error-0043"></a>錯誤 0043  
 項目型別不會明確實作 Equals 時，就會發生例外狀況。  
  
 在 Azure Machine Learning 中的此錯誤未使用，而且將會被取代。  
  
**解決方案：** 無。  
  
|例外狀況訊息|  
|------------------------|  
|沒有可存取的明確方法 Equals 找到。|  
|無法比較資料行的值\\」{0}\\"類型的{1}。 沒有可存取的明確方法 Equals 找到。|  


## <a name="error-0044"></a>錯誤 0044  
 不可能從現有值衍生的資料行的項目類型時，就會發生例外狀況。  
  
 不可能推斷出資料行或資料集內的資料行的類型時，就會發生此錯誤，Azure Machine Learning 中。 此外，這通常會發生於使用不同的項目型別串連兩個或多個資料集。 如果無法判斷一般型別可以代表資料行或資料行，而不遺失資訊中的所有值，Azure Machine Learning，它會產生此錯誤。  
  
**解決方案：** 請確定所有的值，指定資料行中合併兩個資料集的內容不是屬於相同類型 （數值、 布林、 類別、 字串、 日期等），或可以強制轉型為相同的類型。  
  
|例外狀況訊息|  
|------------------------|  
|無法衍生資料行的項目類型。|  
|無法衍生資料行的項目類型"{0}」-所有元素都是 null 參考。|  
|無法衍生資料行的項目類型"{0}"的資料集 」{1}」-所有元素都是 null 參考。|  
  

## <a name="error-0045"></a>錯誤 0045  
 不可能建立的資料行，因為來源中的混合的元素類型時，就會發生例外狀況。  
  
 當合併兩個資料集的項目類型不相同，則會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定中合併兩個資料集的指定資料行中的所有值都都屬於相同類型 （數值、 布林、 類別、 字串、 日期等等）。  
  
|例外狀況訊息|  
|------------------------|  
|無法建立混合的元素類型的資料行。|  
|無法建立識別碼資料行"{0}」 的混合的元素類型： \n\tType 資料的 [{1}， {0}] 會{2}\n\tType 資料的 [{3}， {0}] 是{4}。|  
  

## <a name="error-0046"></a>錯誤 0046  
 當您不指定路徑上建立目錄時，就會發生例外狀況。  
  
 不可以指定路徑上建立目錄時，就會發生此錯誤，Azure Machine Learning 中。 如果 Hive 查詢的輸出目錄路徑的任何部分不正確或無法存取，您會收到這個錯誤。  
  
**解決方案：** 再次瀏覽該模組，並確認已正確地格式化的目錄路徑，而且可使用目前的認證存取。  
  
|例外狀況訊息|  
|------------------------|  
|請指定有效的輸出目錄。|  
|目錄：{0}無法建立。 請指定有效的路徑。|  
  

## <a name="error-0047"></a>錯誤 0047  
 如果某些資料集傳遞到模組中的特徵資料行數目太小，就會發生例外狀況。  
  
 如果要訓練的輸入資料集不包含演算法所需要的資料行的最小數目，就會發生此錯誤，Azure Machine Learning 中。 通常是資料集是空的或只包含定型資料行。  
  
**解決方案：** 再次瀏覽要確定有一或多個其他資料行除了標籤資料行的輸入資料集。  
  
|例外狀況訊息|  
|------------------------|  
|在 輸入資料集的特徵資料行數目小於允許最少。|  
|在 輸入資料集的特徵資料行數目小於允許最少的{0}資料行。|  
|在 輸入資料集的特徵資料行數目 」{0}"小於允許最少的{1}資料行。|  
  

## <a name="error-0048"></a>錯誤 0048  
 不可以開啟檔案時，萬一發生例外狀況。  
  
 在 Azure Machine Learning 中的此錯誤發生時就無法開啟檔案進行讀取或寫入。 基於這些理由，您可能會收到此錯誤：  
  
-   容器或檔案 (blob) 不存在  
  
-   檔案或容器的存取層級不允許您存取檔案  
  
-   檔案太大而無法讀取或格式錯誤  
  
**解決方案：** 再次瀏覽此模組，並想要讀取的檔案。  
  
 確認容器和檔案的名稱正確無誤。  
  
 使用 Azure 傳統入口網站或 Azure 儲存體工具來確認您有存取檔案的權限。  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|例外狀況訊息|  
|------------------------|  
|無法開啟檔案。|  
|開啟檔案時發生錯誤： {0}。|  


## <a name="error-0049"></a>錯誤 0049  
 就無法剖析檔案時，萬一發生例外狀況。  
  
 就無法剖析檔案時，就會發生此錯誤，Azure Machine Learning 中。 您會收到這個錯誤，如果在選取的檔案格式[匯入資料](import-data.md)模組不符合實際的格式的檔案，或如果檔案包含無法辨識的字元。  
  
**解決方案：** 再次瀏覽該模組，並更正檔案的格式化選取範圍，如果不符合檔案的格式。 可能的話，請檢查檔案，以確認它並未包含任何不合法的字元。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析檔案。|  
|剖析檔案時發生錯誤： {0}。|  
  

## <a name="error-0050"></a>錯誤 0050  
 當輸入的情況中發生例外狀況，並輸出檔案都相同。  
  
**解決方案：** 在 Azure Machine Learning 中的此錯誤未使用，而且將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|指定的輸入和輸出檔案不能相同。|


## <a name="error-0051"></a>錯誤 0051  
 數個輸出檔案名稱相同時，萬一發生例外狀況。  
  
**解決方案：** 在 Azure Machine Learning 中的此錯誤未使用，而且將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|指定的輸出檔案不能相同。|


## <a name="error-0052"></a>錯誤 0052  
 如果指定的 Azure 儲存體帳戶金鑰不正確，就會發生例外狀況。  
  
 如果用來存取 Azure 儲存體帳戶金鑰不正確，就會發生此錯誤，Azure Machine Learning 中。 例如，您可能會看到此錯誤，如果 Azure 儲存體金鑰已遭截斷時複製並貼上，或使用不正確的金鑰。  
  
 如需如何取得 Azure 儲存體帳戶金鑰的詳細資訊，請參閱[檢視、 複製和重新產生儲存體存取金鑰](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  
  
**解決方案：** 再次瀏覽該模組，並確認 Azure 儲存體金鑰是正確的帳戶;如有必要，請再次複製從 Azure 傳統入口網站的金鑰。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶金鑰不正確。|  
  

## <a name="error-0053"></a>錯誤 0053  
 有沒有使用者特徵或 machbox 建議的項目時，萬一發生例外狀況。  
  
 找不到的特徵向量時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定特徵向量是出現在輸入資料集。  
  
|例外狀況訊息|  
|------------------------|  
|使用者特徵或 / 和項目所需要但未提供。|  

## <a name="error-0054"></a>錯誤 0054  
 如果要完成作業的資料行中相異值太少，就會發生例外狀況。  
  
**解決方案：** 在 Azure Machine Learning 中的此錯誤未使用，而且將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|資料會有完成作業指定的資料行中相異值太少。|  
|資料會有完成作業指定的資料行中相異值太少。 至少需要{0}項目。|  
|資料具有資料行中的相異值太少"{1}」 完成作業。 至少需要{0}項目。|  
  

## <a name="error-0055"></a>錯誤 0055  
 呼叫已被取代的模組時，就會發生例外狀況。  如果您嘗試呼叫已被取代的模組，就會出現在 Azure Machine Learning 中的此錯誤。
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|存取已被取代的模組。|  
|模組 「{0}"已被取代。|  

## <a name="error-0056"></a>錯誤 0056  
 如果您選取作業的資料行違反需求，就會發生例外狀況。  
  
 當您選擇需要的資料行是特定的資料類型的作業的資料行，就會發生此錯誤，Azure Machine Learning 中。 
 
 如果資料行是正確的資料類型，但您使用的模組必須也標示為特徵、 標籤或類別資料行的資料行，也會發生此錯誤。  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**解決方案：**
  
1.  檢閱目前選取的資料行的資料類型。 

2. 確定選取的資料行是否為類別標籤或特徵資料行。  
  
3.  檢閱您所做的資料行選取範圍，以判斷是否有特定需求的資料類型或資料行的使用方式模組的 [說明] 主題。  
  
3.  使用[編輯中繼資料](edit-metadata.md)到這項作業的持續時間內變更資料行類型。 請務必將資料行類型變更回其原始值，並使用另一個執行個體[編輯中繼資料](edit-metadata.md)，如果您需要針對下游的作業。  
  
|例外狀況訊息|  
|------------------------|  
|一或多個選取的資料行不是處於允許的類別。|  
|資料行具有名稱"{0}」 不在允許的類別。|  
  

## <a name="error-0057"></a>錯誤 0057  
 當您嘗試建立檔案或 blob 已存在時，就會發生例外狀況。  
  
 當您使用時，就會發生這個例外狀況[匯出資料](export-data.md)模組或其他模組，將 Azure Machine Learning 中的實驗的結果儲存到 Azure blob 儲存體，但您嘗試建立檔案或 blob 已存在。   
  
**解決方案：**
 
 您會收到這個錯誤，您先前設定的屬性時，才**Azure blob 儲存體寫入模式**要**錯誤**。 根據設計，此模組會引發錯誤，如果您嘗試寫入已經存在的 blob 資料集。
 
 - 開啟模組屬性，並變更的屬性**Azure blob 儲存體寫入模式**要**覆寫**。
 - 或者，您可以輸入不同的目的地 blob 或檔案的名稱，並務必指定尚未存在的 blob。  
  
|例外狀況訊息|  
|------------------------|  
|檔案或 Blob 已經存在。|  
|檔案或 Blob 」{0}"已經存在。|  
  

## <a name="error-0058"></a>錯誤 0058  
 如果資料集不包含預期的標籤資料行，就會發生此錯誤，Azure Machine Learning 中。  
  
 標籤資料行提供資料或學習模組時，所預期的資料類型不符時，也會發生這個例外狀況，或有錯誤的值。 例如，當定型二元分類器時，請使用實際值的標籤資料行，則會產生這個例外狀況。  
  
**解決方案：** 解決方式取決於學習模組或您使用的培訓講師和在資料集中的資料行的資料類型。 首先，請確認機器學習演算法或訓練模組的需求。  
  
 再次瀏覽輸入資料集。 請確認，您要被視為標籤具有正確的資料，輸入您要建立模型資料行。  
  
 檢查表示遺漏值的輸入，並刪除或取代它們，如有必要。  
  
 如有需要，加入[編輯中繼資料](edit-metadata.md)模組，並確定 [標籤] 資料行已標示為標籤。  
  
|例外狀況訊息|  
|------------------------|  
|標籤資料行不是預期|  
|標籤資料行不是預期中 「{0}"。|  
|標籤資料行"{0}"中不應該使用 「{1}"。|  
  

## <a name="error-0059"></a>錯誤 0059  
 如果無法剖析指定的資料行選擇器中的資料行索引，就會發生例外狀況。  
  
 如果無法剖析使用資料行選取器時指定的資料行索引，就會發生此錯誤，Azure Machine Learning 中。  無效的格式無法剖析的資料行索引時，您會收到這個錯誤。  
  
**解決方案：** 修改為使用有效的索引值的資料行索引。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析一個或多個指定的資料行索引或索引範圍。|  
|資料行索引或範圍"{0}"無法剖析。|  
  

## <a name="error-0060"></a>錯誤 0060  
 資料行選擇器中指定超出範圍的資料行範圍時，就會發生例外狀況。  
  
 資料行選取器中指定的範圍外的資料行範圍時，就會發生此錯誤，Azure Machine Learning 中。 如果資料行選擇器中的資料行範圍並未對應到資料集中的資料行，您會收到這個錯誤。  
  
**解決方案：** 修改資料行選擇器，以對應至資料集內的資料行的資料行範圍。  
  
|例外狀況訊息|  
|------------------------|  
|無效或超出指定範圍的資料行索引範圍。|  
|資料行範圍"{0}"是無效或超出範圍。|  
  

## <a name="error-0061"></a>錯誤 0061  
 當您嘗試將一個資料列加入至 DataTable，但包含不同數目的資料行與資料表時，就會發生例外狀況。  
  
 當您嘗試將一個資料列新增至擁有不同數目的資料行比資料集的資料集時，就會發生此錯誤，Azure Machine Learning 中。  如果要加入至資料集的資料列有不同數目的資料行在輸入資料集，您會收到這個錯誤。  資料列無法附加至資料集，如果資料行數目不同。  
  
**解決方案：** 修改輸入資料集有相同數目的資料行與資料列加入，或修改資料列加入至具有相同數目的資料行的資料集。  
  
|例外狀況訊息|  
|------------------------|  
|所有的資料表必須有相同數目的資料行。|  
  

## <a name="error-0062"></a>錯誤 0062  
 當您嘗試比較兩個具有不同學習類型的模型時，就會發生例外狀況。  
  
 當無法比較兩個不同的計分資料集的評估度量，則會產生 Azure Machine Learning 中的此錯誤。 在此情況下，不可能比較用來產生兩個評分資料集之模型的效能。  
  
**解決方案：** 確認 計分的結果所產生的相同類型的機器學習服務模型 （二進位分類、 迴歸、 多類別分類、 叢集、 異常偵測等等的建議。）您比較的所有模型必須都具有相同的學習類型。  
  
|例外狀況訊息|  
|------------------------|  
|所有模型都必須都具有相同的學習類型。|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|例外狀況訊息|  
|------------------------|  
|R 指令碼評估期間發生錯誤。|  
|R 指令碼評估期間發生下列錯誤:---從 R 錯誤訊息開始--- {0} --R 錯誤訊息結尾--美元--|  
|在 R 指令碼評估期間 」{1}"發生下列錯誤:---從 R 錯誤訊息開始--- {0} --R 錯誤訊息結尾--美元--|  
  


## <a name="error-0064"></a>錯誤 0064  
 如果指定 Azure 儲存體帳戶名稱或儲存體金鑰不正確，就會發生例外狀況。  
  
 如果指定 Azure 儲存體帳戶名稱或儲存體金鑰不正確，就會發生此錯誤，Azure Machine Learning 中。 如果您輸入了不正確的帳戶名稱或密碼的儲存體帳戶，您會收到這個錯誤。 如果您手動輸入帳戶名稱或密碼，也可能會發生。 如果帳戶已被刪除時，它也可能發生。  
  
**解決方案：** 請確認，帳戶名稱和密碼輸入正確，且該帳戶存在。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶名稱或儲存體金鑰不正確。|  
|Azure 儲存體帳戶名稱"{0}」 或儲存體帳戶名稱的金鑰不正確。|  
  

## <a name="error-0065"></a>錯誤 0065  
 如果指定的 Azure blob 名稱不正確，就會發生例外狀況。  
  
 如果指定的 Azure blob 名稱不正確，就會發生此錯誤，Azure Machine Learning 中。  如果，您會收到錯誤：  
  
-   在指定的容器中找不到 blob。  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   只有容器指定為中的來源[匯入資料](import-data.md)要求的格式是以編碼方式與 Excel 或 CSV 時，串連的容器內的所有 blob 的內容不允許使用這些格式。  
  
-   SAS URI 不包含有效的 blob 名稱。  
  
**解決方案：** 再次瀏覽模組擲回例外狀況。 請確認指定的 blob 存在的容器中的儲存體帳戶和權限，讓您看到 blob。 確認輸入的表單**containername/filename**假設您有 Excel 或 CSV 格式的編碼方式。 確認 SAS URI 包含有效的 blob 名稱。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體 blob 不正確。|  
|Azure 儲存體 blob 名稱"{0}「 不正確|  
  

## <a name="error-0066"></a>錯誤 0066  
 如果資源無法上傳至 Azure Blob，就會發生例外狀況。  
  
 如果資源無法上傳至 Azure Blob，就會發生此錯誤，Azure Machine Learning 中。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 兩者都會儲存到包含的輸入的檔的帳戶相同的 Azure 儲存體帳戶。  
  
**解決方案：** 再次瀏覽該模組。 確認 Azure 帳戶名稱、 儲存體金鑰和容器都正確無誤的帳戶具有寫入容器的權限。  
  
|例外狀況訊息|  
|------------------------|  
|資源無法上傳至 Azure 儲存體。|  
|檔案"{0}"無法上傳至 Azure 儲存體作為{1}。|  
  

## <a name="error-0067"></a>錯誤 0067  
 如果資料集具有不同數目的非預期的資料行，就會發生例外狀況。  
  
 如果資料集具有不同數目的非預期的資料行，就會發生此錯誤，Azure Machine Learning 中。  此模組需要有在執行期間的資料行數目不同的資料集內的資料行數目時，您會收到這個錯誤。  
  
**解決方案：** 修改輸入資料集或參數。  
  
|例外狀況訊息|  
|------------------------|  
|非預期的數目的 datatable 中的資料行的詳細資訊。|  
|必須是"{0}"資料行卻找到"{1}」 資料行改為。|  
  

## <a name="error-0068"></a>錯誤 0068  
 如果指定的 Hive 指令碼不正確，就會發生例外狀況。  
  
 如果 Hive 的 SQL 指令碼中有語法錯誤或 Hive 解譯器發生錯誤時執行查詢或指令碼，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**

從 Hive 中的錯誤訊息會正常回報錯誤記錄檔中，讓您可以採取動作，根據特定的錯誤。 

+ 開啟的模組，並檢查錯誤的查詢。  
+ 確認查詢運作正確 Azure Machine Learning 外部登入 Hadoop 叢集的 Hive 主控台並執行查詢。  
+ 請嘗試在您的 Hive 指令碼，在不同的行，而不是混合使用可執行陳述式與在同一行的註解中放置註解。  

### <a name="resources"></a>資源

請參閱下列文章來說明使用適用於 machine learning 的 Hive 查詢：

+ [建立 Hive 資料表，並從 Azure Blob 儲存體載入資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [探索使用 Hive 查詢的資料表中的資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [針對使用 Hive 查詢之 Hadoop 叢集中的資料建立特性](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive 的 SQL 使用者小祕技 (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|例外狀況訊息|  
|------------------------|  
|Hive 指令碼不正確。|  
|Hive 指令碼{0}不正確。|  
  

## <a name="error-0069"></a>錯誤 0069  
 如果指定的 SQL 指令碼不正確，就會發生例外狀況。  
  
 如果指定的 SQL 指令碼語法的問題，或如果資料行或資料表中的指定指令碼無效，就會發生此錯誤，Azure Machine Learning 中。 
 
 如果 SQL 引擎執行查詢或指令碼時遇到任何錯誤，您會收到這個錯誤。 SQL 錯誤訊息會正常回報錯誤記錄檔中，讓您可以採取動作，根據特定的錯誤。  
  
**解決方案：** 再次瀏覽該模組，並檢查錯誤的 SQL 查詢。  
  
 確認查詢運作正確 Azure ML 之外直接登入的資料庫伺服器，並執行查詢。  
  
 如果沒有報告模組例外狀況所產生的 SQL 訊息，需要根據報告的錯誤。 比方說，錯誤訊息有時會包含可能會發生錯誤的特定指引：
+ *沒有這類資料行或遺漏資料庫*，指出，您可能輸入錯誤的資料行名稱。 如果您不確定資料行名稱正確無誤，請嘗試使用方括號或引號來括住的資料行識別碼。
+ *SQL 邏輯錯誤，接近\<SQL 關鍵字\>* ，表示您可能必須先指定關鍵字的語法錯誤

  
|例外狀況訊息|  
|------------------------|  
|SQL 指令碼不正確。|  
|SQL 查詢"{0}「 不正確。|  
|SQL 查詢"{0}「 不正確： {1}|  
  

## <a name="error-0070"></a>錯誤 0070  
 當您嘗試存取不存在的 Azure 資料表時，就會發生例外狀況。  
  
 當您嘗試存取不存在的 Azure 資料表時，就會發生此錯誤，Azure Machine Learning 中。 如果您沒有讀取或寫入 Azure 資料表儲存體時的 Azure 儲存體中指定的資料表，您會收到這個錯誤。 如果您拼錯所需的資料表名稱，或是您所需目標名稱和儲存體類型之間不相符，會發生這項目。 比方說，您要從資料表讀取，但改為輸入 blob 的名稱。  
  
**解決方案：** 再次瀏覽以確認資料表的名稱是正確的模組。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 資料表不存在。|  
|Azure 資料表 」{0}"不存在。|  
  
## <a name="error-0071"></a>錯誤 0071  
 如果提供的認證不正確，就會發生例外狀況。  
  
 如果提供的認證不正確，就會發生此錯誤，Azure Machine Learning 中。  
  
 如果模組無法連線到 HDInsight 叢集，您也可能會收到這個錯誤。  
  
**解決方案：** 檢閱模組的輸入並確認帳戶名稱和密碼。  
  
 請檢查下列可能會導致錯誤的問題：  
  
-   資料集的結構描述不符合目的地 datatable 的結構描述。  
  
-   資料行名稱會記載或拼錯了  
  
-   您要寫入已有不合法的字元資料行名稱的資料表。 通常您可以將這類資料行名稱括在方括號，但如果這樣做無效，編輯資料行名稱使用字母和底線 (_)  
  
-   您嘗試要寫入的字串包含單引號  
  
 如果您嘗試連線到 HDInsight 叢集，確認可使用提供的認證存取目標叢集。  
  
|例外狀況訊息|  
|------------------------|  
|不正確的認證會傳遞。|  
|不正確的使用者名稱 」{0}」 或傳遞密碼|  
  

## <a name="error-0072"></a>錯誤 0072  
 在連接逾時的情況下，發生例外狀況。  
  
 當連線逾時，就會發生此錯誤，Azure Machine Learning 中。如果目前的連線問題進行的資料來源或目的地，例如慢速的網際網路連線，或如果資料集很龐大，及/或 SQL 查詢來讀取資料執行複雜的處理，您會收到這個錯誤。  
  
**解決方案：** 判斷是否有目前低速連線至 Azure 儲存體或整個網際網路的問題。  
  
|例外狀況訊息|  
|------------------------|  
|發生連接逾時。|  
  

## <a name="error-0073"></a>錯誤 0073  
 如果發生錯誤時將資料行轉換成另一種類型，就會發生例外狀況。  
  
 不可能轉換為其他類型的資料行時，就會發生這個錯誤，Azure Machine Learning 中。  如果模組需要特定的型別，而不是將資料行轉換至新的類型，您會收到這個錯誤。  
  
**解決方案：** 修改輸入資料集，以便根據內部例外狀況轉換的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|無法轉換資料行。|  
|無法轉換資料行{0}。|  
  

## <a name="error-0074"></a>錯誤 0074  
 發生例外狀況時[編輯中繼資料](edit-metadata.md)嘗試將疏鬆資料行轉換成類別目錄。  
  
 在 Azure Machine Learning 中的此錯誤發生時[編輯中繼資料](edit-metadata.md)嘗試將疏鬆資料行轉換成類別目錄。  嘗試將疏鬆資料行轉換成類別目錄使用時，您會收到這個錯誤**變成類別**選項。  Azure 機器學習服務不支援疏鬆類別的陣列，因此，模組會失敗。  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|例外狀況訊息|  
|------------------------|  
|疏鬆資料行無法轉換成 「 類別。|  
  

## <a name="error-0075"></a>錯誤 0075  
量化資料集使用無效的分類收納函數時，就會發生例外狀況。  
  
當您嘗試要分類收納資料使用不支援的方法，或參數組合無效時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**

錯誤處理這個事件是在較早版本的 Azure Machine Learning 中允許更多自訂的分類收納方法引進。 目前所有的分類收納方法根據選取項目，從下拉式清單中，我承認不應可能發生這個錯誤。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|例外狀況訊息|  
|------------------------|  
|使用了無效的分類收納函數。|  
  

## <a name="error-0077"></a>錯誤 0077  
 未知的 blob 檔案寫入模式傳遞時，就會發生例外狀況。  
  
 如果傳遞無效的引數中的 blob 檔案的目的地或來源的規格，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 在匯入或匯出資料至 / 從 Azure blob 儲存體的幾乎所有模組，控制的寫入模式的參數值會指派使用下拉式清單中;因此，不可能傳遞無效的值，而且不應該出現此錯誤。 此錯誤會被取代，未來版本中。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的 blob 寫入模式。|  
|不支援的 blob 寫入模式： {0}。|  
  

## <a name="error-0078"></a>錯誤 0078  
 例外狀況，就會發生 HTTP 選項何時[匯入資料](import-data.md)收到 3xx 狀態碼表示重新導向。  
  
 在 Azure Machine Learning 中的此錯誤，就會發生 HTTP 選項時[匯入資料](import-data.md)收到 3xx (301、 302、 304，等等) 狀態碼表示重新導向。 如果您嘗試連接至 HTTP 來源瀏覽器重新導向至其他頁面，您會收到這個錯誤。 安全性考量，將重新導向的網站不允許做為資料來源 Azure Machine Learning。  
  
**解決方案：** 如果網站是一個受信任的網站，請直接輸入重新導向的 URL。  
  
|例外狀況訊息|  
|------------------------|  
|不允許的 http 重新導向|  
  

## <a name="error-0079"></a>錯誤 0079  
 如果指定的 Azure 儲存體容器名稱不正確，就會發生例外狀況。  
  
 如果指定的 Azure 儲存體容器名稱不正確，就會發生此錯誤，Azure Machine Learning 中。 如果您沒有指定的容器和 blob （檔案） 的名稱使用，您會收到這個錯誤**blob 容器開頭的路徑**寫入 Azure Blob 儲存體時的選項。  
  
**解決方案：** 再次瀏覽[匯出資料](export-data.md)模組，並確認 blob 指定的路徑包含容器和檔案名稱，格式**容器/filename**。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體容器名稱不正確。|  
|Azure 儲存體容器名稱"{0}「 不正確; 格式的容器或 blob 容器名稱。|  
  

## <a name="error-0080"></a>錯誤 0080  
 當模組不允許完全為遺漏值的資料行時，就會發生例外狀況。  
  
 當一或多個模組所耗用的資料行包含所有的遺漏值，則會產生 Azure Machine Learning 中的此錯誤。 比方說，如果模組計算每個資料行的彙總統計資料，它無法包含任何資料的資料行上。 在此情況下，模組執行停止與此例外狀況。  
  
**解決方案：** 再次瀏覽輸入資料集，並移除任何包含所有的遺漏值的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|不允許完全為遺漏值的資料行。|  
|資料行{0}已完全為遺漏值。|  
  

## <a name="error-0081"></a>錯誤 0081  
 如果要減少維度的數目等於數字的特徵資料行中包含至少一個疏鬆特徵資料行的輸入資料集，可在 PCA 模組中發生例外狀況。  
  
 如果符合下列條件時，系統會產生 Azure Machine Learning 中的這個錯誤: （a） 的輸入資料集具有至少一個疏鬆資料行，而且 （b） 最後一個要求的維度數目與輸入維度的數目相同。  
  
**解決方案：** 請考慮減少要輸入中的維度數目少於輸出中的維度數目。 這是典型的 PCA 的應用程式中。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|例外狀況訊息|  
|------------------------|  
|包含疏鬆特徵資料行的資料集，以減少維度數目應該小於特徵資料行的數目。|  
 

## <a name="error-0082"></a>錯誤 0082  
 模型無法成功還原序列化時，就會發生例外狀況。  
  
 當已儲存的機器學習模型時，會發生此錯誤，Azure Machine Learning 中的，或由較新版的 Azure Machine Learning 執行階段因重大變更而無法載入轉換。  
  
**解決方案：** 重新執行和模型，必須是所產生的模型或轉換的訓練實驗，或必須重新儲存轉換。  
  
|例外狀況訊息|  
|------------------------|  
|無法還原序列化模型，因為它可能會以較舊的序列化格式序列化。 重新定型，並重新儲存模型。|  
  

## <a name="error-0083"></a>錯誤 0083  
 如果資料集用於培訓不適用於具體的學習類型時，發生例外狀況。  
  
 定型的學習因子和不相容的資料集時，會產生 Azure Machine Learning 中的此錯誤。 例如，資料集可能包含在每個資料列，至少一個遺漏值，如此一來，整個資料集則會略過在定型期間。 在其他情況下，有些機器學習演算法，例如異常偵測不想要顯示的標籤，並可以擲回這個例外狀況，如果標籤會出現在資料集。  
  
**解決方案：** 要用來檢查輸入資料集的需求學習模組的文件，請參閱。 檢查要查看存在的所有必要的資料行的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|用於定型的資料集無效。|  
|{0} 包含無效的資料進行訓練。|  
|{0} 包含無效的資料進行訓練。 學習類型： {1}。|  
  

## <a name="error-0084"></a>錯誤 0084  
 要在評估從 R 指令碼所產生的分數時，就會發生例外狀況。 這是目前不支援。  
  
 如果您嘗試使用其中一個模組來評估模型輸出包含分數的 R 指令碼，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|您是目前不支援評估 R 產生的分數。|  
  

## <a name="error-0085"></a>錯誤 0085  
 指令碼評估失敗並發生錯誤時，就會發生例外狀況。  
  
 當您執行自訂指令碼包含語法錯誤，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 檢閱您的程式碼，在外部編輯器，並檢查發生錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|指令碼評估期間發生錯誤。|  
|在指令碼評估期間發生下列錯誤檢視輸出記錄檔，如需詳細資訊:---錯誤訊息，從開始{0}解譯器---美元{1}---錯誤訊息結尾{0}解譯器---美元------|  
  

## <a name="error-0086"></a>錯誤 0086  
 計數轉換無效時，就會發生例外狀況。  
  
 當您選擇計數資料表為基礎的轉換，但選取的轉換是以目前的資料，或以新的計數資料表不相容時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 此模組支援將儲存的計數和組成兩個不同的格式轉換的規則。 如果您要合併計數資料表，請確認您想要合併兩個資料表使用相同的格式。  
  
一般情況下，計數為基礎的轉換只會套用至有原先建立轉換的資料集相同的結構描述的資料集。  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|例外狀況訊息|  
|------------------------|  
|指定了無效的計數轉換。|  
|輸入連接埠上的計數轉換 '{0}' 無效。|  
|輸入連接埠上的計數轉換 '{0}'無法合併使用輸入連接埠上的計數轉換'{1}'。 檢查以確認用來計算相符項目中繼資料。|  
  

## <a name="error-0087"></a>錯誤 0087  
 為計數模組的學習指定了無效的計數資料表類型時，就會發生例外狀況。  
  
 當您嘗試匯入現有的計數資料表，但資料表是以目前的資料，或以新的計數資料表不相容時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 有不同的格式來儲存轉換所組成的規則與計數。 如果您要合併計數資料表，請確認兩者都使用相同的格式。  
  
 一般而言，計數為基礎的轉換只會套用至有原先建立轉換的資料集相同的結構描述的資料集。  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>錯誤 0088  
 例外狀況發生於了無效的計數類型指定為計數模組的學習。  
  
 發生此錯誤在 Azure Machine Learning 中的當您嘗試使用不同的計算方法，比支援以計數為基礎的特徵化。  
  
**解決方案：** 一般情況下，計算的方法是從選擇下拉式清單中，因此您應該不會看到此錯誤。  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|例外狀況訊息|  
|------------------------|  
|指定無效的計數類型。|  
|指定的計數類型 '{0}' 不是有效的計數類型。|  
  

## <a name="error-0089"></a>錯誤 0089  
 當指定的類別數目小於中用於計算的資料集類別的實際數目時，就會發生例外狀況。  
  
 當您要建立計數資料表，而且標籤資料行包含不同數目的比您的模組參數中指定的類別時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 檢查您的資料集，並找出完全相異值數目 （可能的類別） 標籤資料行中。 當您建立計數資料表時，您必須至少指定類別的這個數字。  
  
 計數資料表無法自動判斷可用的類別數目。  
  
 當您建立計數資料表時，您不能指定 0，或任何數字低於標籤資料行中的類別的實際數目。  
  
|例外狀況訊息|  
|------------------------|  
|類別數目不正確。 請確定您在 [參數] 窗格中指定的類別數目大於或等於標籤資料行中的類別數目。|  
|指定的類別數目是 '{0}'，這是不大於某個標籤值'{1}' 用來計算的資料集內。 請確定您在 [參數] 窗格中指定的類別數目大於或等於標籤資料行中的類別數目。|  
  

## <a name="error-0090"></a>錯誤 0090  
 建立 Hive 資料表失敗時，就會發生例外狀況。  
  
 當您使用時，會發生此錯誤，在 Azure Machine Learning[匯出資料](export-data.md)或無法建立另一個選項，將資料儲存到 HDInsight 叢集，而且指定的 Hive 資料表。  
  
**解決方案：** 檢查與叢集相關聯的 Azure 儲存體帳戶名稱，並確認您使用相同的帳戶在模組屬性。  
  
|例外狀況訊息|  
|------------------------|  
|無法建立 Hive 資料表。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱等同於功能透過模組參數傳入。|  
|Hive 資料表 」{0}「 無法建立。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱等同於功能透過模組參數傳入。|  
|Hive 資料表 」{0}「 無法建立。 針對 HDInsight 叢集，請確認與叢集相關聯的 Azure 儲存體帳戶名稱是"{1}」。|  
 

## <a name="error-0100"></a>錯誤 0100  
 不支援的語言指定自訂的模組時，就會發生例外狀況。  
  
 建置自訂模組和 name 屬性時，會發生此錯誤，在 Azure Machine Learning**語言**自訂模組 xml 定義檔中的項目有無效的值。 目前，唯一有效的值，這個屬性是`R`。 例如:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**解決方案：** 確認的 name 屬性**語言**自訂模組的 xml 定義檔中的項目設為`R`。 儲存檔案，會更新自訂模組 zip 套件，並嘗試再次新增自訂的模組。  
  
|例外狀況訊息|  
|------------------------|  
|指定不支援的自訂模組語言|  
  

## <a name="error-0101"></a>錯誤 0101  
 所有的連接埠和參數識別碼必須是唯一的。  
  
 一個或多個連接埠參數指派自訂模組 XML 定義檔中相同的識別碼值時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 請檢查跨所有連接埠和參數的識別碼值是唯一的。 儲存 xml 檔案，更新的自訂模組 zip 套件，並嘗試再次新增自訂的模組。  
  
|例外狀況訊息|  
|------------------------|  
|所有的連接埠和模組的參數識別碼必須是唯一的|  
|模組 '{0}' 有重複的連接埠/引數識別碼。 所有的連接埠/引數識別碼必須是唯一的。|  
  

## <a name="error-0102"></a>錯誤 0102  
 無法解壓縮 ZIP 檔案時，便會擲回。  
  
 當您要匯入壓縮的封裝，副檔名為.zip，但是封裝不，會發生此錯誤，Azure Machine Learning 中的的 zip 檔案或檔案不會使用支援的 zip 格式。  
  
**解決方案：** 請確定選取的檔案是有效的.zip 檔案，而且它使用其中一種支援的壓縮演算法壓縮。  
  
 如果匯入以壓縮格式的資料集時，您會收到這個錯誤，請確認所有其包含的檔案使用其中一個支援的檔案格式，並會以 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 請嘗試重新所需的檔案新增至新的壓縮 zip 資料夾，並嘗試再次新增自訂的模組。  
  
|例外狀況訊息|  
|------------------------|  
|指定的 ZIP 檔案不是正確的格式|  


## <a name="error-0103"></a>錯誤 0103  
 ZIP 檔案不包含任何.xml 檔案時，會擲回  
  
 自訂模組 zip 套件不包含任何模組定義 (.xml) 檔案時，就會發生此錯誤，Azure Machine Learning 中。 這些檔案必須位於根目錄中的 zip 套件 （例如，不在子資料夾中。）  
  
**解決方案：** 確認一或多個 xml 模組定義檔的 zip 套件的根資料夾中擷取磁碟機上的暫存資料夾。 任何 xml 檔案應該直接在您解壓縮 zip 封裝的資料夾中。 請確定當您建立 zip 套件，您未選取包含要壓縮，這會建立子資料夾內的 zip 套件與您選取要壓縮的資料夾名稱相同的 xml 檔案的資料夾。  
  
|例外狀況訊息|  
|------------------------|  
|指定的 ZIP 檔案不包含任何模組定義檔 （.xml 檔案）|  


## <a name="error-0104"></a>錯誤 0104  
 模組定義檔參考找不到的指令碼時，擲回  
  
 將自訂模組的 xml 定義檔案參考中的指令碼檔案時擲回這個錯誤，在 Azure Machine Learning**語言**不存在的 zip 套件中的項目。 中所定義的指令碼檔案路徑**sourceFile**屬性**語言**項目。 原始程式檔的路徑是相對於根目錄的 zip 套件 （與模組的 xml 定義檔案相同的位置）。 如果指令碼檔案位於子資料夾，就必須指定指令碼檔案的相對路徑。 比方說，如果所有的指令碼儲存在**myScripts** zip 封裝內的資料夾**語言**項目就必須新增此路徑至**sourceFile**屬性設為下面。 例如:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**解決方案：** 請確定值**sourceFile**中的屬性**語言**自訂模組的 xml 定義的項目正確，且存在於正確的相對路徑的 zip 套件中的原始程式檔。  
  
|例外狀況訊息|  
|------------------------|  
|參考的 R 指令碼檔案不存在。|  
|參考的 R 指令碼檔案 '{0}' 找不到。 請確定檔案的相對路徑正確，從定義位置。|  


## <a name="error-0105"></a>錯誤 0105  
 模組定義檔包含不支援的參數類型時，會顯示此錯誤  
  
 當您建立的自訂模組的 xml 定義，而且參數或在定義中的引數的型別不符合支援的型別，則會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定 [類型] 屬性的任何**Arg**自訂模組的 xml 定義檔中的項目是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的參數類型。|  
|不支援的參數類型 '{0}' 指定。|  


## <a name="error-0106"></a>錯誤 0106  
 模組定義檔定義不支援的輸入的類型時擲回  
  
 在 XML 定義的自訂模組的輸入連接埠的類型不符合支援的型別時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定在自訂模組的 XML 定義檔中之 Input 元素的類型屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸入的類型。|  
|不支援的輸入型別 '{0}' 指定。|  


## <a name="error-0107"></a>錯誤 0107  
 模組定義檔定義不支援的輸出類型時擲回  
  
 在自訂模組的 xml 定義中的輸出連接埠的類型不符合支援的型別時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定輸出中的項目自訂模組的 xml 定義檔的類型屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸出型別。|  
|不支援的輸出類型 '{0}' 指定。|  


## <a name="error-0108"></a>錯誤 0108  
 模組定義檔定義多個輸入或輸出連接埠多於受支援時擲回  
  
 當自訂模組的 xml 定義中所定義的輸入或輸出連接埠太多，則會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 可確定在自訂模組 xml 定義中定義的輸入和輸出連接埠的最大數目未超過支援的通訊埠的最大數目。  
  
|例外狀況訊息|  
|------------------------|  
|超過支援的輸入或輸出連接埠數目。|  
|支援的數目超過 '{0}' 連接埠。 允許的數目上限 '{0}'的連接埠是'{1}'。| 

## <a name="error-0109"></a>錯誤 0109  
 模組定義檔定義資料行選擇器不正確時擲回  
  
 當資料行選擇器引數的語法包含自訂模組的 xml 定義中的錯誤，則會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 當資料行選擇器引數的語法包含自訂模組的 xml 定義中的錯誤時，會產生這個錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|不支援資料行選擇器的語法。|  
  

## <a name="error-0110"></a>錯誤 0110  
 模組定義檔定義資料行選擇器參考不存在的輸入連接埠識別碼時擲回  
  
 會產生這個錯誤，Azure Machine Learning 中的時*portId* ColumnPicker 型別的引數的屬性項目內的屬性不符合輸入的連接埠的識別碼值。  
  
**解決方案：** 請確定 portId 屬性符合自訂模組的 xml 定義中所定義的輸入連接埠的識別碼值。  
  
|例外狀況訊息|  
|------------------------|  
|資料行選擇器參考不存在的輸入連接埠識別碼。|  
|資料行選擇器參考不存在的輸入連接埠識別碼 '{0}'。|  
  

## <a name="error-0111"></a>錯誤 0111  
 模組定義檔定義不正確的屬性時擲回  
  
 無效的屬性指派給 XML 定義的自訂模組中的項目時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定自訂模組項目所支援的屬性。  
  
|例外狀況訊息|  
|------------------------|  
|屬性定義無效。|  
|屬性定義 '{0}' 無效。|  
  

## <a name="error-0112"></a>錯誤 0112  
 無法剖析模組定義檔時，會擲回  
  
 Xml 格式，以防止自訂模組進行剖析為有效的 XML 檔案的 XML 定義錯誤時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定每個項目是開啟和關閉正確。 請確定在 XML 格式中沒有任何錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析模組定義檔。|  
|無法剖析模組定義檔 '{0}'。|  
  

## <a name="error-0113"></a>錯誤 0113  
 模組定義檔包含錯誤時，便會擲回。  
  
 可以剖析 XML 定義檔的自訂模組，但包含錯誤，例如定義的自訂模組不支援的項目時，會產生 Azure Machine Learning 中的此錯誤。  
  
**解決方案：** 請確定自訂模組定義檔定義項目和自訂模組所支援的屬性。  
  
|例外狀況訊息|  
|------------------------|  
|模組定義檔包含錯誤。|  
|模組定義檔 '{0}' 包含錯誤。|  
|模組定義檔 '{0}' 包含錯誤。 [https://doi.org/10.13012/J8PN93H8]({1})|  
  

## <a name="error-0114"></a>錯誤 0114  
 建置自訂模組失敗時擲回。  
  
 在自訂模組建置失敗時，會產生 Azure Machine Learning 中的此錯誤。 這發生在一個或多個自訂模組相關的錯誤時發生新增自訂的模組。 其他錯誤會報告此錯誤訊息中。  
  
**解決方案：** 解決此例外狀況訊息中報告錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|無法建置自訂模組。|  
|自訂模組建置失敗，發生錯誤： {0}|  
  

## <a name="error-0115"></a>錯誤 0115  
 自訂模組的預設指令碼有不支援的延伸模組時，便會擲回。  
  
 當您使用未知的檔案名稱擴充功能的自訂模組提供指令碼時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 請確認自訂模組內含的任何指令碼檔案的檔案格式和檔案名稱副檔名。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的擴充功能的預設指令碼。|  
|不支援的檔案副檔名{0}預設指令碼。|  
  

## <a name="error-0121"></a>錯誤 0121  
 SQL 寫入失敗，因為資料表無法寫入時擲回  
  
 當您使用，則會產生這個錯誤，在 Azure Machine Learning[匯出資料](export-data.md)模組將結果儲存到 SQL database 中的資料表和資料表無法寫入。 通常，您會看到此錯誤如果[匯出資料](export-data.md)模組已成功建立與 SQL Server 執行個體的連線，但就無法寫入資料表中的 Azure ML 資料集的內容。  
  
**解決方案：**
 - 開啟的 [屬性] 窗格[匯出資料](export-data.md)模組並確認已正確輸入的資料庫和資料表名稱。 
 - 檢閱您要匯出，並確定資料適用於目的地資料表的資料集的結構描述。
 - 確認 SQL 登入相關聯的使用者名稱和密碼具有寫入資料表的權限。 
 - 如果例外狀況包含從 SQL Server 的其他錯誤資訊，請使用該資訊，來進行修正。  
  
|例外狀況訊息|  
|------------------------|  
|連接到伺服器，無法寫入至資料表。|  
|無法寫入至 Sql 資料表： {0}|  


## <a name="error-0122"></a>錯誤 0122  
 如果指定多個加權資料行，但只允許一個，就會發生例外狀況。  
  
 已選取太多資料行做為加權資料行時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 檢閱輸入的資料集和它的中繼資料。 請確定該只有一個資料行包含的加權。  
  
|例外狀況訊息|  
|------------------------|  
|指定多個加權資料行。|  


## <a name="error-0123"></a>錯誤 0123  
 如果向量的資料行指定為標籤資料行，就會發生例外狀況。  
  
 如果您使用的向量為標籤資料行，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 變更資料格式的資料行如有必要，或選擇不同的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|向量的資料行指定為標籤資料行。|  


## <a name="error-0124"></a>錯誤 0124  
 如果非數字資料行指定為加權資料行，就會發生例外狀況。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|非數字資料行指定為加權資料行。|  
  


## <a name="error-0125"></a>錯誤 0125  
 當多個資料集的結構描述不符時，便會擲回。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|資料集結構描述不符。|  


## <a name="error-0126"></a>錯誤 0126  
 如果使用者指定不支援在 Azure ML 中的 SQL 網域，就會發生例外狀況。  
  
 當使用者指定不支援在 Azure Machine Learning 中的 SQL 網域時，會產生這個錯誤。 如果您嘗試連接到資料庫伺服器未列入允許清單的網域中，您會收到這個錯誤。 目前允許的 SQL 網域為:"。 database.windows.net"，"。 cloudapp.net"，或 「。 database.secure.windows.net"。 也就是說，伺服器必須是 Azure SQL 伺服器或 Azure 上的虛擬機器中的伺服器。  
  
**解決方案：** 再次瀏覽該模組。 確認 SQL database 伺服器屬於其中一個可接受的網域：  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|例外狀況訊息|  
|------------------------|  
|不支援的 SQL 網域。|  
|SQL 網域{0}目前不支援在 Azure ML 中|  
  

## <a name="error-0127"></a>錯誤 0127  
 影像像素大小超過允許的限制  
  
 如果您從分類影像資料集來讀取映像和映像會大於模型可以處理，就會發生此錯誤。  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|例外狀況訊息|  
|------------------------|  
|影像像素大小超過允許的限制。|  
|映像檔中的像素大小 '{0}' 超過允許的限制: '{1}'|  


## <a name="error-0128"></a>錯誤 0128  
 類別資料行的條件機率的數目超過限制。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|類別資料行的條件機率的數目超過限制。|  
|類別資料行的條件機率的數目超過限制。 資料行{0}'和'{1}' 會造成問題的配對。|  


## <a name="error-0129"></a>錯誤 0129  
 在資料集中的資料行數目超過允許的限制。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|在資料集中的資料行數目超過允許的限制。|  
|在資料集中的資料行數目 '{0}'超過允許'。|  
|數字的資料行中的資料集 '{0}'超過允許的限制'{1}'。 '|  
|數字的資料行中的資料集 '{0}'超過允許'{1}'的限制'{2}'。 '|  
## <a name="error-0130"></a>錯誤 0130  
 定型資料集中的所有資料列包含遺漏值時，就會發生例外狀況。  
  
 會發生這種情況是當定型資料集中的某個資料行是空的。  
  
**解決方案：** 使用[清除遺漏資料](clean-missing-data.md)模組移除所有的遺漏值的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|訓練資料集中的所有資料列包含遺漏值。  請考慮使用 清除遺漏的資料模組移除遺漏的值。|  
 

## <a name="error-0131"></a>錯誤 0131  
 如果在 zip 檔案的一或多個資料集無法解壓縮並已正確註冊，就會發生例外狀況  
  
 Zip 檔案中的一或多個資料集無法解壓縮並正確讀取時，會產生這個錯誤。 如果解除封裝失敗，因為 zip 檔案本身或其中一個檔案已損毀，或發生系統錯誤時嘗試解除封裝，並擴充的檔案時，您會收到這個錯誤。  
  
**解決方案：** 若要判斷如何繼續使用錯誤訊息中提供的詳細資料。  
  
|例外狀況訊息|  
|------------------------|  
|上傳壓縮的資料集失敗|  
|壓縮的資料集{0}失敗並出現下列訊息： {1}|  
|壓縮的資料集{0}失敗，發生{1}例外狀況訊息： {2}|  
  

## <a name="error-0132"></a>錯誤 0132  
 要用來解壓縮; 已不指定任何檔案名稱找不到 zip 檔案中的多個檔案。  
  
 當沒有任何檔案名稱指定要用來解壓縮; 時，會產生這個錯誤找不到 zip 檔案中的多個檔案。 您會收到這個錯誤，如果此.zip 檔包含多個壓縮的檔案，但您未指定的檔案中擷取**要解除封裝資料集**文字方塊中，在**屬性**窗格中的模組。 目前，只有一個檔案也可以擷取每次執行模組時。  
  
**解決方案：** 錯誤訊息會提供.zip 檔案中找到的檔案清單。 複製所需的檔案名稱，並將它貼至**要解除封裝資料集**文字方塊。  
  
|例外狀況訊息|  
|------------------------|  
|Zip 檔案包含多個檔案;您必須指定要展開的檔案。|  
|此檔案包含多個檔案。 指定要展開的檔案。 找不到下列檔案： {0}|  
  

## <a name="error-0133"></a>錯誤 0133  
 Zip 檔案中找不到指定的檔案  
  
 在中，輸入檔案名稱時，會產生這個錯誤**要解除封裝資料集**欄位**屬性**窗格.zip 檔案中找到的任何檔案名稱不相符。 此錯誤的最常見的原因是輸入錯誤或搜尋該檔案以展開錯誤保存檔案。  
  
**解決方案：** 再次瀏覽該模組。 如果您要解壓縮檔案的名稱出現在清單中找到的檔案，複製的檔案名稱，並將它貼至**要解除封裝資料集**屬性方塊中。 如果看不到所需的檔案名稱清單中，確認您擁有正確的.zip 檔案和所需的檔案的正確名稱。  
  
|例外狀況訊息|  
|------------------------|  
|指定的檔案無法找到 int zip 檔案。|  
|找不到指定的檔案。 找到下列檔案： {0}|  
  

## <a name="error-0134"></a>錯誤 0134
標籤資料行遺漏或已加上標籤的資料列數目不足時，就會發生例外狀況。  
  
模組需要標籤資料行，但您未包含一個資料行選取範圍，或標籤資料行遺漏太多值時，就會發生此錯誤。

先前的作業，因此沒有足夠的資料列會提供給下游的作業，請變更資料集時，也會發生此錯誤。 例如，假設您使用中的運算式**資料分割和取樣**模組，將資料集的值。 如果沒有相符項目找到您的運算式，其中一個資料集所產生的資料分割會是空白。

解決方案： 

 如果您在資料行選取包含標籤資料行，但它無法辨識，使用[編輯中繼資料](edit-metadata.md)模組將它標示為標籤資料行。
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->然後，您可以使用[清除遺漏資料](clean-missing-data.md)移除含有遺漏值的資料列標籤 資料行中的模組。 

 請檢查您輸入的資料集，以確定它們包含有效的資料，以及足夠的資料列，以滿足需求的作業。 如果它們需要某些最小的數字資料列的資料，但資料包含只有少數資料列或標頭，許多演算法會產生一則錯誤訊息。
  
|例外狀況訊息|
|------------------------|
|標籤資料行遺漏或已加上標籤的資料列數目不足時，就會發生例外狀況。|  
|例外狀況發生於標籤資料行遺漏或具有小於{0}標示資料列|  
  

## <a name="error-0135"></a>錯誤 0135  
 支援只距心為基礎的叢集。  
  
**解決方案：** 如果您已嘗試評估不會使用距心初始化叢集自訂叢集演算法為基礎的叢集模型，您可能會遇到這個錯誤訊息。  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|例外狀況訊息|  
|------------------------|  
|支援只距心為基礎的叢集。|  
  

## <a name="error-0136"></a>錯誤 0136  
 不傳回任何檔案名稱;無法處理的檔案，如此一來。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|不傳回任何檔案名稱;無法處理的檔案，如此一來。|  
  

## <a name="error-0137"></a>錯誤 0137  
 Azure 儲存體 SDK 發生資料表屬性和資料集資料行之間進行轉換期間讀取或寫入錯誤。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|Azure 資料表儲存體屬性和資料集資料行之間的轉換錯誤。|  
|Azure 資料表儲存體屬性和資料集資料行之間的轉換錯誤。 其他資訊： {0}|  

## <a name="error-0138"></a>錯誤 0138  
 記憶體已用盡，無法完成執行的模組。 縮小取樣資料集可能有助於減輕問題。  
  
 執行此模組需要更多的記憶體，大於可用在 Azure 容器中時，就會發生此錯誤。 如果您正在使用大型資料集，以及目前的作業無法納入記憶體，會發生這項目。  
  
**解決方案：** 如果您嘗試讀取大型資料集，而且無法完成作業，降低取樣資料集可能會幫助。  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|例外狀況訊息|  
|------------------------|  
|記憶體已用盡，無法完成執行的模組。|  
  

## <a name="error-0139"></a>錯誤 0139  
 不可能的資料行轉換成另一種類型時，就會發生例外狀況。  
  
 當您嘗試將資料行轉換為不同的資料類型，但類型不支援目前的作業，或模組時，就會發生此錯誤，Azure Machine Learning 中。  
  
 模組會嘗試以隱含方式轉換資料以符合需求的目前的模組，但不可能轉換時，也可能會出現錯誤。  
  
**解決方案：**

1. 檢閱您的輸入的資料並決定您要使用的資料行的確切資料型別並產生錯誤的資料行的資料類型。 有時候您可能會認為資料型別是正確的但發現上游的作業已修改的資料類型或資料行的使用方式。 使用[編輯中繼資料](edit-metadata.md)模組來重設為其原始狀態的資料行中繼資料。 
2. 查看模組的說明頁面，以確認指定的作業的需求。 判斷目前的模組支援的資料類型，以及支援哪些範圍的值。 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. 請考慮是否可以轉換或轉型成不同的資料類型資料行。 下列的所有模組都提供修改資料的相當大的彈性和功能： 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [執行 Python 指令碼](execute-python-script.md)。  

> [!NOTE]
> 仍然無法運作？ 請考慮提供其他意見反應的問題，以協助我們開發更好的疑難排解指引。 只要提交此頁面上的意見反應，並提供產生的錯誤和失敗的資料類型轉換的模組名稱。
  
|例外狀況訊息|  
|------------------------|  
|不允許轉換。|  
|無法轉換： {0}。|  
|無法轉換： {0}，在資料列上{1}。|  
|無法轉換類型的資料行{0}類型的資料行{1}資料列上{2}。|  
|無法轉換資料行"{2}"的型別{0}類型的資料行{1}資料列上{3}。|  
|無法轉換資料行"{2}"的型別{0}資料行 」{3}"型別的{1}資料列上{4}。| 

## <a name="error-0140"></a>錯誤 0140  
 如果，發生例外狀況傳遞資料行集引數不包含標籤資料行以外的其他資料行。  
  
 如果您連線到需要多個資料行，包括功能、 模組的資料集，就會發生此錯誤，但您已提供標籤資料行。  
  
**解決方案：** 選擇要包含在資料集中至少一個特徵資料行。  
  
|例外狀況訊息|  
|------------------------|  
|指定的資料行集不包含標籤資料行以外的其他資料行。|  
  

## <a name="error-0141"></a>錯誤 0141  
 如果選取的數字資料行中分類的唯一值和字串資料行數目太小，就會發生例外狀況。  
  
 若要執行的作業選取的資料行沒有足夠的唯一值時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 某些作業執行統計運算功能和類別的資料行上，如果沒有足夠的值，此作業可能失敗或傳回無效的結果。 檢查您的資料集，請參閱中的特徵和標籤資料行的值數目，並判斷您嘗試執行此作業是統計上有效。  
  
 如果來源資料集是有效的您也可以檢查一些上游資料操作或中繼資料作業是否已變更資料，並移除一些值。  
  
 如果上游的作業包括分割、 取樣，或重新取樣，請確認輸出包含預期的資料列和值的數目。  
  
|例外狀況訊息|  
|------------------------|  
|選取的數字資料行中分類的唯一值和字串資料行的數目是太小。|  
|選取的數字資料行中分類的唯一值和字串資料行總數 (目前{0}) 至少應該是 {1}|  
  

## <a name="error-0142"></a>錯誤 0142  
 當系統無法載入憑證來驗證時，就會發生例外狀況。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|無法載入憑證。|  
|憑證{0}無法載入。 其憑證指紋是{1}。|  
  

## <a name="error-0143"></a>錯誤 0143  
 無法剖析提供使用者應該是從 GitHub 的 URL。  
  
 當您指定無效的 URL 和模組需要有效的 GitHub URL 時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 請確認 URL 參考到有效的 GitHub 存放庫。 不支援其他站台類型。  
  
|例外狀況訊息|  
|------------------------|  
|URL 不是從 github.com 中。|  
|URL 不是來自 github.com 中： {0}|  

## <a name="error-0144"></a>錯誤 0144  
 使用者提供的 GitHub url 遺漏預期的部分。  
  
 當您指定 GitHub 檔案來源使用無效的 URL 格式時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 請檢查 GitHub 儲存機制 URL 有效，且結尾 \blob\ 或 \tree\\。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析的 GitHub URL。|  
|無法剖析的 GitHub URL (必須是 '\blob\\' 或 '\tree\\' 存放庫名稱之後): {0}|  

## <a name="error-0145"></a>錯誤 0145  
 基於某些原因，無法建立複寫目錄。  
  
 模組無法建立指定的目錄時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|無法建立複寫目錄。|  
  

## <a name="error-0146"></a>錯誤 0146  
 使用者檔案解壓縮到本機目錄時，合併的路徑可能太長。  
  
 當您在解壓縮檔案，但某些檔案名稱都太長，解壓縮時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 編輯的檔案名稱，結合路徑和檔案名稱長度不超過 248 個字元。  
  
|例外狀況訊息|  
|------------------------|  
|複寫路徑超過 248 個字元，請縮短指令碼名稱或路徑。|  

## <a name="error-0147"></a>錯誤 0147  
 無法下載因故從 GitHub 的東西  
  
 您無法讀取，或從 GitHub 下載指定的檔案時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：** 此問題可能是暫時的;您可以嘗試在另一個階段中存取的檔案。 或驗證您有必要的權限，以及來源有效。  
  
|例外狀況訊息|  
|------------------------|  
|GitHub 的存取時發生錯誤。|  
|GitHub 的存取時發生錯誤。 [https://doi.org/10.13012/J8PN93H8]({0})|  
  

## <a name="error-0148"></a>錯誤 0148  
 擷取資料，或建立目錄時的未經授權的存取問題。  
  
 當您嘗試建立目錄，或從儲存體讀取資料，但並沒有必要的權限時，就會發生此錯誤，Azure Machine Learning 中。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|擷取資料時的未經授權的存取例外狀況。|  
  

## <a name="error-0149"></a>錯誤 0149  
 使用者檔案不存在內 GitHub 套件組合。  
  
 找不到指定的檔案時，就會發生此錯誤，Azure Machine Learning 中。  
  
解決方案： 
  
|例外狀況訊息|  
|------------------------|  
|找不到 GitHub 的檔案。|  
|找不到 GitHub 的檔案。: {0}|  
  

## <a name="error-0150"></a>錯誤 0150  
 來自使用者封裝的指令碼無法解壓縮，很可能是因為與 GitHub 的檔案發生衝突。  
  
 指令碼無法被擷取時，通常具有相同名稱的現有檔案時，就會發生此錯誤，Azure Machine Learning 中。  
  
解決方案：
  
|例外狀況訊息|  
|------------------------|  
|無法解壓縮套件組合中;使用 GitHub 檔案的可能名稱衝突。|  
  

## <a name="error-0151"></a>錯誤 0151  
 寫入至雲端儲存體時發生錯誤。 檢查 URL。  
  
 此模組會嘗試將資料寫入至雲端儲存體，但 URL 為無法使用或無效時，就會發生此錯誤，Azure Machine Learning 中。  
  
解決方案：請檢查 URL，並確認它可寫入。  
  
|例外狀況訊息|  
|------------------------|  
|寫入至雲端儲存體 (可能不正確的 url) 時發生錯誤。|  
|寫入至雲端儲存體時發生錯誤： {0}。 檢查 url。|  
  
## <a name="error-0152"></a>錯誤 0152  
 Azure 雲端類型的模組內容中指定不正確。  
  
|例外狀況訊息|  
|------------------------|  
|不正確的 Azure 雲端類型|  
|不正確的 Azure 雲端類型： {0}|  
  
## <a name="error-0153"></a>錯誤 0153  
 指定的儲存體結束點無效。  
  
|例外狀況訊息|  
|------------------------|  
|不正確的 Azure 雲端類型|  
|不正確的儲存體端點： {0}|  

## <a name="error-0154"></a>錯誤 0154  
 無法解析指定的伺服器名稱  
  
|例外狀況訊息|  
|------------------------|  
|無法解析指定的伺服器名稱|  
|指定的伺服器{0}。 無法解析 documents.azure.com|

## <a name="error-0155"></a>錯誤 0155  
 DocDb 用戶端擲回例外狀況  
  
|例外狀況訊息|  
|------------------------|  
|DocDb 用戶端擲回例外狀況|  
|DocDb 用戶端： {0}|

## <a name="error-0156"></a>錯誤 0156  
 HCatalog 伺服器回應不正確。  
  
|例外狀況訊息|  
|------------------------|  
|HCatalog 伺服器回應不正確。 請確認所有服務正在都執行。|  
|HCatalog 伺服器回應不正確。 請確認所有服務正在都執行。 錯誤詳細資料：{0}|

## <a name="error-0157"></a>錯誤 0157  
 讀取 Azure Cosmos DB，因為不一致或不同的文件結構描述時發生錯誤。 讀取器需要有相同的結構描述的所有文件。  
  
|例外狀況訊息|  
|------------------------|  
|偵測到對不同結構描述的文件。 請確定所有文件有相同的結構描述|

## <a name="error-1000"></a>錯誤 1000  
內部程式庫例外狀況。  
  
會提供此錯誤，否則擷取未處理的內部引擎錯誤。 因此，此錯誤的原因可能會產生錯誤的模組而有所不同。  
  
若要取得更多協助，我們建議您張貼到 Azure Machine Learning 論壇，以及描述的案例，包括做為輸入的資料伴隨著錯誤的詳細的訊息。 此意見反應將可協助我們排定優先順序的錯誤，並找出最重要的問題，以便進一步作業。  
  
|例外狀況訊息|  
|------------------------|  
|程式庫例外狀況。|  
|程式庫例外狀況： {0}|  
|{0} 程式庫例外狀況： {1}|  
