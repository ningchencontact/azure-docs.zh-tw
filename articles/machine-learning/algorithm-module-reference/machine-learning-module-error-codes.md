---
title: 針對模組錯誤進行疑難排解
titleSuffix: Azure Machine Learning service
description: 使用錯誤碼針對 Azure Machine Learning Studio 中的模組例外狀況進行疑難排解
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a1a6817c08223b360c08804e0595f12f2947ea5f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693082"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>演算法的例外狀況和錯誤碼 & 模組參考

瞭解在 Azure Machine Learning Studio 中使用模組可能會遇到的錯誤訊息和例外狀況代碼。 

若要解決此問題，請尋找本文中的錯誤，以閱讀常見的原因。 有兩種方式可取得 Studio 中錯誤訊息的完整文字：  
 
- 按一下右窗格中的 [**查看輸出記錄**檔] 連結，並向下流覽至底部。 詳細的錯誤訊息會顯示在視窗的最後兩行中。  
  
- 選取發生錯誤的模組，然後按一下紅色 X。只會顯示相關的錯誤文字。  
  
如果錯誤訊息正文沒有説明，請將內容的相關資訊以及任何想要的新增或變更傳送給我們。 您可以提交有關錯誤主題的意見反應，或造訪[AZURE MACHINE LEARNING STUDIO 論壇](https://aka.ms/aml-forum-studio)並張貼問題。  


## <a name="error-0001"></a>錯誤0001  
 如果找不到一或多個資料集的指定資料行，就會發生例外狀況。  
  
 如果對模組進行資料行選擇，但選取的資料行不存在於輸入資料集中，您就會收到這個錯誤。 如果您已在資料行名稱中手動輸入，或是當您執行管線時，資料行選取器提供了不存在於 dataset 中的建議資料行，就會發生這個錯誤。  
  
**解決方式：** 請重新流覽擲回此例外狀況的模組，並驗證資料行名稱是否正確，以及所有參考的資料行是否都存在。  
  
|例外狀況訊息|  
|------------------------|  
|找不到一或多個指定的資料行|  
|找不到名稱或索引為 "{0}" 的資料行|  
|名稱或索引為 "{0}" 的資料行不存在於 "{1}" 中|  
 

## <a name="error-0002"></a>錯誤0002  
 如果一或多個參數無法剖析或從指定的類型轉換成目標方法類型所需，就會發生例外狀況。  
  
 當您指定參數做為輸入，且實數值型別與預期的類型不同，而且無法執行隱含轉換時，Azure Machine Learning 會發生此錯誤。  
  
**解決方式：** 檢查模組需求，並判斷需要哪一個實數值型別（字串、整數、雙精確度等）  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析參數|  
|無法剖析 "{0}" 參數|  
|無法將 "{0}" 參數剖析（轉換）為 "{1}"|  
|無法將 "{0}" 參數從 "{1}" 轉換為 "{2}"|  
|無法將 "{0}" 參數值 "{1}" 從 "{2}" 轉換為 "{3}"|  
|無法將資料行 "{1}" 中的值 "{0}" 從 "{2}" 轉換為 "{3}"，並使用格式 "{4}"|  
  

## <a name="error-0003"></a>錯誤0003  
 如果一或多個輸入是 null 或空白，就會發生例外狀況。  
  
 如果模組的任何輸入或參數為 null 或空白，您將會在 Azure Machine Learning 中收到此錯誤。  例如，當您未輸入任何參數值時，就可能會發生此錯誤。 如果您選擇具有遺漏值的資料集或空的資料集，也可能會發生此問題。  
  
**解決方案：**
 
+ 開啟產生例外狀況的模組，並確認已指定所有輸入。 請確定已指定所有必要的輸入。 
+ 請確定從 Azure 儲存體載入的資料可供存取，且帳戶名稱或金鑰尚未變更。  
+ 請檢查輸入資料中是否有遺漏值，或為 null。
+ 如果在資料來源上使用查詢，請確認資料是以您預期的格式傳回。 
+ 檢查資料規格中的錯誤或其他變更。
  
|例外狀況訊息|  
|------------------------|  
|一或多個輸入為 null 或空白|  
|輸入 "{0}" 為 null 或空白|  
  

## <a name="error-0004"></a>錯誤 .0004  
 如果參數小於或等於特定的值，就會發生例外狀況。  
  
 如果訊息中的參數低於模組用來處理資料所需的界限值，您將會在 Azure Machine Learning 中收到此錯誤。  
  
**解決方式：** 重新流覽擲回例外狀況的模組，並將參數修改為大於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數應大於界限值。|  
|參數 "{0}" 值應大於 {1}。|  
|參數 "{0}" 有值 "{1}"，應大於 {2}|  
  


## <a name="error-0005"></a>錯誤0005  
 如果參數小於特定值，就會發生例外狀況。  
  
 如果訊息中的參數低於或等於模組用來處理資料所需的界限值，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 重新流覽擲回例外狀況的模組，並將參數修改為大於或等於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數應大於或等於界限值。|  
|參數 "{0}" 值應該大於或等於 {1}。|  
|參數 "{0}" 有值 "{1}"，應大於或等於 {2}。|  
  

## <a name="error-0006"></a>錯誤和  
 如果參數大於或等於指定的值，就會發生例外狀況。  
  
 如果訊息中的參數大於或等於模組處理資料所需的界限值，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 重新流覽擲回例外狀況的模組，並將參數修改為小於指定的值。  
  
|例外狀況訊息|  
|------------------------|  
|參數不相符。 其中一個參數應小於另一個。|  
|參數 "{0}" 值應小於參數 "{1}" 值。|  
|參數 "{0}" 有值 "{1}"，應小於 {2}。|  
  

## <a name="error-0007"></a>錯誤0007  
 如果參數大於特定的值，就會發生例外狀況。  
  
 如果您在模組的屬性中指定了大於的值，就會在 Azure Machine Learning 中收到這個錯誤。 例如，您可以指定超出支援日期範圍的資料，或者您可能會指出只有三個數據行可用時，才會使用五個數據行。 
 
 如果您要指定需要以某種方式比對的兩組資料，也可能會看到此錯誤。 例如，如果您要重新命名資料行，並依索引指定資料行，則您提供的名稱數目必須符合資料行索引的數目。 另一個範例可能是使用兩個數據行的數學運算，其中資料行必須具有相同數目的資料列。 
  
**解決方案：**
 
 + 開啟有問題的模組，並查看任何數值屬性設定。
 + 請確定任何參數值都落在該屬性的支援值範圍內。
 + 如果模組接受多個輸入，請確定輸入的大小相同。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 檢查資料集或資料來源是否已變更。 有時候，使用舊版資料的值將會在資料行數目、資料行資料類型或資料大小變更之後失敗。  
  
|例外狀況訊息|  
|------------------------|  
|參數不相符。 其中一個參數應小於或等於另一個。|  
|參數 "{0}" 值應該小於或等於參數 "{1}" 值。|  
|參數 "{0}" 有值 "{1}"，應小於或等於 {2}。|  
  

## <a name="error-0008"></a>錯誤0008  
 如果參數不在範圍內，就會發生例外狀況。  
  
 如果訊息中的參數超出模組處理資料所需的界限，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
 例如，如果您嘗試使用 [加入資料列] 來合併兩個具有不同資料[行](add-rows.md)數目的資料集，就會顯示此錯誤。  
  
**解決方式：** 重新流覽擲回例外狀況的模組，並將參數修改為在指定的範圍內。  
  
|例外狀況訊息|  
|------------------------|  
|參數值不在指定的範圍內。|  
|參數 "{0}" 值不在範圍內。|  
|參數 "{0}" 值應在 [{1}，{2}] 的範圍內。|  
  

## <a name="error-0009"></a>錯誤0009  
 未正確指定 Azure 儲存體帳戶名稱或容器名稱時，就會發生例外狀況。  
  
當您指定 Azure 儲存體帳戶的參數，但無法解析名稱或密碼時，Azure Machine Learning Studio 會發生此錯誤。 密碼或帳戶名稱的錯誤可能會因為許多原因而發生：
 
 + 帳戶的類型錯誤。 某些新的帳戶類型不支援與 Machine Learning Studio 搭配使用。 如需詳細資訊，請參閱匯[入資料](import-data.md)。
 + 您輸入的帳戶名稱不正確
 + 帳戶已不存在
 + 儲存體帳戶的密碼錯誤或已變更
 + 您未指定容器名稱，或容器不存在
 + 您未完全指定檔案路徑（blob 的路徑）
   
**解決方案：**

當您嘗試手動輸入帳戶名稱、密碼或容器路徑時，通常會發生這類問題。 我們建議您針對匯[入資料](import-data.md)模組使用新的 wizard，這可協助您查閱和檢查名稱。

同時檢查帳戶、容器或 blob 是否已刪除。 使用另一個 Azure 儲存體公用程式，確認已正確輸入帳戶名稱和密碼，且容器存在。 

Azure Machine Learning 不支援某些較新的帳戶類型。 例如，新的「經常性」或「冷」儲存類型無法用於機器學習。 傳統儲存體帳戶和建立為「一般用途」的儲存體帳戶都能正常執行。

如果已指定 blob 的完整路徑，請確認路徑已指定為**container/blobname**，而且容器和 blob 都存在於帳戶中。  
  
 路徑不應該包含前置斜線。 例如， **/container/blob**不正確，且應輸入為**容器/blob**。  

  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶名稱或容器名稱不正確。|  
|Azure 儲存體帳戶名稱 "{0}" 或容器名稱 "{1}" 不正確;需要容器/blob 格式的容器名稱。|  
  

## <a name="error-0010"></a>錯誤 mylnxstorage02  
 如果輸入資料集有應該符合但卻不相符的資料行名稱，就會發生例外狀況。  
  
 如果訊息中的資料行索引在兩個輸入資料集內有不同的資料行名稱，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 使用 [[編輯中繼資料](edit-metadata.md)] 或修改原始資料集，讓指定的資料行索引具有相同的資料行名稱。  
  
|例外狀況訊息|  
|------------------------|  
|在輸入資料集中具有對應索引的資料行具有不同的名稱。|  
|對於輸入資料集（以零為基底）的資料行 {0} （{1} 和 {2}），資料行名稱並不相同。|  
  

## <a name="error-0011"></a>錯誤0011  
 如果傳遞的資料行集引數不適用於任何資料集資料行，就會發生例外狀況。  
  
 如果指定的資料行選取專案不符合給定 dataset 中的任何資料行，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
 如果您尚未選取資料行，而且必須至少有一個資料行供模組使用，您也可以取得此錯誤。  
  
**解決方式：** 修改模組中的資料行選取範圍，以便將它套用至資料集內的資料行。  
  
 如果模組要求您選取特定資料行（例如標籤資料行），請確認已選取正確的資料行。  
  
 如果選取了不適當的資料行，請將其移除，然後重新執行管線。  
  
|例外狀況訊息|  
|------------------------|  
|指定的資料行集不會套用到任何資料集資料行。|  
|指定的資料行集 "{0}" 不會套用至任何資料集資料行。|  
  

## <a name="error-0012"></a>錯誤0012  
 如果無法使用傳遞的引數集來建立類別的實例，就會發生例外狀況。  
  
**解決方式：** 此錯誤不是由使用者採取動作，將在未來的版本中被取代。  
  
|例外狀況訊息|  
|------------------------|  
|未定型的模型，先訓練模型。|  
|未定型的模型（{0}），請使用訓練的模型。|  
  

## <a name="error-0013"></a>錯誤0013  
 如果傳遞至模組的學習模組是不正確類型，就會發生例外狀況。  
  
 當定型的模型與連接的計分模組不相容時，就會發生此錯誤。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->
  
**解決方案：**

判斷定型模組所產生的學習模組類型，並決定適合學習模組的評分模組。 

如果模型是使用任何特製化定型模組來定型，請將定型的模型只連接至對應的特製化計分模組。 


|模型類型|訓練課程模組| 計分模組|
|----|----|----|
|任何分類器|[定型模型](train-model.md) |[評分模型](score-model.md)|
|任何回歸模型|[定型模型](train-model.md) |[評分模型](score-model.md)|

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
|傳遞了無效類型的學習模組。|  
|學習模組 "{0}" 的類型無效。|  


## <a name="error-0014"></a>錯誤0014  
 如果資料行的唯一值計數大於允許的數目，就會發生例外狀況。  
  
 當資料行包含太多唯一值時，就會發生此錯誤。  例如，如果您指定要將資料行當做類別資料來處理，但資料行中有太多唯一值，以允許處理完成，您可能會看到這個錯誤。 如果兩個輸入中的唯一值數目不符，您也可能會看到此錯誤。   
  
**解決方案：**

開啟產生錯誤的模組，並識別用來做為輸入的資料行。 針對某些模組，您可以用滑鼠右鍵按一下資料集輸入，然後選取 [**視覺化**] 以取得個別資料行的統計資料，包括唯一值的數目及其散發。

針對您想要用於群組或分類的資料行，採取步驟來減少資料行中的唯一值數目。 視資料行的資料類型而定，您可以透過不同的方式來減少。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到符合您案例的解決方法嗎？ 您可以提供此主題的意見反應，其中包括產生錯誤的模組名稱，以及資料行的資料類型和基數。 我們將使用此資訊，為常見案例提供更具目標的疑難排解步驟。   
  
|例外狀況訊息|  
|------------------------|  
|資料行的唯一值數目大於 [允許]。|  
|資料行中的唯一值數目： "{0}" 超過 {1} 的元組計數。|  
  

## <a name="error-0015"></a>錯誤0015  
 如果資料庫連接失敗，就會發生例外狀況。  
  
 如果您輸入不正確的 SQL 帳戶名稱、密碼、資料庫伺服器或資料庫名稱，或因為資料庫或伺服器發生問題而無法建立與資料庫的連接，就會收到這個錯誤。  
  
**解決方式：** 確認已正確輸入帳戶名稱、密碼、資料庫伺服器和資料庫，而且指定的帳號具有正確的許可權層級。 確認資料庫目前可供存取。  
  
|例外狀況訊息|  
|------------------------|  
|建立資料庫連接時發生錯誤。|  
|建立資料庫連接時發生錯誤： {0}。|  
  


## <a name="error-0016"></a>錯誤0016  
 如果傳遞至模組的輸入資料集應該有相容的資料行類型，但卻不是，就會發生例外狀況。  
  
 如果兩個或多個資料集內傳遞的資料行類型彼此不相容，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 使用 [[編輯中繼資料](edit-metadata.md)] 或修改原始輸入資料集<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 以確保資料行的類型相容。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集中具有對應索引的資料行具有不相容的類型。|  
|@No__t_0 和 {1} 的資料行不相容。|  
|輸入資料集（{1} 和 {2}）的資料行 {0} （以零為基底）的資料行元素類型不相容。|  
  

## <a name="error-0017"></a>錯誤0017  
 如果選取的資料行使用目前模組不支援的資料類型，就會發生例外狀況。  
  
 例如，如果您的資料行選取範圍包含的資料行具有無法由模組處理的資料類型（例如數學運算的字串資料行），或是類別特徵資料行所在的分數資料行，則您可能會在 Azure Machine Learning 中收到此錯誤：必填。  
  
**解決方案：**
 1. 找出問題所在的資料行。
 2. 檢查模組的需求。
 3. 修改資料行，使其符合需求。 視您嘗試的資料行和轉換而定，您可能需要使用下列幾個模組來進行變更：
    + 使用 [[編輯中繼資料](edit-metadata.md)] 來變更資料行的資料類型，或將 [資料行使用方式] 從 [功能] 變更為 [數值]、[類別] 或 [非類別] 等等。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 作為最後的手段，您可能需要修改原始的輸入資料集。

> [!TIP]
> 找不到符合您案例的解決方法嗎？ 您可以提供此主題的意見反應，其中包括產生錯誤的模組名稱，以及資料行的資料類型和基數。 我們將使用此資訊，為常見案例提供更具目標的疑難排解步驟。 
  
|例外狀況訊息|  
|------------------------|  
|無法處理目前類型的資料行。 模組不支援此類型。|  
|無法處理 {0} 類型的資料行。 模組不支援此類型。|  
|無法處理類型 {0} 的資料行 "{1}"。 模組不支援此類型。|  
|無法處理類型 {0} 的資料行 "{1}"。 模組不支援此類型。 參數名稱： {2}|  
  

## <a name="error-0018"></a>錯誤0018  
 如果輸入資料集無效，就會發生例外狀況。  
  
**解決方式：** Azure Machine Learning 中的這個錯誤可能會出現在許多內容中，因此不會有單一解析。 一般而言，此錯誤表示提供給模組輸入的資料具有錯誤的資料行數目，或資料類型不符合模組的需求。 例如：  
  
-   模組需要標籤資料行，但未將資料行標示為標籤，或您尚未選取標籤資料行。  
  
-   模組要求資料必須是類別，但是您的資料是數值。  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   資料的格式錯誤。  
  
-   匯入的資料包含不正確字元、錯誤的值或超出範圍的值。  
-   資料行是空的或包含太多遺漏值。  
  
 若要判斷需求和資料的可能，請參閱將使用資料集作為輸入之模組的說明主題。  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->。  
  
|例外狀況訊息|  
|------------------------|  
|資料集無效。|  
|{0} 包含不正確資料。|  
|{0} 和 {1} 應該是一致的資料行。|  
  

## <a name="error-0019"></a>錯誤0019  
 如果資料行應該包含排序的值，就會發生例外狀況，但卻不會。  
  
 如果指定的資料行值不是順序，您就會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 手動修改輸入資料集並重新執行模組，以排序資料行值。  
  
|例外狀況訊息|  
|------------------------|  
|資料行中的值不會排序。|  
|資料行 "{0}" 中的值未排序。|  
|資料集 "{1}" 的資料行 "{0}" 中的值未排序。|  
  

## <a name="error-0020"></a>錯誤0020  
 如果傳遞給模組的某些資料集的資料行數目太小，就會發生例外狀況。  
  
 如果沒有為模組選取足夠的資料行，您將會在 Azure Machine Learning 中收到這個錯誤。  
  
**解決方式：** 請重新流覽模組，並確定資料行選取器已選取正確的資料行數目。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集的資料行數目小於允許的最小值。|  
|輸入資料集的資料行數目小於允許的最小值 {0} 資料行。|  
|輸入資料集 "{0}" 中的資料行數目小於允許的最小值 {1} 資料行。|

## <a name="error-0021"></a>錯誤0021  
 如果傳遞給模組的某些資料集內的資料列數目太小，就會發生例外狀況。  
  
 當 dataset 中沒有足夠的資料列可執行指定的作業時，Azure Machine Learning 看到此錯誤。 例如，如果輸入資料集是空的，或者您嘗試執行的作業需要有效的最少資料列數，您可能會看到這個錯誤。 這類作業可以包含（但不限於）以統計方法、特定類型的分類收納和使用計數學習為基礎的群組或分類。  
  
**解決方案：**
 
 + 開啟傳回錯誤的模組，並檢查輸入資料集和模組屬性。 
 + 請確認輸入資料集不是空的，而且有足夠的資料列可符合模組說明中所述的需求。  
 + 如果您的資料是從外部來源載入，請確定資料來源可以使用，而且資料定義中沒有任何錯誤或變更，而導致匯入處理常式取得較少的資料列。
 + 如果您要對模組的資料執行作業，而此作業可能會影響資料的類型或值的數目，例如清除、分割或聯結作業，請檢查這些作業的輸出，以判斷傳回的資料列數目。  



## <a name="error-0022"></a>錯誤0022  
 如果輸入資料集中選取的資料行數目不等於預期的數目，就會發生例外狀況。  
  
 當下游模組或作業需要特定數目的資料行或輸入，而且您提供了太少或太多的資料行或輸入時，就會發生 Azure Machine Learning 中的這個錯誤。 例如：  
  
-   您指定單一標籤資料行或索引鍵資料行，但不小心選取了多個資料行。  
  
-   您正在重新命名資料行，但所提供的名稱比資料行更多或少。  
  
-   來源或目的地中的資料行數目已變更，或不符合模組所使用的資料行數目。  
  
-   您已針對輸入提供以逗號分隔的值清單，但值的數目不相符，或不支援多個輸入。  
  
**解決方式：** 請重新流覽模組，並檢查資料行選取範圍，以確定已選取正確的資料行數目。 確認上游模組的輸出，以及下游作業的需求。  
  
 如果您使用的其中一個資料行選取選項可以選取多個資料行（資料行索引、所有功能、全部數值等等），請驗證選取範圍所傳回的確切資料行數目。  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 確認上游資料行的數目或類型尚未變更。  
  
 如果您使用建議資料集來定型模型，請記住，推薦預期會有有限數目的資料行，對應于使用者專案組或使用者專案排名。 在定型模型或分割建議資料集之前，請先移除其他資料行。 如需詳細資訊，請參閱[分割資料](split-data.md)。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集中選取的資料行數目不等於預期的數目。|  
|輸入資料集中選取的資料行數目不等於 {0}。|  
|資料行選取模式 "{0}" 提供輸入資料集中不等於 {1} 的選取資料行數目。|  
|資料行選取模式 "{0}" 應提供在輸入資料集中選取的 {1} 個數據行，但提供了 {2} 個數據行。|  



## <a name="error-0023"></a>錯誤0023  
 如果輸入資料集的目標資料行對目前講師模組無效，就會發生例外狀況。  
  
 如果目標資料行（如模組參數中所選取）不是有效的資料類型、包含所有遺漏值，或不是預期的類別，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請重新流覽模組輸入，以檢查標籤/目標資料行的內容。 請確定它沒有所有遺漏的值。 如果模組預期目標資料行是類別，請確定目標資料行中有一個以上的相異值。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集具有不支援的目標資料行。|  
|輸入資料集具有不支援的目標資料行 "{0}"。|  
|針對 {1} 類型的學習模組，輸入資料集有不支援的目標資料行 "{0}"。|  
 

## <a name="error-0024"></a>錯誤0024  
如果資料集不包含標籤資料行，就會發生例外狀況。  

 當模組需要標籤資料行，而且該資料集沒有標籤資料行時，就會發生 Azure Machine Learning 中的這個錯誤。 例如，評估評分資料集時，通常需要有標籤資料行才能計算精確度計量。  
 
也可能會發生標籤資料行出現在 dataset 中，但 Azure Machine Learning 無法正確偵測到。
  
**解決方案：**

+ 開啟產生錯誤的模組，並判斷標籤資料行是否存在。 資料行的名稱或資料類型並不重要，只要資料行包含您嘗試預測的單一結果（或相依變數）即可。 如果您不確定哪一個資料行具有標籤，請尋找一般名稱，例如 [*類別*] 或 [*目標*]。 
+  如果資料集不包含標籤資料行，則可能是標籤資料行已明確或不小心移除上游。 這也可能是因為資料集不是上游計分模組的輸出。
+ 若要將資料行明確標示為標籤資料行，請新增 [[編輯中繼資料](edit-metadata.md)] 模組，並連接到資料集。 僅選取 [標籤] 資料行，然後從 [**欄位**] 下拉式清單中選取 [**標籤**]。 
+ 如果選擇錯誤的資料行做為標籤，您可以從**欄位**選取 [**清除標籤**]，以修正資料行上的中繼資料。 
  
|例外狀況訊息|  
|------------------------|  
|Dataset 中沒有標籤資料行。|  
|"@No__t_0" 中沒有標籤資料行。|  
  

## <a name="error-0025"></a>錯誤0025  
 如果資料集未包含分數資料行，就會發生例外狀況。  
  
 如果評估模型的輸入不包含有效的分數資料行，就會發生 Azure Machine Learning 中的這個錯誤。 例如，使用者在使用正確的定型模型進行計分之前，會嘗試評估資料集，或已明確地卸載 [分數] 資料行。 如果兩個資料集上的分數資料行不相容，也會發生這個例外狀況。 例如，您可能嘗試比較線性回歸輸入變數與二元分類器的精確度。  
  
**解決方式：** 重新流覽評估模型的輸入，並檢查它是否包含一或多個分數資料行。 如果不是，則不會評分資料集，或在上游模組中捨棄分數資料行。  
  
|例外狀況訊息|  
|------------------------|  
|Dataset 中沒有分數資料行。|  
|"@No__t_0" 中沒有分數資料行。|  
|「@No__t_1」所產生的「{0}」中沒有分數資料行。 使用正確的學習模組類型來評分資料集。|  
  

## <a name="error-0026"></a>錯誤0026  
 如果不允許具有相同名稱的資料行，就會發生例外狀況。  
  
 如果多個資料行具有相同的名稱，就會發生 Azure Machine Learning 中的這個錯誤。 您可能會收到這個錯誤的其中一種方式，就是如果資料集沒有標題列，而且會自動指派資料行名稱： Col0、Col1 等等。  
  
**解決方式：** 如果資料行具有相同的名稱，請在輸入資料集和模組之間插入[編輯中繼資料](edit-metadata.md)模組。 使用 [[編輯中繼資料](edit-metadata.md)] 中的資料行選取器選取要重新命名的資料行，並在 [**新資料行名稱**] 文字方塊中輸入新的名稱。  
  
|例外狀況訊息|  
|------------------------|  
|在引數中指定了相等的資料行名稱。 模組不允許相等的資料行名稱。|  
|不允許引數「{0}」和「{1}」中的相同資料行名稱。 請指定不同的名稱。|  
  

## <a name="error-0027"></a>錯誤0027  
 當兩個物件的大小必須相同但不是時，就會發生例外狀況。  
  
 這是 Azure Machine Learning 中常見的錯誤，可能是由許多狀況所造成。  
  
**解決方式：** 沒有特定的解決方式。 不過，您可以檢查下列條件：  
  
-   如果您要重新命名資料行，請確定每個清單（輸入資料行和新名稱清單）都有相同數目的專案。  
  
-   如果您要聯結或串連兩個資料集，請確定它們具有相同的架構。  
  
-   如果您要聯結具有多個資料行的兩個資料集，請確定索引鍵資料行具有相同的資料類型，然後選取 [**允許重複專案] 和 [保留選取範圍中的資料行順序**] 選項。  
  
|例外狀況訊息|  
|------------------------|  
|傳遞物件的大小不一致。|  
|"@No__t_0" 的大小與 "{1}" 的大小不一致。|  
  

## <a name="error-0028"></a>錯誤0028  
 當資料行集包含重複的資料行名稱，而且不允許使用時，就會發生例外狀況。  
  
 當資料行名稱重複時，Azure Machine Learning 發生此錯誤;也就是說，這不是唯一的。  
  
**解決方式：** 如果有任何資料行具有相同的名稱，請在輸入資料集與引發錯誤的模組之間加入[編輯中繼資料](edit-metadata.md)的實例。 使用 [[編輯中繼資料](edit-metadata.md)] 中的資料行選取器選取要重新命名的資料行，然後在 [**新資料行名稱**] 文字方塊中輸入新資料行的名稱。 如果您要重新命名多個資料行，請確定您在新的資料**行名稱**中輸入的值是唯一的。  
  
|例外狀況訊息|  
|------------------------|  
|資料行集包含重複的資料行名稱。|  
|名稱 "{0}" 是重複的。|  
|"@No__t_0" 名稱在 "{1}" 中重複。|  
  

## <a name="error-0029"></a>錯誤0029  
 傳遞不正確 URI 時，就會發生例外狀況。  
  
 當傳遞不正確 URI 時，Azure Machine Learning 發生此錯誤。  如果下列任一條件成立，您就會收到此錯誤：或。  
  
-   為讀取或寫入 Azure Blob 儲存體提供的公用或 SAS URI 包含錯誤。  
  
-   SAS 的時間範圍已過期。  
  
-   透過 HTTP 來源的 Web URL 代表檔案或回送 URI。  
  
-   透過 HTTP 的 Web URL 包含格式不正確的 URL。  
  
-   遠端來源無法解析 URL。  
  
**解決方式：** 請重新流覽模組，並確認 URI 的格式。 如果資料來源是透過 HTTP 的 Web URL，請確認預期的來源不是檔案或回送 URI （localhost）。  
  
|例外狀況訊息|  
|------------------------|  
|傳遞了不正確 Uri。|  
  

## <a name="error-0030"></a>錯誤0030  
 無法下載檔案時，就會發生例外狀況。  
  
 當無法下載檔案時，會發生 Azure Machine Learning 中的這個例外狀況。 在三（3）次重試嘗試之後，嘗試從 HTTP 來源讀取失敗時，您會收到此例外狀況。  
  
**解決方式：** 請確認 HTTP 來源的 URI 是否正確，以及目前是否可透過網際網路存取該網站。  
  
|例外狀況訊息|  
|------------------------|  
|無法下載檔案。|  
|下載檔案時發生錯誤： {0}。|  
  

## <a name="error-0031"></a>錯誤0031  
 如果資料行集中的資料行數目少於所需，就會發生例外狀況。  
  
 如果選取的資料行數目少於所需，就會發生 Azure Machine Learning 中的這個錯誤。  如果未選取所需的最小資料行數目，您就會收到這個錯誤。  
  
**解決方式：** 使用資料**行選取器**，將其他資料行加入至資料行選取範圍。  
  
|例外狀況訊息|  
|------------------------|  
|資料行集中的資料行數目小於所需。|  
|應指定 {0} 的資料行。 指定資料行的實際數目是 {1}。|  

## <a name="error-0032"></a>錯誤0032  
 如果引數不是數位，就會發生例外狀況。  
  
 如果引數為 double 或 NaN，您將會在 Azure Machine Learning 中收到此錯誤。  
  
**解決方式：** 修改指定的引數，以使用有效的值。  
  
|例外狀況訊息|  
|------------------------|  
|引數不是數位。|  
|"{0}" 不是數位。|  
  

## <a name="error-0033"></a>錯誤0033  
 如果引數是無限大，就會發生例外狀況。  
  
 如果引數是無限的，就會發生此錯誤 Azure Machine Learning。 如果引數是 `double.NegativeInfinity` 或 `double.PositiveInfinity`，您就會收到這個錯誤。  
  
**解決方式：** 將指定的引數修改為有效的值。  
  
|例外狀況訊息|  
|------------------------|  
|引數必須是有限的。|  
|"{0}" 不是有限的。|  
  

## <a name="error-0034"></a>錯誤0034  
 如果指定的使用者-專案組有一個以上的評等，就會發生例外狀況。  
  
 如果使用者專案組有一個以上的評等值，則 Azure Machine Learning 中的這個錯誤會發生在建議中。  
  
**解決方式：** 請確定使用者-專案組只擁有一個評等值。  
  
|例外狀況訊息|  
|------------------------|  
|資料集內的值有一個以上的評等。|  
|評等預測資料表中的使用者 {0} 和專案 {1} 有一個以上的評等。|  
  

## <a name="error-0035"></a>錯誤0035  
 如果沒有為指定的使用者或專案提供任何功能，就會發生例外狀況。  
  
 當您嘗試使用建議模型進行評分，但找不到功能向量時，Azure Machine Learning 發生此錯誤。  
  
**解決方案：**

Matchbox 推薦具有某些必須在使用專案功能或使用者功能時符合的需求。  此錯誤表示您提供做為輸入的使用者或專案缺少功能向量。  您必須確定每個使用者或專案的資料中都有可用的功能向量。  
  
 例如，如果您使用像是使用者年齡、地點或收入等功能來定型建議模型，但現在想要為訓練期間未看到的新使用者建立分數，您必須提供一組對等的功能（亦即，年齡、位置，以及收入值），以針對新使用者進行適當的預測。 
 
 如果您沒有這些使用者的任何功能，請考慮使用特色工程來產生適當的功能。  例如，如果您沒有個別的使用者年齡或收入值，您可能會產生近似的值供使用者群組使用。 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > 解決方案不適用您的案例嗎？ 歡迎您傳送本文的意見反應，並提供案例的相關資訊，包括模組和資料行中的資料列數目。 我們會使用這項資訊來提供未來更詳細的疑難排解步驟。
   
|例外狀況訊息|  
|------------------------|  
|未提供必要使用者或專案的任何功能。|  
|@No__t_0 所需但未提供的功能。|  
  

## <a name="error-0036"></a>錯誤0036  
 如果針對指定的使用者或專案提供了多個功能向量，就會發生例外狀況。  
  
 如果功能向量定義了一次以上，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定功能向量未定義一次以上。  
  
|例外狀況訊息|  
|------------------------|  
|使用者或專案的特徵定義重複。|  
|@No__t_0 的功能定義重複。|  
  

## <a name="error-0037"></a>錯誤0037  
 如果指定了多個標籤資料行，但只允許一個，就會發生例外狀況。  
  
 如果選取一個以上的資料行做為新的標籤資料行，就會發生此錯誤 Azure Machine Learning。 大部分受監督的學習演算法都需要將單一資料行標示為目標或標籤。  
  
**解決方式：** 請務必選取單一資料行做為 [新標籤] 資料行。  
  
|例外狀況訊息|  
|------------------------|  
|已指定多個標籤資料行。|  
  

## <a name="error-0038"></a>錯誤0038  
 如果預期的專案數應該是確切的值，則會發生例外狀況，但卻不是。  
  
 如果預期的專案數應該是確切的值，但卻不是，就會發生 Azure Machine Learning 中的這個錯誤。  如果元素數目不等於有效的預期值，您將會收到這個錯誤。  
  
**解決方式：** 修改輸入，使其具有正確的元素數目。  
  
|例外狀況訊息|  
|------------------------|  
|元素數目無效。|  
|"@No__t_0" 中的元素數目無效。|  
|"@No__t_0" 中的元素數目不等於 {1} 元素的有效數目。|  
  

## <a name="error-0039"></a>錯誤0039  
 如果作業失敗，就會發生例外狀況。  
  
 當內部作業無法完成時，Azure Machine Learning 發生此錯誤。  
  
**解決方式：** 此錯誤是由許多狀況所造成，而且沒有特定的補救方式。  
 下表包含此錯誤的一般訊息，後面接著特定的條件描述。 
 
 如果沒有可用的詳細資訊，請[傳送意見](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)反應，並提供有關產生錯誤和相關條件之模組的資訊。
  
|例外狀況訊息|  
|------------------------|  
|作業失敗。|  
|完成作業時發生錯誤： {0}。|  
  

## <a name="error-0040"></a>錯誤0040  
 呼叫已被取代的模組時，就會發生例外狀況。  
  
 呼叫已被取代的模組時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 以支援的模組取代已被取代的模組。 如需所要使用之模組的相關資訊，請參閱模組輸出記錄檔。  
  
|例外狀況訊息|  
|------------------------|  
|存取已淘汰的模組。|  
|模組 "{0}" 已被取代。 請改用模組 "{1}"。|  
 

## <a name="error-0041"></a>錯誤0041  
 呼叫已被取代的模組時，就會發生例外狀況。  
  
 呼叫已被取代的模組時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 使用一組支援的模組來取代已被取代的模組。 這項資訊應該會顯示在模組輸出記錄檔中。  
  
|例外狀況訊息|  
|------------------------|  
|存取已淘汰的模組。|  
|模組 "{0}" 已被取代。 針對要求的功能使用「{1}」模組。|  
 

## <a name="error-0042"></a>錯誤0042  
 無法將資料行轉換成另一種類型時，就會發生例外狀況。  
  
 如果無法將資料行轉換成指定的類型，就會發生 Azure Machine Learning 中的這個錯誤。  如果模組需要特定的資料類型，例如 datetime、text、浮點數或整數，您就會收到這個錯誤，但無法將現有的資料行轉換成所需的類型。  
 
例如，您可以選取資料行，然後嘗試將它轉換成數值資料類型，以便在數學運算中使用，如果資料行包含不正確資料，就會收到這個錯誤。 

另一個原因是，如果您嘗試使用包含浮點數或多個唯一值的資料行做為分類資料行，您可能會收到這個錯誤。 
  
**解決方案：**

+ 開啟產生錯誤之模組的 [說明] 頁面，並確認資料類型需求。
+ 在輸入資料集中，檢查資料行的資料類型。
+ 檢查來自所謂無架構資料來源的資料。
+ 檢查資料集是否有遺漏值或可能封鎖轉換成所需資料類型的特殊字元。 
    + 數值資料類型應該是一致的：例如，在整數的資料行中檢查浮點數。
    + 在數位資料行中尋找文字字串或 NA 值。 
    + 布林值可以根據所需的資料類型，轉換成適當的標記法。
    + 檢查非 unicode 字元、定位字元或控制字元的文字資料行
    + 日期時間資料應該保持一致，以避免模型化錯誤，但清除可能會因為許多格式而變得複雜原因。 請考慮使用 <!--the [Execute R Script](execute-r-script.md) or -->[執行 Python 腳本](execute-python-script.md)模組以執行清除。  
+ 如有必要，請修改輸入資料集中的值，以便能夠成功轉換資料行。 修改可能包括分類收納、截斷或四捨五入作業、排除極端值，或遺漏值的插補。 請參閱下列文章，以瞭解機器學習服務中的一些常見資料轉換案例：
    + [清除遺漏的資料](clean-missing-data.md)
    + [標準化資料](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> 解決方式不清楚，或不適用於您的案例？ 歡迎您在本文上傳送意見反應，並提供案例的相關資訊，包括模組和資料行的資料類型。 我們會使用這項資訊來提供未來更詳細的疑難排解步驟。  
  
|例外狀況訊息|  
|------------------------|  
|不允許轉換。|  
|無法將類型 {0} 的資料行轉換成 {1} 類型的資料行。|  
|無法將類型 {0} 的資料行 "{2}" 轉換成類型 {1} 的資料行。|  
|無法將類型 {0} 的資料行 "{2}" 轉換成類型 {1} 的資料行 "{3}"。|  
  

## <a name="error-0043"></a>錯誤0043  
 當元素類型未明確地執行 Equals 時，就會發生例外狀況。  
  
 Azure Machine Learning 中的這個錯誤未使用，將會被取代。  
  
**解決方式：** 無.  
  
|例外狀況訊息|  
|------------------------|  
|找不到可存取的明確方法 Equals。|  
|無法比較類型 {1} 之資料行 \\ "{0} \\" 的值。 找不到可存取的明確方法 Equals。|  


## <a name="error-0044"></a>錯誤0044  
 無法從現有的值衍生資料行的元素類型時，就會發生例外狀況。  
  
 當無法在 dataset 中推斷一或多個資料行的類型時，就會發生 Azure Machine Learning 中的這個錯誤。 當串連兩個或多個具有不同元素類型的資料集時，通常會發生此情況。 如果 Azure Machine Learning 無法判斷能夠代表一或多個資料行中所有值的一般類型，而不會遺失資訊，則會產生此錯誤。  
  
**解決方式：** 請確認兩個資料集中所指定資料行的所有值都是相同的類型（數值、布林值、類別、字串、日期等），或者可以強制轉型為相同的類型。  
  
|例外狀況訊息|  
|------------------------|  
|無法衍生資料行的元素類型。|  
|無法衍生資料行 "{0}" 的元素類型--所有元素都是 null 參考。|  
|無法為資料集 "{1}" 的資料行 "{0}" 衍生元素類型--所有元素都是 null 參考。|  
  

## <a name="error-0045"></a>錯誤0045  
 因為來源中的混合元素類型而無法建立資料行時，就會發生例外狀況。  
  
 當結合兩個資料集的元素類型不同時，就會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定兩個資料集中所指定資料行的所有值都屬於相同類型（數值、布林值、類別、字串、日期等等）。  
  
|例外狀況訊息|  
|------------------------|  
|無法建立具有混合元素類型的資料行。|  
|無法建立混合元素類型的識別碼為 "{0}" 的資料行： \ n\tType data [{1}，{0}] {2} 資料 [{3}，{0}] 的 \n\tType 是 {4}。|  
  

## <a name="error-0046"></a>錯誤0046  
 無法在指定的路徑上建立目錄時，就會發生例外狀況。  
  
 當您無法在指定的路徑上建立目錄時，就會發生 Azure Machine Learning 中的這個錯誤。 如果 Hive 查詢輸出目錄的任何部分不正確或無法存取，您就會收到這個錯誤。  
  
**解決方式：** 請重新流覽模組，並確認目錄路徑的格式正確，且可使用目前的認證存取。  
  
|例外狀況訊息|  
|------------------------|  
|請指定有效的輸出目錄。|  
|目錄：無法建立 {0}。 請指定有效的路徑。|  
  

## <a name="error-0047"></a>錯誤0047  
 如果傳遞給模組的某些資料集的特徵資料行數目太小，就會發生例外狀況。  
  
 如果定型的輸入資料集未包含演算法所需的最小資料行數目，則會在 Azure Machine Learning 發生此錯誤。 通常是資料集是空的，或只包含定型資料行。  
  
**解決方式：** 請重新流覽輸入資料集，以確保除了 [標籤] 資料行之外，還有一或多個其他資料行。  
  
|例外狀況訊息|  
|------------------------|  
|輸入資料集的特徵資料行數目小於允許的最小值。|  
|輸入資料集的特徵資料行數目小於允許的最小值 {0} 資料行。|  
|輸入資料集 "{0}" 中的特徵資料行數目小於允許的最小值 {1} 資料行。|  
  

## <a name="error-0048"></a>錯誤0048  
 無法開啟檔案時，就會發生例外狀況。  
  
 當無法開啟檔案進行讀取或寫入時，Azure Machine Learning 發生此錯誤。 基於下列原因，您可能會收到此錯誤：  
  
-   容器或檔案（blob）不存在  
  
-   檔案或容器的存取層級不允許您存取檔案  
  
-   檔案太大，無法讀取或格式錯誤  
  
**解決方式：** 重新流覽模組和您嘗試閱讀的檔案。  
  
 請確認容器和檔案的名稱是否正確。  
  
 使用 Azure 傳統入口網站或 Azure 儲存體工具來確認您有存取該檔案的許可權。  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|例外狀況訊息|  
|------------------------|  
|無法開啟檔案。|  
|開啟檔案時發生錯誤： {0}。|  


## <a name="error-0049"></a>錯誤0049  
 不可能剖析檔案時，會發生例外狀況。  
  
 當無法剖析檔案時，Azure Machine Learning 發生此錯誤。 如果匯[入資料](import-data.md)模組中選取的檔案格式與檔案的實際格式不相符，或者檔案包含無法辨識的字元，您就會收到這個錯誤。  
  
**解決方式：** 重新流覽模組，並更正檔案格式選取專案（如果它不符合檔案格式）。 可能的話，請檢查檔案以確認它不包含任何不合法的字元。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析檔案。|  
|剖析檔案時發生錯誤： {0}。|  
  

## <a name="error-0050"></a>錯誤0050  
 當輸入和輸出檔相同時，就會發生例外狀況。  
  
**解決方式：** Azure Machine Learning 中的這個錯誤未使用，將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|輸入和輸出的指定檔案不能相同。|


## <a name="error-0051"></a>錯誤0051  
 當多個輸出檔相同時，就會發生例外狀況。  
  
**解決方式：** Azure Machine Learning 中的這個錯誤未使用，將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|指定的輸出檔案不能相同。|


## <a name="error-0052"></a>錯誤0052  
 如果未正確指定 Azure 儲存體帳戶金鑰，就會發生例外狀況。  
  
 如果用來存取 Azure 儲存體帳戶的金鑰不正確，就會發生 Azure Machine Learning 中的這個錯誤。 例如，如果在複製並貼上時，Azure 儲存體金鑰已截斷，或使用了錯誤的金鑰，您可能會看到此錯誤。  
  
 如需如何取得 Azure 儲存體帳戶金鑰的詳細資訊，請參閱[查看、複製和重新產生儲存體存取金鑰](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  
  
**解決方式：** 請重新流覽模組，並確認該帳戶的 Azure 儲存體金鑰正確;如有需要，請從 Azure 傳統入口網站重新複製金鑰。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶金鑰不正確。|  
  

## <a name="error-0053"></a>錯誤0053  
 當 matchbox 建議沒有使用者功能或專案時，就會發生例外狀況。  
  
 當找不到功能向量時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定功能向量出現在輸入資料集內。  
  
|例外狀況訊息|  
|------------------------|  
|需要使用者功能或/和專案，但未提供。|  

## <a name="error-0054"></a>錯誤0054  
 如果資料行中的相異值太少而無法完成作業，就會發生例外狀況。  
  
**解決方式：** Azure Machine Learning 中的這個錯誤未使用，將會被取代。  
  
|例外狀況訊息|  
|------------------------|  
|在指定資料行中的相異值太少，無法完成作業。|  
|在指定資料行中的相異值太少，無法完成作業。 所需的最小值是 {0} 元素。|  
|資料行 "{1}" 中的相異值太少，無法完成操作。 所需的最小值是 {0} 元素。|  
  

## <a name="error-0055"></a>錯誤0055  
 呼叫已被取代的模組時，就會發生例外狀況。  如果您嘗試呼叫已被取代的模組，則會出現 Azure Machine Learning 中的這個錯誤。
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|存取已淘汰的模組。|  
|模組 "{0}" 已被取代。|  

## <a name="error-0056"></a>錯誤0056  
 如果您為作業選取的資料行違反需求，就會發生例外狀況。  
  
 當您針對需要特定資料類型之資料行的作業選擇資料行時，就會發生 Azure Machine Learning 中的這個錯誤。 
 
 如果資料行是正確的資料類型，但您所使用的模組要求該資料行也標示為特徵、標籤或類別資料行，也可能會發生此錯誤。  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**解決方案：**
  
1.  檢查目前選取之資料行的資料類型。 

2. 確定選取的資料行是類別、標籤或特徵資料行。  
  
3.  請參閱您在其中進行資料行選取之模組的說明主題，以判斷是否有資料類型或資料行使用方式的特定需求。  
  
3.  使用 [[編輯中繼資料](edit-metadata.md)] 來變更此作業期間的資料行類型。 如果您需要進行下游作業，請務必將資料行類型變更回其原始值，使用另一個 [[編輯中繼資料](edit-metadata.md)] 實例。  
  
|例外狀況訊息|  
|------------------------|  
|一或多個選取的資料行不在允許的類別目錄中。|  
|名稱為 "{0}" 的資料行不在允許的類別目錄中。|  
  

## <a name="error-0057"></a>錯誤0057  
 嘗試建立已經存在的檔案或 blob 時，就會發生例外狀況。  
  
 當您使用[匯出資料](export-data.md)模組或其他模組將管線的結果儲存到 Azure blob 儲存體 Azure Machine Learning，但您嘗試建立已經存在的檔案或 blob 時，就會發生此例外狀況。   
  
**解決方案：**
 
 只有在您先前將屬性 [ **Azure blob 儲存體寫入模式]** 設定為 [**錯誤**] 時，才會收到此錯誤。 根據設計，如果您嘗試將資料集寫入已存在的 blob，此模組就會引發錯誤。
 
 - 開啟模組屬性，並將屬性 [ **Azure blob 儲存體寫入模式]** 變更為 [**覆寫**]。
 - 或者，您也可以輸入不同目的地 blob 或檔案的名稱，並務必指定不存在的 blob。  
  
|例外狀況訊息|  
|------------------------|  
|檔案或 Blob 已經存在。|  
|檔案或 Blob "{0}" 已經存在。|  
  

## <a name="error-0058"></a>錯誤0058  
 如果資料集未包含預期的標籤資料行，就會發生 Azure Machine Learning 中的這個錯誤。  
  
 當提供的標籤資料行不符合學習模組所預期的資料或資料類型，或具有錯誤的值時，也可能會發生此例外狀況。 例如，當定型二元分類器時，使用實際值標籤資料行時，就會產生這個例外狀況。  
  
**解決方式：** 解決方式取決於您所使用的學習模組或訓練人員，以及資料集中資料行的資料類型。 首先，請確認機器學習演算法或訓練課程模組的需求。  
  
 重新流覽輸入資料集。 確認您預期被視為標籤的資料行，具有您要建立之模型的正確資料類型。  
  
 檢查是否有遺漏值的輸入，並在必要時予以排除或取代。  
  
 如有需要，請新增 [[編輯中繼資料](edit-metadata.md)] 模組，並確定 [標籤] 欄已標示為標籤。  
  
|例外狀況訊息|  
|------------------------|  
|標籤資料行不是預期的|  
|[標籤] 資料行不會如「{0}」中所預期。|  
|標籤資料行 "{0}" 不應出現在 "{1}" 中。|  
  

## <a name="error-0059"></a>錯誤0059  
 如果無法剖析資料行選擇器中指定的資料行索引，就會發生例外狀況。  
  
 如果在使用資料行選取器時指定的資料行索引無法剖析，就會發生 Azure Machine Learning 中的這個錯誤。  當資料行索引的格式無效，無法剖析時，您將會收到這個錯誤。  
  
**解決方式：** 請修改資料行索引，以使用有效的索引值。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析一或多個指定的資料行索引或索引範圍。|  
|無法剖析資料行索引或範圍 "{0}"。|  
  

## <a name="error-0060"></a>錯誤0060  
 當資料行選擇器中指定超出範圍的資料行範圍時，就會發生例外狀況。  
  
 當資料行選取器中指定超出範圍的資料行範圍時，Azure Machine Learning 發生此錯誤。 如果資料行選擇器中的資料行範圍並未對應到 dataset 中的資料行，您就會收到這個錯誤。  
  
**解決方式：** 修改資料行選擇器中的資料行範圍，以對應至 dataset 中的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|指定了無效或超出範圍的資料行索引範圍。|  
|資料行範圍 "{0}" 無效或超出範圍。|  
  

## <a name="error-0061"></a>錯誤0061  
 嘗試將資料列加入與資料表資料行數目不同的 DataTable 時，就會發生例外狀況。  
  
 當您嘗試將資料列加入與資料集不同的資料行數目時，會發生 Azure Machine Learning 中的這個錯誤。  如果要加入 dataset 的資料列與輸入資料集的資料行數目不同，您就會收到這個錯誤。  如果資料行數目不同，就無法將資料列附加至 dataset。  
  
**解決方式：** 修改輸入資料集，使其與加入的資料列擁有相同數目的資料行，或修改加入的資料列，使其具有與資料集相同的資料行數目。  
  
|例外狀況訊息|  
|------------------------|  
|所有資料表都必須有相同數目的資料行。|  
  

## <a name="error-0062"></a>錯誤0062  
 嘗試比較具有不同學習模組類型的兩個模型時，就會發生例外狀況。  
  
 當無法比較兩個不同評分資料集的評估計量時，就會產生 Azure Machine Learning 中的這個錯誤。 在此情況下，您無法比較用來產生兩個評分資料集之模型的有效性。  
  
**解決方式：** 確認評分的結果是由相同類型的機器學習服務模型（二元分類、回歸、多類別分類、建議、叢集、異常偵測等）所產生。您比較的所有模型都必須具有相同的學習模組類型。  
  
|例外狀況訊息|  
|------------------------|  
|所有模型都必須具有相同的學習模組類型。|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the pipeline to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the pipeline.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to pipelines in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|例外狀況訊息|  
|------------------------|  
|R 腳本評估期間發生錯誤。|  
|R 腳本評估期間發生下列錯誤：----------R----------的錯誤訊息開頭 {0}-----------R 的錯誤訊息結尾-----------|  
|在評估 R 腳本 "{1}" 的期間發生下列錯誤： R----------的錯誤訊息開頭----------{0}-----------來自 R 的錯誤訊息結尾-----------|  
  


## <a name="error-0064"></a>錯誤0064  
 如果未正確指定 Azure 儲存體帳戶名稱或儲存體金鑰，就會發生例外狀況。  
  
 如果未正確指定 Azure 儲存體帳戶名稱或儲存體金鑰，Azure Machine Learning 就會發生此錯誤。 如果您為儲存體帳戶輸入不正確的帳戶名稱或密碼，就會收到這個錯誤。 如果您手動輸入帳戶名稱或密碼，就可能發生這種情況。 如果帳戶已被刪除，也可能會發生此問題。  
  
**解決方式：** 請確認已正確輸入帳戶名稱和密碼，且帳戶存在。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體帳戶名稱或儲存體金鑰不正確。|  
|帳戶名稱的 Azure 儲存體帳戶名稱 "{0}" 或儲存體金鑰不正確。|  
  

## <a name="error-0065"></a>錯誤0065  
 如果指定的 Azure blob 名稱不正確，就會發生例外狀況。  
  
 如果指定的 Azure blob 名稱不正確，就會發生 Azure Machine Learning 中的這個錯誤。  當下列情況發生時，您將會收到錯誤：  
  
-   在指定的容器中找不到 blob。  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   當格式為 Excel 或 CSV 具有編碼時，只有容器已指定為匯[入資料](import-data.md)要求中的來源。使用這些格式時，不允許在容器內串連所有 blob 的內容。  
  
-   SAS URI 不包含有效 blob 的名稱。  
  
**解決方式：** 重新流覽擲回例外狀況的模組。 請確認指定的 blob 是否存在於儲存體帳戶的容器中，而且該許可權可讓您查看 blob。 如果您的 Excel 或 CSV 具有編碼格式，請確認輸入的格式為 [**容器名稱/檔案名**]。 確認 SAS URI 包含有效 blob 的名稱。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體 blob 不正確。|  
|Azure 儲存體 blob 名稱 "{0}" 不正確|  
  

## <a name="error-0066"></a>錯誤0066  
 如果無法將資源上傳至 Azure Blob，就會發生例外狀況。  
  
 如果無法將資源上傳至 Azure Blob，則會在 Azure Machine Learning 發生此錯誤。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 兩者都會儲存到與包含輸入檔案的帳戶相同的 Azure 儲存體帳戶。  
  
**解決方式：** 重新流覽模組。 請確認 Azure 帳戶名稱、儲存體金鑰和容器都正確，且帳戶具有寫入容器的許可權。  
  
|例外狀況訊息|  
|------------------------|  
|無法將資源上傳至 Azure 儲存體。|  
|檔案 "{0}" 無法上傳到 Azure 儲存體，因為 {1}。|  
  

## <a name="error-0067"></a>錯誤0067  
 如果資料集的資料行數目與預期不同，就會發生例外狀況。  
  
 如果資料集與預期的資料行數目不同，就會發生 Azure Machine Learning 中的這個錯誤。  當 dataset 中的資料行數目與模組在執行期間預期的資料行數目不同時，您會收到這個錯誤。  
  
**解決方式：** 修改輸入資料集或參數。  
  
|例外狀況訊息|  
|------------------------|  
|Datatable 中有未預期的資料行數目。|  
|必須是 "{0}" 資料行，但卻找到 "{1}" 資料行。|  
  

## <a name="error-0068"></a>錯誤0068  
 如果指定的 Hive 腳本不正確，就會發生例外狀況。  
  
 如果 Hive SQL Q 腳本中有語法錯誤，或 Hive 解譯器在執行查詢或腳本時發生錯誤，就會發生此 Azure Machine Learning 錯誤。  
  
**解決方案：**

Hive 的錯誤訊息通常會回報在錯誤記錄檔中，讓您可以根據特定錯誤採取動作。 

+ 開啟模組，並檢查查詢是否有錯誤。  
+ 藉由登入 Hadoop 叢集的 Hive 主控台並執行查詢，確認查詢可在 Azure Machine Learning 外部正常運作。  
+ 請嘗試將批註放在您的 Hive 腳本中，而不是將可執行檔語句和批註混合在同一行。  

### <a name="resources"></a>資源

如需機器學習服務的 Hive 查詢協助，請參閱下列文章：

+ [建立 Hive 資料表並從 Azure Blob 儲存體載入資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [使用 Hive 查詢流覽資料表中的資料](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [針對使用 Hive 查詢之 Hadoop 叢集中的資料建立特性](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive for SQL 使用者功能提要（PDF）](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|例外狀況訊息|  
|------------------------|  
|Hive 腳本不正確。|  
|Hive 腳本 {0} 不正確。|  
  

## <a name="error-0069"></a>錯誤0069  
 如果指定的 SQL 腳本不正確，就會發生例外狀況。  
  
 如果指定的 SQL 腳本有語法問題，或腳本中指定的資料行或資料表無效，就會發生 Azure Machine Learning 中的這個錯誤。 
 
 如果 SQL 引擎在執行查詢或腳本時發生任何錯誤，就會收到這個錯誤。 SQL 錯誤訊息通常會回報在錯誤記錄檔中，讓您可以根據特定錯誤採取動作。  
  
**解決方式：** 請重新流覽模組，並檢查 SQL 查詢是否有錯誤。  
  
 藉由直接登入資料庫伺服器並執行查詢，確認查詢可在 Azure ML 外部正常運作。  
  
 如果模組例外狀況回報 SQL 產生的訊息，請根據回報的錯誤採取動作。 例如，錯誤訊息有時會包含可能發生之錯誤的特定指引：
+ *沒有這類資料行或遺失的資料庫*，表示您可能輸入了不正確的資料行名稱。 如果您確定資料行名稱正確無誤，請嘗試使用方括弧或引號來括住資料行識別碼。
+ *@No__t_1SQL 關鍵字 \> 附近的 SQL 邏輯錯誤*，表示您在指定的關鍵字之前可能有語法錯誤

  
|例外狀況訊息|  
|------------------------|  
|SQL 腳本不正確。|  
|SQL 查詢 "{0}" 不正確。|  
|SQL 查詢 "{0}" 不正確： {1}|  
  

## <a name="error-0070"></a>錯誤0070  
 嘗試存取不存在的 Azure 資料表時，就會發生例外狀況。  
  
 當您嘗試存取不存在的 Azure 資料表時，會發生 Azure Machine Learning 中的這個錯誤。 如果您指定 Azure 儲存體中的資料表，但在讀取或寫入 Azure 表格儲存體時並不存在，則會收到此錯誤。 如果您輸入所需資料表的名稱，或目標名稱與儲存類型不相符，就會發生這種情況。 例如，您想要從資料表讀取，但改為輸入 blob 的名稱。  
  
**解決方式：** 請重新流覽模組，確認資料表的名稱是否正確。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 資料表不存在。|  
|Azure 資料表 "{0}" 不存在。|  
  
## <a name="error-0071"></a>錯誤0071  
 如果提供的認證不正確，就會發生例外狀況。  
  
 如果提供的認證不正確，就會發生 Azure Machine Learning 中的這個錯誤。  
  
 如果模組無法連線到 HDInsight 叢集，您可能也會收到這個錯誤。  
  
**解決方式：** 檢查模組的輸入，並確認帳戶名稱和密碼。  
  
 檢查是否有可能導致錯誤的下列問題：  
  
-   資料集的架構與目的地 datatable 的架構不相符。  
  
-   資料行名稱遺失或拼錯  
  
-   您正在寫入具有不合法字元之資料行名稱的資料表。 一般來說，您可以用方括弧括住這類資料行名稱，但是如果這樣做沒有用，請編輯資料行名稱，只使用字母和底線（_）  
  
-   您嘗試寫入的字串包含單引號  
  
 如果您嘗試連線到 HDInsight 叢集，請確認可以使用提供的認證來存取目標叢集。  
  
|例外狀況訊息|  
|------------------------|  
|傳遞了不正確的認證。|  
|傳遞了不正確的使用者名稱 "{0}" 或密碼|  
  

## <a name="error-0072"></a>錯誤0072  
 當連接逾時時，就會發生例外狀況。  
  
 當連接逾時時，Azure Machine Learning 發生此錯誤。如果目前有與資料來源或目的地的連線問題（例如，網際網路連線速度緩慢），或如果資料集很大，且（或）讀取資料的 SQL 查詢執行複雜的處理，您就會收到這個錯誤。  
  
**解決方式：** 判斷目前是否有與 Azure 儲存體或網際網路連線速度變慢的問題。  
  
|例外狀況訊息|  
|------------------------|  
|發生連接逾時。|  
  

## <a name="error-0073"></a>錯誤0073  
 如果將資料行轉換成另一種類型時發生錯誤，就會發生例外狀況。  
  
 當無法將資料行轉換成另一種類型時，就會發生 Azure Machine Learning 中的這個錯誤。  如果模組需要特定類型，而且無法將此資料行轉換成新的類型，您就會收到這個錯誤。  
  
**解決方式：** 修改輸入資料集，以便根據內部例外狀況來轉換資料行。  
  
|例外狀況訊息|  
|------------------------|  
|無法轉換資料行。|  
|無法將資料行轉換成 {0}。|  
  

## <a name="error-0074"></a>錯誤0074  
 當[編輯中繼資料](edit-metadata.md)嘗試將 sparse 資料行轉換成類別時，就會發生例外狀況。  
  
 當[編輯中繼資料](edit-metadata.md)嘗試將 sparse 資料行轉換成類別時，Azure Machine Learning 發生此錯誤。  當您嘗試使用 [**建立類別**] 選項將稀疏資料行轉換成類別時，將會收到這個錯誤。  Azure machine Learning 不支援稀疏類別陣列，因此模組將會失敗。  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|例外狀況訊息|  
|------------------------|  
|稀疏資料行無法轉換成類別。|  
  

## <a name="error-0075"></a>錯誤0075  
當量化資料集時使用了不正確分類收納函數時，就會發生例外狀況。  
  
當您嘗試使用不支援的方法來將資料分類，或當參數組合無效時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方案：**

這個事件的錯誤處理是在舊版的 Azure Machine Learning 中引進，允許更多的分類收納方法自訂。 目前所有的分類收納方法都是以下拉式清單中的選取專案為基礎，因此在技術上，您應該無法再取得這個錯誤。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|例外狀況訊息|  
|------------------------|  
|使用了不正確分類收納函數。|  
  

## <a name="error-0077"></a>錯誤0077  
 傳遞了未知的 blob 檔案寫入模式時，就會發生例外狀況。  
  
 如果在 blob 檔案目的地或來源的規格中傳遞了不正確引數，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 幾乎所有模組都會將資料匯入或匯出至 Azure blob 儲存體，而控制寫入模式的參數值是使用下拉式清單來指派;因此，無法傳遞不正確值，而且不應該出現此錯誤。 這個錯誤將在較新版本中被取代。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的 blob 寫入模式。|  
|不支援的 blob 寫入模式： {0}。|  
  

## <a name="error-0078"></a>錯誤0078  
 匯[入資料](import-data.md)的 HTTP 選項收到表示重新導向的3xx 狀態碼時，就會發生例外狀況。  
  
 當匯[入資料](import-data.md)的 HTTP 選項收到3xx （301、302、304等等）狀態碼，指出重新導向時，就會發生此 Azure Machine Learning 錯誤。 如果您嘗試連接到 HTTP 來源，將瀏覽器重新導向至另一個頁面，就會收到這個錯誤。 基於安全性理由，不允許重新導向網站做為 Azure Machine Learning 的資料來源。  
  
**解決方式：** 如果網站是受信任的網站，請直接輸入重新導向的 URL。  
  
|例外狀況訊息|  
|------------------------|  
|不允許 Http 重新導向|  
  

## <a name="error-0079"></a>錯誤0079  
 如果指定的 Azure 儲存體容器名稱不正確，就會發生例外狀況。  
  
 如果指定的 Azure 儲存體容器名稱不正確，就會發生 Azure Machine Learning 中的這個錯誤。 如果您未在寫入 Azure Blob 儲存體時，使用 **[以容器開頭的 Blob 路徑**] 選項來指定容器和 blob （檔案）名稱，就會收到這個錯誤。  
  
**解決方式：** 重新流覽[匯出資料](export-data.md)模組，並確認指定的 blob 路徑包含容器和檔案名，格式為**container/filename**。  
  
|例外狀況訊息|  
|------------------------|  
|Azure 儲存體容器名稱不正確。|  
|Azure 儲存體容器名稱 "{0}" 不正確;需要容器/blob 格式的容器名稱。|  
  

## <a name="error-0080"></a>錯誤0080  
 模組不允許具有所有遺漏值的資料行時，就會發生例外狀況。  
  
 當模組所使用的一或多個資料行包含所有遺漏值時，就會產生 Azure Machine Learning 中的這個錯誤。 例如，如果模組正在計算每個資料行的匯總統計資料，它就無法在不包含任何資料的資料行上運作。 在這種情況下，模組執行會因此例外狀況而停止。  
  
**解決方式：** 重新流覽輸入資料集，並移除包含所有遺漏值的任何資料行。  
  
|例外狀況訊息|  
|------------------------|  
|不允許具有所有遺漏值的資料行。|  
|資料行 {0} 遺漏了所有值。|  
  

## <a name="error-0081"></a>錯誤0081  
 如果要減少到的維度數目等於輸入資料集的特徵資料行數目（其中至少包含一個稀疏特徵資料行），則會在 PCA 模組中發生例外狀況。  
  
 如果符合下列條件，就會產生 Azure Machine Learning 中的這個錯誤：（a）輸入資料集至少有一個稀疏資料行，而（b）要求的最後維度數目與輸入維度的數目相同。  
  
**解決方式：** 請考慮將輸出中的維度數目減少為小於輸入中的維度數目。 這在 PCA 的應用程式中是常見的。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|例外狀況訊息|  
|------------------------|  
|對於包含稀疏功能的資料行，要縮減的維度數目應該小於特徵資料行數目。|  
 

## <a name="error-0082"></a>錯誤0082  
 無法成功還原序列化模型時，就會發生例外狀況。  
  
 當較新版本的 Azure Machine Learning 執行時間因重大變更而無法載入儲存的機器學習模型或轉換時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 產生模型或轉換的定型管線必須重新執行，而且必須重新儲存模型或轉換。  
  
|例外狀況訊息|  
|------------------------|  
|無法將模型還原序列化，因為它可能是以較舊的序列化格式進行序列化。 重新定型並重新儲存模型。|  
  

## <a name="error-0083"></a>錯誤0083  
 如果用於定型的資料集無法用於具體的學習模組類型，就會發生例外狀況。  
  
 當資料集與正在定型的學習模組不相容時，就會產生 Azure Machine Learning 中的這個錯誤。 例如，資料集可能會在每個資料列中包含至少一個遺漏值，因此會在定型期間略過整個資料集。 在其他情況下，某些機器學習演算法（例如異常偵測）不會預期標籤存在，如果資料集內有標籤，可能會擲回這個例外狀況。  
  
**解決方式：** 請參閱用來檢查輸入資料集需求的學習模組檔。 檢查資料行，以查看所有必要的資料行是否存在。  
  
|例外狀況訊息|  
|------------------------|  
|用於定型的資料集無效。|  
|{0} 包含用於定型的無效資料。|  
|{0} 包含用於定型的無效資料。 學習模組類型： {1}。|  
  

## <a name="error-0084"></a>錯誤0084  
 評估從 R 腳本產生的分數時，就會發生例外狀況。 目前不支援這種情況。  
  
 如果您嘗試使用其中一個模組來評估包含分數的 R 腳本輸出的模型，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|目前不支援評估 R 產生的分數。|  
  

## <a name="error-0085"></a>錯誤0085  
 當腳本評估失敗併發生錯誤時，就會發生例外狀況。  
  
 當您執行包含語法錯誤的自訂腳本時，Azure Machine Learning 發生此錯誤。  
  
**解決方式：** 在外部編輯器中檢查您的程式碼，並檢查是否有錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|腳本評估期間發生錯誤。|  
|腳本評估期間發生下列錯誤，如需詳細資訊，請參閱輸出記錄檔：----------從 {0} 解譯器開始的錯誤訊息----------{1} 從----------解譯器 {0} 錯誤訊息的結尾----------|  
  

## <a name="error-0086"></a>錯誤0086  
 當計數轉換無效時，就會發生例外狀況。  
  
 當您選取以計數資料表為基礎的轉換，但選取的轉換與目前的資料或新的計數資料表不相容時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 模組支援以兩種不同的格式，儲存組成轉換的計數和規則。 如果您要合併計數資料表，請確認您想要合併的兩個數據表都使用相同的格式。  
  
一般來說，以計數為基礎的轉換只能套用至與原先建立轉換之資料集具有相同架構的資料集。  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|例外狀況訊息|  
|------------------------|  
|指定了不正確計數轉換。|  
|輸入埠 ' {0} ' 的計數轉換無效。|  
|輸入埠 ' {0} ' 的計數轉換無法與輸入埠 ' {1} ' 的計數轉換合併。 檢查以確認用來計算相符專案的中繼資料。|  
  

## <a name="error-0087"></a>錯誤0087  
 針對使用計數模組的學習指定了不正確計數資料表類型時，就會發生例外狀況。  
  
 當您嘗試匯入現有的計數資料表，但資料表與目前的資料或新的計數資料表不相容時，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 有不同的格式可以儲存組成轉換的計數和規則。 如果您要合併計數資料表，請確認兩者都使用相同的格式。  
  
 一般來說，以計數為基礎的轉換只能套用至與原先建立轉換之資料集具有相同架構的資料集。  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>錯誤0088  
 針對使用計數模組的學習指定了不正確計數類型時，就會發生例外狀況。  
  
 當您嘗試使用不同于以計數為基礎的特徵化所支援的計數方法時，會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 一般而言，會從下拉式清單中選擇計算方法，因此您不應該看到此錯誤。  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|例外狀況訊息|  
|------------------------|  
|指定了不正確計數類型。|  
|指定的計數類型 ' {0} ' 不是有效的計數類型。|  
  

## <a name="error-0089"></a>錯誤0089  
 當指定的類別數目小於用於計算之資料集中的實體類別數目時，就會發生例外狀況。  
  
 當您建立計數資料表時，如果標籤資料行包含的類別數目與您在模組參數中指定的不同，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 檢查您的資料集，並確切找出 [標籤] 資料行中有多少相異值（可能的類別）。 當您建立計數資料表時，您必須至少指定此數目的類別。  
  
 計數資料表無法自動判斷可用的類別數目。  
  
 當您建立計數資料表時，不能指定0或小於 [標籤] 資料行中實際的類別數目的任何數位。  
  
|例外狀況訊息|  
|------------------------|  
|類別的數目不正確。 請確定您在 [參數] 窗格中指定的類別數目大於或等於 [標籤] 資料行中的類別數目。|  
|指定的類別數目為 ' {0} '，但它不大於用來計算的資料集內的標籤值 ' {1} '。 請確定您在 [參數] 窗格中指定的類別數目大於或等於 [標籤] 資料行中的類別數目。|  
  

## <a name="error-0090"></a>錯誤0090  
 Hive 資料表建立失敗時，就會發生例外狀況。  
  
 當您使用[匯出資料](export-data.md)或另一個選項將資料儲存至 HDInsight 叢集，且無法建立指定的 Hive 資料表時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請檢查與叢集相關聯的 Azure 儲存體帳戶名稱，並確認您在模組屬性中使用相同的帳戶。  
  
|例外狀況訊息|  
|------------------------|  
|無法建立 Hive 資料表。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱與透過 module 參數傳入的相同。|  
|無法建立 Hive 資料表 "{0}"。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱與透過 module 參數傳入的相同。|  
|無法建立 Hive 資料表 "{0}"。 針對 HDInsight 叢集，請確定與叢集相關聯的 Azure 儲存體帳戶名稱是 "{1}"。|  
 

## <a name="error-0100"></a>錯誤0100  
 針對自訂模組指定不支援的語言時，就會發生例外狀況。  
  
 當您建立自訂模組，且自訂模組 xml 定義檔中**Language**元素的 name 屬性具有不正確值時，Azure Machine Learning 會發生此錯誤。 目前，此屬性唯一有效的值為 `R`。 例如：  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**解決方式：** 確認自訂模組 xml 定義檔中**Language**元素的 name 屬性已設定為 `R`。 儲存檔案、更新自訂模組 zip 封裝，然後再次嘗試新增自訂模組。  
  
|例外狀況訊息|  
|------------------------|  
|指定了不支援的自訂模組語言|  
  

## <a name="error-0101"></a>錯誤0101  
 所有埠和參數識別碼都必須是唯一的。  
  
 當自訂模組 XML 定義檔中的一個或多個埠或參數被指派相同的識別碼值時，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 檢查所有埠和參數的識別碼值是否為唯一。 儲存 xml 檔案、更新自訂模組 zip 封裝，然後再次嘗試新增自訂模組。  
  
|例外狀況訊息|  
|------------------------|  
|模組的所有埠和參數識別碼都必須是唯一的|  
|模組 ' {0} ' 有重複的埠/引數識別碼。 模組的所有埠/引數識別碼都必須是唯一的。|  
  

## <a name="error-0102"></a>錯誤0102  
 無法解壓縮 ZIP 檔案時擲回。  
  
 當您匯入副檔名為 .zip 的壓縮封裝，但該封裝不是 zip 檔案，或檔案未使用支援的 zip 格式時，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 請確定選取的檔案是有效的 .zip 檔案，而且是使用其中一種支援的壓縮演算法進行壓縮。  
  
 如果您在以壓縮格式匯入資料集時收到此錯誤，請確認所有包含的檔案都使用其中一種支援的檔案格式，而且是 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 嘗試將所需的檔案重新新增到新的壓縮 ZIP 檔案夾，然後再次嘗試新增自訂模組。  
  
|例外狀況訊息|  
|------------------------|  
|指定的 ZIP 檔案格式不正確|  


## <a name="error-0103"></a>錯誤0103  
 當 ZIP 檔案不包含任何 .xml 檔案時擲回  
  
 當自訂模組 zip 封裝未包含任何模組定義（.xml）檔案時，Azure Machine Learning 發生此錯誤。 這些檔案必須位於 zip 套件的根目錄中（例如，不在子資料夾內）。  
  
**解決方式：** 將一個或多個 xml 模組定義檔案解壓縮至磁片磁碟機上的暫存資料夾，以確認該檔案位於 zip 套件的根資料夾中。 任何 xml 檔案都應該直接在您解壓縮 zip 套件的資料夾中。 當您建立 zip 封裝時，請務必不要選取包含 xml 檔案的資料夾來壓縮，因為這會在 zip 封裝中建立子資料夾，其名稱與您選取要壓縮的資料夾相同。  
  
|例外狀況訊息|  
|------------------------|  
|指定的 ZIP 檔案不包含任何模組定義檔（.xml 檔案）|  


## <a name="error-0104"></a>錯誤0104  
 當模組定義檔參考到找不到的腳本時擲回  
  
 當自訂模組 xml 定義檔參考的**語言**專案中不存在 zip 封裝中的腳本檔案時，就會擲回 Azure Machine Learning 中的這個錯誤。 腳本檔案路徑是在**Language**元素的**sourceFile**屬性中定義。 來源檔案的路徑相對於 zip 套件的根目錄（與模組 xml 定義檔相同的位置）。 如果腳本檔案位於子資料夾中，則必須指定腳本檔案的相對路徑。 例如，如果所有腳本都儲存在 zip 封裝內的**myScripts**資料夾中，**語言**元素就必須將此路徑新增至**sourceFile**屬性，如下所示。 例如：  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**解決方式：** 請確定自訂模組 xml 定義的**Language**元素中的**sourceFile**屬性值是正確的，而且來源檔案存在於 zip 封裝的正確相對路徑中。  
  
|例外狀況訊息|  
|------------------------|  
|參考的 R 腳本檔案不存在。|  
|找不到參考的 R 指令檔 ' {0} '。 請確定檔案的相對路徑是正確的定義位置。|  


## <a name="error-0105"></a>錯誤0105  
 當模組定義檔包含不支援的參數類型時，就會顯示此錯誤。  
  
 當您建立自訂模組 xml 定義，且定義中的參數或引數類型與支援的類型不相符時，就會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組 xml 定義檔中任何**Arg**元素的 type 屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的參數類型。|  
|指定了不支援的參數類型 ' {0} '。|  


## <a name="error-0106"></a>錯誤0106  
 當模組定義檔定義了不支援的輸入類型時擲回  
  
 當自訂模組 XML 定義中的輸入埠類型與支援的類型不相符時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組 XML 定義檔中 Input 元素的 type 屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸入類型。|  
|指定了不支援的輸入類型 ' {0} '。|  


## <a name="error-0107"></a>錯誤0107  
 當模組定義檔定義了不支援的輸出類型時擲回  
  
 當自訂模組 xml 定義中的輸出埠類型不符合支援的類型時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組 xml 定義檔中 Output 元素的 type 屬性是支援的類型。  
  
|例外狀況訊息|  
|------------------------|  
|不支援的輸出類型。|  
|指定了不支援的輸出類型 ' {0} '。|  


## <a name="error-0108"></a>錯誤0108  
 模組定義檔定義的輸入或輸出埠數目超過支援的時擲回  
  
 當自訂模組 xml 定義中定義了太多輸入或輸出埠時，就會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組 xml 定義中定義的輸入和輸出埠數目上限未超過支援的埠數目上限。  
  
|例外狀況訊息|  
|------------------------|  
|已超過支援的輸入或輸出埠數目。|  
|超過支援的 ' {0} ' 埠數目。 允許的 ' {0} ' 埠數目上限為 ' {1} '。| 

## <a name="error-0109"></a>錯誤0109  
 當模組定義檔不正確地定義資料行選擇器時擲回  
  
 當資料行選擇器引數的語法在自訂模組 xml 定義中包含錯誤時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 當資料行選擇器引數的語法在自訂模組 xml 定義中包含錯誤時，就會產生此錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|資料行選擇器的語法不受支援。|  
  

## <a name="error-0110"></a>錯誤0110  
 當模組定義檔定義的資料行選擇器參考了不存在的輸入埠識別碼時擲回  
  
 當 ColumnPicker 類型之 Arg 的 Properties 元素中的*portId*屬性與輸入埠的識別碼值不相符時，就會產生此 Azure Machine Learning 錯誤。  
  
**解決方式：** 請確定 portId 屬性符合自訂模組 xml 定義中所定義輸入埠的識別碼值。  
  
|例外狀況訊息|  
|------------------------|  
|資料行選擇器參考了不存在的輸入埠識別碼。|  
|資料行選擇器參考了不存在的輸入埠識別碼 ' {0} '。|  
  

## <a name="error-0111"></a>錯誤0111  
 當模組定義檔定義了不正確屬性時擲回  
  
 當將不正確屬性指派給自訂模組 XML 定義中的元素時，會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組元素支援屬性。  
  
|例外狀況訊息|  
|------------------------|  
|屬性定義無效。|  
|屬性定義 ' {0} ' 無效。|  
  

## <a name="error-0112"></a>錯誤0112  
 無法剖析模組定義檔時擲回  
  
 當 xml 格式發生錯誤，導致無法將自訂模組 XML 定義剖析為有效的 XML 檔案時，就會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定每個專案都已正確開啟和關閉。 請確定 XML 格式沒有任何錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析模組定義檔。|  
|無法剖析模組定義檔 ' {0} '。|  
  

## <a name="error-0113"></a>錯誤0113  
 模組定義檔包含錯誤時擲回。  
  
 當可以剖析自訂模組 XML 定義檔但包含錯誤（例如自訂模組不支援的元素定義）時，就會產生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確定自訂模組定義檔會定義自訂模組支援的元素和屬性。  
  
|例外狀況訊息|  
|------------------------|  
|模組定義檔包含錯誤。|  
|模組定義檔 ' {0} ' 包含錯誤。|  
|模組定義檔 ' {0} ' 包含錯誤。 {1}|  
  

## <a name="error-0114"></a>錯誤0114  
 建立自訂模組失敗時擲回。  
  
 當自訂模組組建失敗時，會產生 Azure Machine Learning 中的這個錯誤。 新增自訂模組時遇到一或多個自訂模組相關的錯誤時，就會發生這種情況。 此錯誤訊息中會報告其他錯誤。  
  
**解決方式：** 解決此例外狀況訊息中所報告的錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|無法建立自訂模組。|  
|自訂模組組建失敗，發生錯誤： {0}|  
  

## <a name="error-0115"></a>錯誤0115  
 當自訂模組預設腳本具有不支援的副檔名時擲回。  
  
 當您為使用未知副檔名的自訂模組提供腳本時，會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 請確認自訂模組中包含的任何腳本檔案的檔案格式和副檔名。  
  
|例外狀況訊息|  
|------------------------|  
|預設腳本不支援擴充功能。|  
|預設腳本不支援的 file 擴充功能 {0}。|  
  

## <a name="error-0121"></a>錯誤0121  
 當 SQL 寫入因為資料表無法寫入而失敗時擲回  
  
 當您使用 [[匯出資料](export-data.md)] 模組將結果儲存至 SQL 資料庫中的資料表，而且無法寫入資料表時，就會產生 Azure Machine Learning 中的這個錯誤。 一般而言，如果[匯出資料](export-data.md)模組成功建立與 SQL Server 實例的連線，但卻無法將 Azure ML 資料集的內容寫入資料表，您就會看到此錯誤。  
  
**解決方案：**
 - 開啟[匯出資料](export-data.md)模組的 [屬性] 窗格，並確認已正確輸入資料庫和資料表名稱。 
 - 請檢查您要匯出之資料集的架構，並確定資料與目的地資料表相容。
 - 確認與使用者名稱和密碼相關聯的 SQL 登入具有寫入資料表的許可權。 
 - 如果例外狀況包含來自 SQL Server 的其他錯誤資訊，請使用該資訊進行更正。  
  
|例外狀況訊息|  
|------------------------|  
|已連接到伺服器，無法寫入資料表。|  
|無法寫入 Sql 資料表： {0}|  


## <a name="error-0122"></a>錯誤0122  
 如果指定多個加權資料行，但只允許一個，就會發生例外狀況。  
  
 當選取太多資料行作為權數資料行時，Azure Machine Learning 發生此錯誤。  
  
**解決方式：** 檢查輸入資料集及其中繼資料。 請確定只有一個資料行包含權數。  
  
|例外狀況訊息|  
|------------------------|  
|已指定多個加權資料行。|  


## <a name="error-0123"></a>錯誤0123  
 如果將向量的資料行指定為標籤資料行，就會發生例外狀況。  
  
 如果您使用向量做為標籤資料行，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方式：** 如有必要，請變更資料行的資料格式，或選擇不同的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|向量的資料行指定為標籤資料行。|  


## <a name="error-0124"></a>錯誤0124  
 如果將非數值資料行指定為權數資料行，就會發生例外狀況。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|非數值資料行指定為加權資料行。|  
  


## <a name="error-0125"></a>錯誤0125  
 當多個資料集的架構不相符時，就會擲回。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|資料集架構不相符。|  


## <a name="error-0126"></a>錯誤0126  
 如果使用者指定的 SQL 網域在 Azure ML 中不受支援，就會發生例外狀況。  
  
 當使用者指定 Azure Machine Learning 中不支援的 SQL 網域時，就會產生此錯誤。 如果您嘗試連接到未列入白名單的網域中的資料庫伺服器，就會收到這個錯誤。 目前允許的 SQL 網域為： ". database.windows.net"、"cloudapp.net" 或 "database.secure.windows.net"。 也就是，伺服器必須是 Azure SQL 伺服器或 Azure 上虛擬機器中的伺服器。  
  
**解決方式：** 重新流覽模組。 確認 SQL 資料庫伺服器屬於其中一個已接受的網域：  
  
-   . database.windows.net  
  
-   . cloudapp.net  
  
-   . database.secure.windows.net  
  
|例外狀況訊息|  
|------------------------|  
|不支援的 SQL 網域。|  
|Azure ML 目前不支援 SQL 網域 {0}|  
  

## <a name="error-0127"></a>錯誤0127  
 影像圖元大小超過允許的限制  
  
 如果您要從分類的影像資料集讀取影像，而且影像比模型可以處理的還大，就會發生此錯誤。  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|例外狀況訊息|  
|------------------------|  
|影像圖元大小超過允許的限制。|  
|檔案 ' {0} ' 中的影像圖元大小超過允許的限制： ' {1} '|  


## <a name="error-0128"></a>錯誤0128  
 類別資料行的條件機率數目超過限制。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|類別資料行的條件機率數目超過限制。|  
|類別資料行的條件機率數目超過限制。 資料行 ' {0} ' 和 ' {1} ' 是有問題的配對。|  


## <a name="error-0129"></a>錯誤0129  
 Dataset 中的資料行數目超過允許的限制。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|Dataset 中的資料行數目超過允許的限制。|  
|' @No__t_0 ' 中資料集的資料行數超過允許的數目。 '|  
|' @No__t_0 ' 中資料集的資料行數超過允許的限制 ' {1} '。|  
|' @No__t_0 ' 中資料集的資料行數超過允許的 ' {1} ' 限制 ' {2} '。|  
## <a name="error-0130"></a>錯誤0130  
 訓練資料集中的所有資料列都包含遺漏值時，就會發生例外狀況。  
  
 訓練資料集內的某些資料行是空的時，就會發生這種情況。  
  
**解決方式：** 使用 [[清除遺漏的資料](clean-missing-data.md)] 模組來移除具有所有遺漏值的資料行。  
  
|例外狀況訊息|  
|------------------------|  
|訓練資料集中的所有資料列都包含遺漏值。  請考慮使用 [清除遺漏的資料] 模組來移除遺漏的值。|  
 

## <a name="error-0131"></a>錯誤0131  
 如果 zip 檔案中的一或多個資料集無法解壓縮並正確註冊，就會發生例外狀況  
  
 當 zip 檔案中的一或多個資料集無法解壓縮並正確讀取時，就會產生此錯誤。 如果解壓縮失敗，因為 zip 檔案本身或其中一個檔案已損毀，或是嘗試解壓縮並展開檔案時發生系統錯誤，您將會收到這個錯誤。  
  
**解決方式：** 使用錯誤訊息中提供的詳細資料，以決定如何繼續進行。  
  
|例外狀況訊息|  
|------------------------|  
|上傳壓縮的資料集失敗|  
|壓縮的資料集 {0} 失敗，並出現下列訊息： {1}|  
|已壓縮的資料集 {0} 因 {1} 例外狀況而失敗，訊息為： {2}|  
  

## <a name="error-0132"></a>錯誤0132  
 未指定要解壓縮的檔案名;在 zip 檔案中找到多個檔案。  
  
 當未指定檔案名稱進行解壓縮時，就會產生此錯誤。在 zip 檔案中找到多個檔案。 如果 .zip 檔案包含一個以上的壓縮檔案，但您未在模組的**屬性**窗格中指定要解壓縮的**資料集**文字方塊中的檔案，就會收到這個錯誤。 目前，每次執行模組時，只能解壓縮一個檔案。  
  
**解決方式：** 錯誤訊息會提供 .zip 檔案中找到的檔案清單。 複製所需檔案的名稱，並將它貼到 [**要解壓縮的資料集**] 文字方塊中。  
  
|例外狀況訊息|  
|------------------------|  
|Zip 檔案包含多個檔案;您必須指定要展開的檔案。|  
|檔案包含一個以上的檔案。 指定要展開的檔案。 找到下列檔案： {0}|  
  

## <a name="error-0133"></a>錯誤0133  
 在 zip 檔案中找不到指定的檔案  
  
 當 [**屬性**] 窗格的 [**要解壓縮的資料集**] 欄位中輸入的檔案名與 .zip 檔案中找到的任何檔案名稱不相符時，就會產生此錯誤。 此錯誤最常見的原因是輸入錯誤，或在錯誤的封存檔案中搜尋檔案以進行擴充。  
  
**解決方式：** 重新流覽模組。 如果您想要解壓縮的檔案名出現在找到的檔案清單中，請將檔案名複製並貼到 [**要解壓縮的資料集**] 屬性方塊中。 如果您在清單中看不到想要的檔案名，請確認您有正確的 .zip 檔案和所需檔案的正確名稱。  
  
|例外狀況訊息|  
|------------------------|  
|在 zip 檔案中找不到指定的檔案。|  
|找不到指定的檔案。 找到下列檔案： {0}|  
  

## <a name="error-0134"></a>錯誤0134
當標籤資料行遺失或已標記的資料列數目不足時，就會發生例外狀況。  
  
當模組需要標籤資料行，但您未在資料行選取中包含一個，或 [標籤] 資料行遺漏太多值時，就會發生此錯誤。

當先前的作業變更資料集時，可能會發生此錯誤，使得下游作業可以使用不足的資料列。 例如，假設您在資料**分割和範例**模組中使用運算式，以根據值來分割資料集。 如果找不到運算式的相符專案，分割區所產生的其中一個資料集就會是空的。

解決方案： 

 如果您在資料行選取範圍中包含標籤資料行，但無法辨識它，請使用 [[編輯中繼資料](edit-metadata.md)] 模組，將它標示為標籤資料行。
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->然後，您可以使用 [[清除遺漏的資料](clean-missing-data.md)] 模組，移除標籤資料行中有遺漏值的資料列。 

 請檢查您的輸入資料集，以確定它們包含有效的資料，以及足以滿足作業需求的資料列。 如果有許多演算法需要最少的資料列數，但資料只包含幾個資料列，或只有一個標頭，則會產生錯誤訊息。
  
|例外狀況訊息|
|------------------------|
|當標籤資料行遺失或已標記的資料列數目不足時，就會發生例外狀況。|  
|標籤資料行遺失或小於 {0} 標籤的資料列時發生例外狀況|  
  

## <a name="error-0135"></a>錯誤0135  
 僅支援以距心為基礎的叢集。  
  
**解決方式：** 如果您嘗試評估以自訂叢集演算法為基礎的群集模型，但該叢集不使用距心來初始化叢集，您可能會遇到這個錯誤訊息。  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|例外狀況訊息|  
|------------------------|  
|僅支援以距心為基礎的叢集。|  
  

## <a name="error-0136"></a>錯誤0136  
 未傳回檔案名;無法處理檔案。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|未傳回檔案名;無法處理檔案。|  
  

## <a name="error-0137"></a>錯誤0137  
 Azure 儲存體 SDK 在讀取或寫入期間，于資料表屬性和資料集資料行之間轉換時發生錯誤。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|Azure 資料表儲存體屬性和資料集資料行之間發生轉換錯誤。|  
|Azure 資料表儲存體屬性和資料集資料行之間發生轉換錯誤。 其他資訊： {0}|  

## <a name="error-0138"></a>錯誤0138  
 記憶體已用盡，無法完成模組的執行。 將資料集縮減取樣可能有助於減輕問題。  
  
 當執行的模組所需的記憶體多於 Azure 容器中可用的數量時，就會發生此錯誤。 如果您使用的是大型資料集，而目前的作業無法納入記憶體中，就可能會發生這種情況。  
  
**解決方式：** 如果您嘗試讀取大型資料集，但無法完成作業，則資料集的縮減取樣可能會有説明。  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|例外狀況訊息|  
|------------------------|  
|記憶體已用盡，無法完成模組的執行。|  
  

## <a name="error-0139"></a>錯誤0139  
 無法將資料行轉換成另一種類型時，就會發生例外狀況。  
  
 當您嘗試將資料行轉換成不同的資料類型，但目前的作業或模組不支援該類型時，會發生 Azure Machine Learning 中的這個錯誤。  
  
 當模組嘗試以隱含方式轉換資料以符合目前模組的需求時，也可能會出現此錯誤，但無法進行轉換。  
  
**解決方案：**

1. 檢查您的輸入資料，並判斷您想要使用之資料行的確切資料類型，以及產生錯誤之資料行的資料類型。 有時候，您可能認為資料類型是正確的，但發現上游作業已修改資料類型或資料行的使用方式。 使用 [[編輯中繼資料](edit-metadata.md)] 模組，將資料行中繼資料重設為其原始狀態。 
2. 查看 [模組說明] 頁面，確認指定之作業的需求。 判斷目前模組支援的資料類型，以及支援的值範圍。 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. 請考慮是否可以將資料行轉換或轉換成不同的資料類型。 下列模組全都提供相當大的彈性和能力來修改資料： 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [執行 Python 腳本](execute-python-script.md)。  

> [!NOTE]
> 仍無法運作嗎？ 請考慮對問題提供額外的意見反應，以協助我們開發更好的疑難排解指引。 只要提交此頁面的意見反應，並提供產生錯誤之模組的名稱，以及失敗的資料類型轉換。
  
|例外狀況訊息|  
|------------------------|  
|不允許轉換。|  
|無法轉換： {0}。|  
|無法在資料列 {1} 上轉換： {0}。|  
|無法將類型 {0} 的資料行轉換成資料列 {2} 上 {1} 類型的資料行。|  
|無法將類型 {0} 的資料行 "{2}"，轉換為數據列 {3} 上 {1} 類型的資料行。|  
|無法將類型 {0} 的資料行 "{2}"，轉換為數據列 {4} 上類型 {1} 的資料行 "{3}"。| 

## <a name="error-0140"></a>錯誤0140  
 如果傳遞的資料行集引數不包含標籤資料行以外的其他資料行，就會發生例外狀況  
  
 如果您將資料集連接到需要多個資料行的模組（包括功能），但只提供了 [標籤] 資料行，就會發生這個錯誤。  
  
**解決方式：** 選擇至少一個要包含在資料集內的特徵資料行。  
  
|例外狀況訊息|  
|------------------------|  
|指定的資料行集不包含標籤資料行以外的其他資料行。|  
  

## <a name="error-0141"></a>錯誤0141  
 如果分類和字串資料行中選取的數值資料行和唯一值的數目太小，就會發生例外狀況。  
  
 當選取的資料行中沒有足夠的唯一值來執行作業時，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 某些作業會對功能和類別資料行執行統計作業，而且如果沒有足夠的值，則運算可能會失敗，或傳回不正確結果。 檢查您的資料集，以查看功能和標籤資料行中有多少值，並判斷您嘗試執行的作業是否為統計有效。  
  
 如果源資料集有效，您也可以檢查某些上游資料操作或中繼資料作業是否已變更資料並移除某些值。  
  
 如果上游作業包含分割、取樣或重新取樣，請確認輸出包含預期的資料列和值數目。  
  
|例外狀況訊息|  
|------------------------|  
|[類別目錄] 和 [字串] 資料行中選取的數值資料行和唯一值的數目太小。|  
|在分類和字串資料行（目前 {0}）中選取的數值資料行和唯一值的總數至少應為 {1}|  
  

## <a name="error-0142"></a>錯誤0142  
 當系統無法載入要驗證的憑證時，就會發生例外狀況。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|無法載入憑證。|  
|無法載入憑證 {0}。 其指紋為 {1}。|  
  

## <a name="error-0143"></a>錯誤0143  
 無法剖析應從 GitHub 取得的使用者提供 URL。  
  
 當您指定不正確 URL，而此模組需要有效的 GitHub URL 時，就會發生此錯誤 Azure Machine Learning。  
  
**解決方式：** 確認 URL 參照的是有效的 GitHub 存放庫。 不支援其他網站類型。  
  
|例外狀況訊息|  
|------------------------|  
|URL 不是來自 github.com。|  
|URL 不是來自 github.com： {0}|  

## <a name="error-0144"></a>錯誤0144  
 使用者提供的 GitHub url 缺少預期的部分。  
  
 當您使用不正確 URL 格式指定 GitHub 檔案來源時，Azure Machine Learning 發生此錯誤。  
  
**解決方式：** 請檢查 GitHub 存放庫的 URL 是否有效，並以 \blob\ 或 \tree \\ 結尾。  
  
|例外狀況訊息|  
|------------------------|  
|無法剖析 GitHub URL。|  
|無法剖析 GitHub URL （在存放庫名稱後面必須有 ' \blob \\ ' 或 ' \tree \\ '）： {0}|  

## <a name="error-0145"></a>錯誤0145  
 因為某些原因，所以無法建立複寫目錄。  
  
 當模組無法建立指定的目錄時，Azure Machine Learning 發生此錯誤。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|無法建立複寫目錄。|  
  

## <a name="error-0146"></a>錯誤0146  
 將使用者檔案解壓縮至本機目錄時，合併的路徑可能太長。  
  
 當您解壓縮檔案時，會發生此 Azure Machine Learning 錯誤，但解壓縮時某些檔案名太長。  
  
**解決方式：** 編輯檔案名，使結合的路徑和檔案名不超過248個字元。  
  
|例外狀況訊息|  
|------------------------|  
|複寫路徑長度超過248個字元，請縮短腳本名稱或路徑。|  

## <a name="error-0147"></a>錯誤0147  
 因為某些原因，所以無法從 GitHub 下載內容  
  
 當您無法從 GitHub 讀取或下載指定的檔案時，Azure Machine Learning 發生此錯誤。  
  
**解決方式：** 問題可能是暫時性的;您可能會嘗試在另一次存取檔案。 或者，請確認您擁有必要的許可權，而且來源有效。  
  
|例外狀況訊息|  
|------------------------|  
|GitHub 存取錯誤。|  
|GitHub 存取錯誤。 {0}|  
  

## <a name="error-0148"></a>錯誤0148  
 解壓縮資料或建立目錄時發生未經授權的存取問題。  
  
 當您嘗試建立目錄或從儲存體讀取資料，但沒有必要的許可權時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
**解決方案：**
  
|例外狀況訊息|  
|------------------------|  
|解壓縮資料時發生未經授權的存取例外狀況。|  
  

## <a name="error-0149"></a>錯誤0149  
 使用者檔案不存在於 GitHub 套件組合中。  
  
 當找不到指定的檔案時，Azure Machine Learning 發生此錯誤。  
  
解決方案： 
  
|例外狀況訊息|  
|------------------------|  
|找不到 GitHub 檔案。|  
|找不到 GitHub 檔案。： {0}|  
  

## <a name="error-0150"></a>錯誤0150  
 來自使用者封裝的腳本無法解壓縮，很可能是因為與 GitHub 檔案發生衝突。  
  
 當無法解壓縮腳本時，通常是在有相同名稱的現有檔案時，就會發生此錯誤 Azure Machine Learning。  
  
解決方案：
  
|例外狀況訊息|  
|------------------------|  
|無法將配套解壓縮;GitHub 檔案可能發生名稱衝突。|  
  

## <a name="error-0151"></a>錯誤0151  
 寫入雲端存放裝置時發生錯誤。 檢查 URL。  
  
 當模組嘗試將資料寫入雲端存放裝置，但 URL 無法使用或無效時，就會發生 Azure Machine Learning 中的這個錯誤。  
  
解決方式：檢查 URL 並確認它可寫入。  
  
|例外狀況訊息|  
|------------------------|  
|寫入雲端儲存體時發生錯誤（可能是錯誤的 url）。|  
|寫入雲端存放裝置時發生錯誤： {0}。 檢查 url。|  
  
## <a name="error-0152"></a>錯誤0152  
 Azure 雲端類型在模組內容中指定不正確。  
  
|例外狀況訊息|  
|------------------------|  
|錯誤的 Azure 雲端類型|  
|錯誤的 Azure 雲端類型： {0}|  
  
## <a name="error-0153"></a>錯誤0153  
 指定的儲存體結束點無效。  
  
|例外狀況訊息|  
|------------------------|  
|錯誤的 Azure 雲端類型|  
|錯誤的儲存端點： {0}|  

## <a name="error-0154"></a>錯誤0154  
 無法解析指定的伺服器名稱  
  
|例外狀況訊息|  
|------------------------|  
|無法解析指定的伺服器名稱|  
|無法解析指定的伺服器 {0} documents.azure.com。|

## <a name="error-0155"></a>錯誤0155  
 DocDb 用戶端擲回例外狀況  
  
|例外狀況訊息|  
|------------------------|  
|DocDb 用戶端擲回例外狀況|  
|DocDb 用戶端： {0}|

## <a name="error-0156"></a>錯誤0156  
 HCatalog 伺服器的回應錯誤。  
  
|例外狀況訊息|  
|------------------------|  
|HCatalog 伺服器的回應錯誤。 檢查所有服務是否正在執行。|  
|HCatalog 伺服器的回應錯誤。 檢查所有服務是否正在執行。 錯誤詳細資料： {0}|

## <a name="error-0157"></a>錯誤0157  
 從 Azure Cosmos DB 讀取時發生錯誤，因為檔架構不一致或不同。 讀取器要求所有檔都必須有相同的架構。  
  
|例外狀況訊息|  
|------------------------|  
|偵測到具有不同架構的檔。 請確定所有檔都具有相同的架構|

## <a name="error-1000"></a>錯誤1000  
內部程式庫例外狀況。  
  
此錯誤是為了捕捉其他未處理的內部引擎錯誤所提供。 因此，此錯誤的原因可能會因產生錯誤的模組而有所不同。  
  
若要取得更多協助，建議您將伴隨錯誤的詳細訊息張貼至 Azure Machine Learning 論壇，連同案例的描述，包括當做輸入使用的資料。 這項意見反應可協助我們設定錯誤的優先順序，並找出最重要的問題以進行進一步的工作。  
  
|例外狀況訊息|  
|------------------------|  
|程式庫例外狀況。|  
|程式庫例外狀況： {0}|  
|{0} 程式庫例外狀況： {1}|  
