---
title: 執行 Python 機器學習服務指令碼
titleSuffix: Azure Machine Learning Studio
description: 了解如何在 Azure Machine Learning Studio 中使用 Python。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750602"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>在 Azure Machine Learning Studio 中執行 Python 機器學習服務指令碼

Python 是許多資料科學家工具櫃中的寶貴工具。 它用於典型機器學習服務工作流程，包括資料探索、 功能擷取、 模型訓練和驗證和部署的每個階段。

本文說明如何使用執行 Python 指令碼模組，以及在使用您的 Azure Machine Learning Studio 實驗和 web 服務中的 Python 程式碼。

## <a name="using-the-execute-python-script-module"></a>使用執行 Python 指令碼模組

在 Studio 中 Python 的主要介面是透過[執行 Python 指令碼][ execute-python-script]模組。 它接受最多三個輸入，並產生最多兩個輸出，類似於[執行 R 指令碼][ execute-r-script]模組。 透過呼叫的特殊命名的進入點函式的 [參數] 方塊輸入 Python 程式碼`azureml_main`。

![執行 Python 指令碼模組](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模組 [參數] 方塊中的範例 python 程式碼](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>輸入參數

對 Python 模組的輸入都會公開為 Pandas 資料框架。 `azureml_main`函式會接受最多兩個選擇性的 Pandas 資料框架，做為參數。

輸入連接埠和函式參數之間的對應是有位置關係的：

- 第一個連接的輸入連接埠會對應至函式的第一個參數。
- 第二個輸入連接埠 (如果連接) 會對應至函式的第二個參數。
- 第三個輸入是用來[匯入其他的 Python 模組](#import-modules)。

更詳細的輸入連接埠如何對應至參數的語意`azureml_main`函式會如下所示。

![資料表的輸入連接埠組態和產生的 Python 簽章](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>輸出的傳回值

`azureml_main`函式必須傳回單一 Pandas 資料框架中 Python 封裝[順序](https://docs.python.org/2/c-api/sequence.html)tuple、 清單或 NumPy 陣列等。 此序列的第一個項目會傳回模組的第一個輸出連接埠。 用於模組的第二個輸出連接埠[視覺效果](#visualizations)，而且不需要傳回的值。 此配置如下所示。

![對應輸入參數的連接埠，並傳回輸出連接埠](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>輸入和輸出資料類型的翻譯

Studio 資料集不是相同 Panda 資料框架。 如此一來，在 Studio 中的輸入資料集轉換為 Pandas 資料框架和輸出資料框架會轉換回 Studio 資料集。 在此轉換程序中，也會執行下列的翻譯：

 **Python 的資料類型** | **Studio 轉譯程序** |
| --- | --- |
| 字串和數字| 因為轉譯 |
| Pandas 'M' | 轉譯成 「 遺漏值 」 |
| 索引向量 | 不支援 * |
| 非字串資料行名稱 | 呼叫`str`上資料行名稱 |
| 重複的資料行名稱 | 加入數值後置詞：(1)、 (2)，(3)，依此類推。

**在 Python 函式中的所有輸入的資料框架一律具有 64 位元的數值索引從 0 到資料列數目減 1*

## <a id="import-modules"></a>匯入現有的 Python 指令碼模組

用來執行的 Python 後端根據[Anaconda](https://store.continuum.io/cshop/anaconda/)，廣泛用於科學 Python 散發套件。 它隨附將近 200 個最常見的 Python 套件中以資料為中心的工作負載使用。 Studio 目前不支援使用 Pip 或 Conda 來安裝和管理外部程式庫這類的套件管理系統。  如果您發現需要將額外的程式庫，請使用下列案例做為指南。

Studio 實驗中納入現有的 Python 指令碼，是常見的使用案例。 [執行 Python 指令碼][ execute-python-script]模組接受包含第三個輸入連接埠上的 Python 模組的 zip 檔案。 該檔案會由執行架構在執行階段解壓縮，且內容會新增至 Python 解譯器的程式庫路徑。 然後 `azureml_main` 進入點函式可以直接匯入這些模組。 

作為範例，請考量包含簡單 “Hello, World” 函式的 Hello.py 檔案。

![Hello.py 檔案中的 使用者定義函式](./media/execute-python-scripts/figure4.png)

接下來，我們會建立包含 Hello.py 的 Hello.zip 檔案：

![包含使用者定義 Python 程式碼的 zip 檔案](./media/execute-python-scripts/figure5.png)

做為資料集中將 zip 檔案上傳至 Studio。 然後建立並執行使用 Hello.zip 檔案中的 Python 程式碼，是將它附加到第三個輸入連接埠的實驗**執行 Python 指令碼**模組，如下圖所示。

![範例實驗，具有 Hello.zip 做為輸入來執行 Python 指令碼模組](./media/execute-python-scripts/figure6a.png)

![使用者定義 Python 程式碼上傳為 zip 檔案](./media/execute-python-scripts/figure6b.png)

模組輸出顯示 zip 檔案已解除封裝，且函式 `print_hello` 已執行。

![模組輸出顯示使用者定義函式](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>存取 Azure 儲存體 Blob

您可以存取資料儲存在 Azure Blob 儲存體帳戶使用下列步驟：

1. 下載[適用於 Python 的 Azure Blob 儲存體封裝](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)在本機。
1. 做為資料集，zip 檔案上傳至 Studio 工作區。
1. 建立 BlobService 物件與 `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 停用**需要安全傳輸**在您的儲存體**組態**設定 索引標籤

![停用在 Azure 入口網站需要安全傳輸](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>運作 Python 指令碼

當評分實驗中使用的任何[執行 Python 指令碼][execute-python-script]模組發佈為 Web 服務時，系統會呼叫這些模組。 例如下, 圖顯示計分實驗，其中包含用以評估單一 Python 運算式的程式碼。

![Studio 工作區 web 服務](./media/execute-python-scripts/figure3a.png)

![Python Pandas 運算式](./media/execute-python-scripts/python-script-with-python-pandas.png)

從此實驗建立的 web 服務會採取下列動作：

1. 採用 Python 運算式作為輸入 （做為字串）
1. 將 Python 運算式傳送至 Python 解譯器
1. 傳回表格，內含的運算式，評估的結果。

## <a id="visualizations"></a>使用視覺效果

可傳回以 MatplotLib 建立繪圖[執行 Python 指令碼][execute-python-script]。 不過，繪圖不自動重新導向至映像時使用。因此使用者必須明確地儲存任何繪圖為 PNG 檔案。

若要從 MatplotLib 產生映像，您必須採取下列步驟：

1. 切換為"AGG"的後端，從預設 Qt 型轉譯器。
1. 建立新的圖表物件。
1. 取得軸並且產生所有繪圖。
1. 將圖表儲存為 PNG 檔案。

下列映像建立使用 Pandas 中的 scatter_matrix 函式散佈圖矩陣可說明此程序。

![將 MatplotLib 圖表儲存至映像的程式碼](./media/execute-python-scripts/figure-v1-8.png)

![按一下以視覺化方式檢視上執行 Python 指令碼模組，來檢視圖表](./media/execute-python-scripts/figure-v2-9a.png)

![視覺化範例實驗中使用 Python 程式碼的繪圖](./media/execute-python-scripts/figure-v2-9b.png)

可以傳回多個圖表儲存至不同的映像。 Studio 執行階段會挑選所有映像，並將它們串連的視覺效果。

## <a name="advanced-examples"></a>進階範例

在 Studio 中安裝的 Anaconda 環境包含常見的套件，例如 NumPy、 SciPy 和 Scikits-learn。 這些套件可以有效地用於機器學習服務管線中的資料處理。

例如，下列實驗和指令碼說明在 Scikits-learn 來計算資料集的特徵重要性分數的集團學習者使用。 分數可用來執行受監督的功能選取，再饋送至其他模型。

以下 Python 函式是用於根據分數計算重要性分數及將功能排序：

![依分數的功能排名函式](./media/execute-python-scripts/figure8.png)

然後下列實驗會在 Azure Machine Learning Studio 中的 “Pima Indian Diabetes” 資料集計算及傳回功能的重要性分數：

![使用 Python Pima Indian Diabetes 資料集中排名功能進行實驗](./media/execute-python-scripts/figure9a.png)

![執行 Python 指令碼模組的輸出視覺化](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[執行 Python 指令碼][ execute-python-script]模組目前具有下列限制：

### <a name="sandboxed-execution"></a>沙箱化執行

Python 執行階段目前已沙箱化，且不允許以永續方式的網路或本機檔案系統存取權。 所有本機儲存的文件都會被隔離，並且在模組結束時加以刪除。 Python 程式碼無法在其執行的機器上存取大部分的目錄，但目前的目錄及其子目錄例外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺乏複雜的開發和偵錯支援

Python 模組目前不支援 IDE 功能，例如 intellisense 和偵錯。 此外，如果模組在執行階段失敗，則會提供完整的 Python 堆疊追蹤。 但是，它必須在模組的輸出記錄檔中檢視。 我們目前建議您在如 IPython 的環境中開發 Python 指令碼及進行偵錯，然後將程式碼匯入到模組。

### <a name="single-data-frame-output"></a>單一資料框架輸出

Python 進入點是唯一獲得允許的位置，可以將單一資料框架傳回為輸出。 它不是目前無法傳回至 Studio 執行階段的任意 Python 物件，例如直接使用定型的模型。 如同[執行 R 指令碼][execute-r-script] (具有相同的限制)，在許多情況下可以將物件存放至位元組陣列，然後在資料框架內傳回。

### <a name="inability-to-customize-python-installation"></a>無法自訂 Python 安裝

目前，新增自訂 Python 模組的唯一方法是透過稍早所述的 zip 檔案機制。 雖然這是對於小模組可行，就麻煩大模組 （特別是具有原生 Dll 的模組） 或大量的模組。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script