---
title: 使用 VS Code 的 Debug Kusto 查詢語言內嵌 Python-Azure 資料總管
description: 瞭解如何使用 VS Code 來對 Kusto 查詢語言（KQL）內嵌 Python 進行 debug。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822899"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>使用 VS Code 的 Debug Kusto 查詢語言內嵌 Python

Azure 資料總管支援使用[python （）外掛程式](/azure/kusto/query/pythonplugin)來執行內嵌于 Kusto 查詢語言中的 python 程式碼。 外掛程式執行時間裝載于沙箱中，這是一個隔離且安全的 Python 環境。 Python （）外掛程式功能會將 Kusto 查詢語言原生功能延伸為 OSS Python 套件的龐大封存。 此延伸模組可讓您執行先進的演算法，例如機器學習、人工智慧、統計和時間序列，做為查詢的一部分。

Kusto 查詢語言工具不方便用來開發和調試 Python 演算法。 因此，請在您最愛的 Python 整合式開發環境（例如 Jupyter、PyCharm、VS 或 VS Code）上開發演算法。 當演算法完成時，請複製並貼到 KQL 中。 為了改善並簡化此工作流程，Azure 資料總管支援 Kusto Explorer 或 Web UI 用戶端之間的整合，以及用於撰寫和調試 KQL 內嵌 Python 程式碼的 VS Code。 

> [!NOTE]
> 此工作流程只能用來對相對較小的輸入資料表進行調試（最多可達幾 MB）。 因此，您可能需要限制輸入以進行偵錯工具。  如果您需要處理大型資料表，請將它限制為使用 `| take`、`| sample`或 `where rand() < 0.x`進行的偵錯工具。

## <a name="prerequisites"></a>必要條件

1. 安裝 Python [Anaconda 散發](https://www.anaconda.com/distribution/#download-section)套件。 在 [ **Advanced Options**] 中，選取 [**將 Anaconda 新增至我的 PATH 環境變數**]。
2. 安裝 [Visual Studio Code](https://code.visualstudio.com/Download)
3. 安裝[適用于 Visual Studio Code 的 Python 擴充](https://marketplace.visualstudio.com/items?itemName=ms-python.python)功能。

## <a name="run-your-query-in-your-client-application"></a>在您的用戶端應用程式中執行查詢

1. 在您的用戶端應用程式中，在包含內嵌 Python 的查詢前面加上 `set query_python_debug;`
1. 執行查詢。
    * Kusto Explorer： VS Code 會自動以*debug_python .py*腳本啟動。
    * Kusto Web UI： 
        1. 下載並儲存*debug_python .py*、 *df .txt*和*kargs。* 在 [視窗] 中，選取 [**允許**]。 **將檔案儲存**在選取的目錄中。 

            ![Web UI 下載內嵌 python 檔案](media/debug-inline-python/webui-inline-python.png)

        1. 以滑鼠右鍵按一下*debug_python .py* ，然後使用 VS code 開啟。 
        .Py 腳本包含來自 KQL 查詢的內嵌 Python 程式碼，其前面會加上範本程式碼，以從*df*和*kargs*的參數字典初始化輸入資料框架。 *debug_python*    
            
1. 在 VS code 中，啟動 VS code 偵錯工具： **Debug** > **開始調試（F5）** ，選取 [ **Python**設定]。 偵錯工具將會啟動，並自動中斷點以對內嵌程式碼進行錯用。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code 中的內嵌 Python 調試功能如何運作？

1. 查詢會在伺服器中進行剖析和執行，直到達到必要的 `| evaluate python()` 子句為止。
1. 系統會叫用 Python 沙箱，而不是執行程式碼，它會序列化輸入資料表、參數字典和程式碼，然後將它們傳回給用戶端。
1. 這三個物件會儲存在所選目錄（Web UI）或用戶端% TEMP% 目錄（Kusto Explorer）中的三個檔案中（ *df .txt*、 *kargs .txt*和*debug_python. .py* ）。
1. VS code 會啟動，並以*debug_python .py*檔案預先載入，其中包含用來從個別檔案初始化 df 和 kargs 的首碼代碼，後面接著內嵌在 KQL 查詢中的 python 腳本。

## <a name="query-example"></a>查詢範例

1. 在您的用戶端應用程式中執行下列 KQL 查詢：

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    請參閱產生的資料表：

    | x  | 四  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 使用 `set query_python_debug;`在用戶端應用程式中執行相同的 KQL 查詢：

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. 已啟動 VS Code：

    ![啟動 VS code](media/debug-inline-python/launch-vs-code.png)

1. VS Code 在 debug 主控台中進行調試並列印「結果」資料框架：

    ![VS code 偵錯工具](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 沙箱映射與您的本機安裝之間可能有差異。 藉[由查詢外掛程式來檢查特定封裝的沙箱映射](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)。
