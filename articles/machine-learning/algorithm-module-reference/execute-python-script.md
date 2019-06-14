---
title: 執行 Python 指令碼：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中執行 Python 指令碼模組執行 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029126"
---
# <a name="execute-python-script-module"></a>執行 Python 指令碼模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來執行 Python 程式碼。 如需 Python 的架構和設計原則的詳細資訊，請參閱[下列文件。](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

使用 Python，您可以執行目前不支援現有的模組這類的工作：

+ 視覺化資料使用 `matplotlib`
+ 使用 Python 程式庫列舉資料集和您的工作區中的模型
+ 讀取、 載入和處理來自不支援的來源資料[匯入資料](./import-data.md)模組
+ 執行您自己的深入學習程式碼 


Azure Machine Learning 會使用 Anaconda 散發套件的 Python，其中包含許多資料處理的一般公用程式。 我們將會自動更新 Anaconda 版本。 目前的版本為：
 -  Anaconda 4.5+、windows 散發套件的 Python 3.6 

預先安裝的套件如下：
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 若要安裝其他套件不在預先安裝的清單中，例如*scikit-learn 其他*，將下列程式碼新增至您的指令碼： 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>使用方式

**執行 Python 指令碼**模組包含您可以使用做為起點的範例 Python 程式碼。 若要設定**執行 Python 指令碼**模組，提供輸入和 Python 程式碼中執行的一組**Python 指令碼**文字方塊。

1. 新增**執行 Python 指令碼**模組至您的實驗。

2. 加上連接**Dataset1**介面，您想要用於輸入的任何資料集。 參考您的 Python 指令碼，做為此資料集**DataFrame1**。

    如果您想要產生使用 Python 的資料，或使用 Python 程式碼來直接將模組匯入資料，資料集的使用是選擇性的。

    這個模組支援的第二個資料集上**Dataset2**。 為 DataFrame2 參考 Python 指令碼中的第二個資料集。

    資料集儲存在 Azure Machine Learning 會自動轉換成**pandas**時載入此模組的資料框架。

    ![執行 Python 輸入的對應](media/module/python-module.png)

4. 若要加入新的 Python 套件或程式碼，加入 壓縮的檔案，包含要在這些自訂的資源**指令碼組合**。 若要輸入**指令碼組合**必須壓縮的檔案已上傳至您的工作區。 

    上傳的 zip 壓縮封存中所包含的任何檔案可以用於實驗執行。 如果封存包含目錄結構，會保留結構，但您必須在前面加上名為的目錄**src**的路徑。

5. 在 [ **Python 指令碼**] 文字方塊中，輸入或貼上有效的 Python 指令碼。

    **Python 指令碼**文字方塊會預先填入一些註解和資料存取和輸出的範例程式碼中的指示。 **您必須編輯或取代此程式碼。** 請務必遵循有關縮排和大小寫的 Python 慣例。

    + 此指令碼必須包含名為函式`azureml_main`此模組的進入點。
    + 進入點函式可以包含最多兩個輸入引數：`Param<dataframe1>`和 `Param<dataframe2>`
    + 解壓縮及儲存在目錄中，連接到第三個輸入連接埠的 zip 的檔案`.\Script Bundle`，這也會新增至 Python `sys.path`。 

    因此，如果您的 zip 檔案包含`mymodule.py`，它使用匯入`import mymodule`。

    + 可傳回兩個資料集的介面，必須是類型的序列`pandas.DataFrame`。 您可以在 您的 Python 程式碼中建立其他輸出，並將它們寫入直接寫入 Azure 儲存體。

6. 執行實驗，或選取的模組，然後按一下**執行所選**執行只是 Python 指令碼。

    所有資料和程式碼載入到虛擬機器，並使用指定的 Python 環境執行。

## <a name="results"></a>結果

必須為 pandas 提供內嵌的 Python 程式碼所執行的任何計算的結果。資料框架，好讓您可以使用與其他模組在實驗中的結果會自動轉換為 Azure Machine Learning 資料集格式。

此模組會傳回兩個資料集：  
  
+ **結果資料集 1**第一個傳回的 pandas 資料框架，在 Python 指令碼中所定義

+ **產生資料集 2**第二個傳回的 pandas 資料框架，在 Python 指令碼中所定義


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 