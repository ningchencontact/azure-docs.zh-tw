---
title: 執行 R 指令碼：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中執行 R 指令碼模組執行 R 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518049"
---
# <a name="execute-r-script"></a>執行 R 指令碼

這篇文章說明如何使用**執行 R 指令碼**視覺介面實驗中執行 R 程式碼的模組。

使用 R，您可以執行目前不支援現有的模組這類的工作： 
- 建立自訂資料轉換
- 使用您自己的度量來評估預測
- 作為獨立模組中視覺介面使用未實作的演算法建置模型

## <a name="r-version-support"></a>R 版本支援

Azure Machine Learning 服務的視覺介面使用 CRAN (Comprehensive R Archive Network) 分佈的。目前使用的版本是 CRAN 3.5.1。

## <a name="supported-r-packages"></a>支援的 R 封裝

R 環境已預先安裝了超過 100 個封裝。 如需完整清單，請參閱[預先安裝 R 封裝](#pre-installed-r-packages)。

您也可以將下列程式碼加入任何**執行 R 指令碼**模組，並查看已安裝的套件。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>安裝 R 套件
若要安裝其他 R 套件，請使用`install.packages()`方法。 請務必指定 CRAN 儲存機制。 針對每個安裝套件**執行 R 指令碼**模組，而且不讓其他共用**執行 R 指令碼**模組。

此範例示範如何安裝 Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>如何設定執行 R 指令碼

**執行 R 指令碼**模組包含範例程式碼，您可以使用做為起點。 若要設定**執行 R 指令碼**模組，提供一組輸入和執行程式碼。

![R 模組](media/module/execute-r-script.png)

儲存在視覺化介面中的資料集自動轉換為 R 資料框架時載入此模組。

1.  新增**執行 R 指令碼**模組至您的實驗。

    > [!NOTE]
    > 所有的資料傳遞至**執行 R 指令碼**模組會轉換成 R`data.frame`格式。

1. 連接任何所需的指令碼的輸入。 輸入是選擇性的而且可以包含資料和其他的 R 程式碼。

    * **Dataset1**:參考做為第一個輸入`dataframe1`。 輸入資料集必須格式化為 CSV、 TSV、 ARFF 或您可以連接的 Azure Machine Learning 資料集。

    * **Dataset2**:參考做為第二個輸入`dataframe2`。 此資料集也必須格式化為 CSV、 TSV、 ARFF 檔案，或為 Azure Machine Learning 資料集。

    * **指令碼組合**:第三個輸入接受的 ZIP 檔案。 Zip 壓縮的檔案可以包含多個檔案和多個檔案類型。

1. 在 [ **R 指令碼**] 文字方塊中，輸入或貼上有效的 R 指令碼。

    若要可協助您開始使用，請**R 指令碼**文字方塊會預先填入範例程式碼，您可以編輯或取代。

    * 此指令碼必須包含名為函式`azureml_main`，這是此模組的進入點。

    * 進入點函式可以包含最多兩個輸入引數：`Param<dataframe1>`和 `Param<dataframe2>`
 
    > [!NOTE]
    >  現有的 R 程式碼可能需要執行中的視覺介面實驗的微幅變更。 比方說，您以 CSV 格式提供的輸入的資料應明確轉換至資料集之前，您可以在程式碼中使用它。 在某些方面與中的視覺介面使用的資料和資料行類型的 R 語言中使用的資料和資料行類型也不同。

1.  **隨機種子**:輸入要使用 R 環境內的隨機種子值的值。 此參數相當於呼叫`set.seed(value)`R 程式碼中。  

1. 執行實驗。  

## <a name="results"></a>結果

**執行 R 指令碼**模組可以傳回多個輸出，但是他們必須提供為 R 資料框架。 資料框架會自動轉換至與其他模組的相容性的視覺介面資料集。

標準訊息和錯誤都會從 R 傳回模組的記錄檔中。

## <a name="sample-scripts"></a>範例指令碼

有許多種方式，您可以使用自訂 R 指令碼來擴充您的實驗。  本節提供範例程式碼的常見工作。


### <a name="add-r-script-as-an-input"></a>做為輸入 přidat skript jazyka R

**執行 R 指令碼**模組可支援任意的 R 指令碼檔案，做為輸入。 若要這樣做，他們必須上傳至您的工作區，做為 ZIP 檔案的一部分。

1. 若要上傳包含您的工作區的 R 程式碼的 ZIP 檔案，請按一下**新增**，按一下**資料集**，然後選取**從本機檔案**而**Zip 檔案**選項。  

1. 請確認 zip 壓縮的檔案可用於**儲存的資料集**清單。

1.  若要將資料集連接**指令碼組合**輸入連接埠。

1. ZIP 檔案中包含的所有檔案都可在實驗執行時間期間。 

    如果指令碼組合檔案包含的目錄結構，則會保留結構。 不過，您必須更改您的程式碼前面加上目錄 **。 指令碼套件組合 /** 的路徑。

### <a name="process-data"></a>處理資料

下列範例示範如何調整並正規化輸入的資料：

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>做為輸入讀取 ZIP 檔案

此範例示範如何使用 ZIP 檔案中的資料集，做為輸入來**執行 R 指令碼**模組。

1. 建立資料檔採用 CSV 格式，並將它命名為"mydatafile.csv"。
1. 建立 ZIP 檔案，並將 CSV 檔案新增至封存。
1. 壓縮的檔案上傳至您的 Azure Machine Learning 工作區中。 
1. 連接到產生的資料集**組合**輸入您**執行 R 指令碼**模組。
1. 使用下列程式碼來讀取 CSV 資料從壓縮的檔案。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>複寫資料列

此範例示範如何複寫正以平衡樣本資料集內的記錄：

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>執行 R 指令碼模組之間傳遞 R 物件

您可以在執行個體之間傳遞 R 物件**執行 R 指令碼**模組使用內部序列化機制。 這個範例假設您想要移動名為的 R 物件`A`兩個之間**執行 R 指令碼**模組。

1. 新增第一個**執行 R 指令碼**模組實驗和下列程式碼中的型別**R 指令碼**文字方塊中，以建立序列化的物件`A`模組中的資料行的輸出資料的資料表：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    明確轉換成整數類型會這樣做，因為序列化函數會輸出在 R 中的資料`Raw`視覺介面不支援的格式。

1. 新增的第二個執行個體**執行 R 指令碼**模組，並將它連接到前一個模組的輸出連接埠。

1. 輸入中的下列程式碼**R 指令碼**文字方塊中，擷取物件`A`從輸入資料表。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>預先安裝的 R 套件

目前可供使用的預先安裝 R 套件的清單：

|              |            | 
|--------------|------------| 
| 封裝      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| 基底         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| 掃帚        | 0.5.2      | 
| callr        | 3.2.0      | 
| 插入號        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| 叢集      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| 編譯器     | 3.5.1      | 
| 蠟筆       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| 評估     | $0.14 元       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| 外部索引      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 泛型     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| 黏附         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| 圖形     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| 方格         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| 樂園        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| 迭代器    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| 標記     | 0.3        | 
| 斜格紋      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| 矩陣       | 1.2-17     | 
| 方法      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| 平行     | 3.5.1      | 
| 要件       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| 食譜      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| 標尺       | 1.0.0      | 
| 選取      | 0.4-1      | 
| 空間      | 7.3-11     | 
| 曲線      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| 生存     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| 工具        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| 公用程式        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| 鬚值      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 動物園          | 1.8-6      | 

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 