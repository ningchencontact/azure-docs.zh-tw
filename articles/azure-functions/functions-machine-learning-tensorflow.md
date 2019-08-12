---
title: 教學課程：在 Azure Functions 中使用 Python 和 TensorFlow 來進行機器學習推斷 | Microsoft Docs
description: 本教學課程示範如何在 Azure Functions 中套用 TensorFlow 機器學習模型
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: abc7302ee59103a9cbab156b95a41b77eb95d474
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729155"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>教學課程：在 Azure Functions 中使用 Python 和 TensorFlow 來套用機器學習模型

本文示範 Azure Functions 如何讓您搭配使用 Python 和 TensorFlow 與機器學習模型來根據影像的內容對影像進行分類。

您會在本教學課程中學到： 

> [!div class="checklist"]
> * 初始化本機環境以在 Python 中開發 Azure Functions
> * 將自訂 TensorFlow 機器學習模型匯入至函式應用程式
> * 建置無伺服器 HTTP API 來預測相片是否包含狗或貓
> * 從 Web 應用程式取用 API

![螢幕擷取畫面：已完成的專案](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件 

若要在 Python 中建立 Azure Functions，您必須安裝一些工具。

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>複製教學課程存放庫

若要開始，請開啟終端機，並使用 Git 複製下列存放庫：

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

存放庫包含幾個資料夾。

- start  ：這是教學課程的工作資料夾
- end  ：這是供您參考的最終結果和完整實作
- *resources*：包含機器學習模型和協助程式庫
- frontend  ：會呼叫函式應用程式的網站

## <a name="create-and-activate-a-python-virtual-environment"></a>建立並啟用 Python 虛擬環境

Azure Functions 需要 Python 3.6.x。 您將會建立虛擬環境來確保您會使用所需的 Python 版本。

將目前的工作目錄變更為 start  資料夾。 然後建立並啟用名為 .venv  的虛擬環境。 根據您安裝的 Python，用來建立 Python 3.6 虛擬環境的命令可能會與下列指示不同。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

終端機提示字元前面現在會加上 `(.venv)` 前置詞，來表示您已成功啟用虛擬環境。 確認虛擬環境中的 `python` 確實是 Python 3.6.x。

```console
python --version
```

> [!NOTE]
> 針對本教學課程的其餘部分，請在虛擬環境中執行命令。 如果您需要在終端機重新啟用虛擬環境，請執行作業系統所適用的啟用命令。

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

在 start  資料夾中，使用 Azure Functions Core Tools 來初始化 Python 函式應用程式。

```console
func init --worker-runtime python
```

函式應用程式可以包含一或多個 Azure Functions。 在編輯器中開啟 start  資料夾，並檢查內容。

- [local.settings.json  ](functions-run-local.md#local-settings-file)：包含用於本機開發的應用程式設定
- [host.json  ](functions-host-json.md)：包含 Azure Functions 主機和擴充功能的設定
- [requirements.txt  ](functions-reference-python.md#python-version-and-package-management)：包含此應用程式所需的 Python 套件

## <a name="create-an-http-function"></a>建立 HTTP 函式

應用程式需要單一 HTTP API 端點，以將影像 URL 作為輸入，並傳回影像是否包含狗或貓的預測。

在終端機中，使用 Azure Functions Core Tools 來對名為 classify  的新 HTTP 函式進行 Scaffold 操作。

```console
func new --language python --template HttpTrigger --name classify
```

系統隨即會建立名為 classify  的新資料夾，裡面會包含兩個檔案。

- \_\_init\_\_.py  ：main 函式的檔案
- function.json  ：此檔案會說明函式的觸發程式及其輸入和輸出繫結

### <a name="run-the-function"></a>執行函式

在已啟用 Python 虛擬環境的終端機中，啟動函式應用程式。

```console
func start
```

開啟瀏覽器並瀏覽至下列 URL。 此函式應該會執行並傳回「Hello Azure!」 

```
http://localhost:7071/api/classify?name=Azure
```

使用 `Ctrl-C` 來停止函式應用程式。

## <a name="import-the-tensorflow-model"></a>匯入 TensorFlow 模型

您將會使用預先建置的 TensorFlow 模型，該模型已透過 Azure 自訂視覺服務進行定型，並且會從該服務匯出。

> [!NOTE]
> 如果您想要使用自訂視覺服務的免費層來建置自己的模型，則可遵循[專案存放庫範例中的指示](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)。

此模型由 <REPOSITORY_ROOT>/resources/model  資料夾中的兩個檔案組成：model.db  和 labels.txt  。 請將其複製到 classify  函式的資料夾。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

請務必在上述命令中包含 \*。 確認 classify  現在有包含名為 model.pb  和 labels.txt  的檔案。

## <a name="add-the-helper-functions-and-dependencies"></a>新增協助程式函式和相依性

resources  資料夾內名為 predict.py  的檔案中有一些協助程式函式可使用 TensorFlow 來準備輸入影像和進行預測。 請將此檔案複製到 classify  函式的資料夾。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

確認 classify  現在有包含名為 predict.py  的檔案。

### <a name="install-dependencies"></a>安裝相依性

協助程式庫有一些需要安裝的相依性。 請在編輯器中開啟 start/requirements.txt  ，並在此檔案中新增下列相依性。

```txt
tensorflow
Pillow
requests
```

儲存檔案。

在已啟用虛擬環境的終端機中，於 start  資料夾執行下列命令以安裝相依性。 某些安裝步驟可能需要幾分鐘的時間才能完成。

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>在全域變數中快取模型

在編輯器中開啟 predict.py  ，然後查看靠近檔案頂端的 `_initialize` 函式。 請注意，第一次執行函式時會從磁碟中載入 TensorFlow 模型，並將其儲存至全域變數。 後續執行 `_initialize` 時，則不會再從磁碟載入。 使用這項技術在記憶體中快取模型可提升後續預測的速度。

如需全域變數的詳細資訊，請參閱 [Azure Functions Python 開發人員指南](functions-reference-python.md#global-variables)。

## <a name="update-function-to-run-predictions"></a>更新函式以執行預測

在編輯器中開啟 classify/\_\_init\_\_.py  。 匯入稍早新增至相同資料夾的 predict  程式庫。 將下列 `import` 陳述式新增至檔案中已有的其他匯入底下。

```python
import json
from .predict import predict_image_from_url
```

使用下列程式碼取代函式的範本程式碼。

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

務必儲存變更。

此函式會在名為 `img` 的查詢字串參數中接收影像 URL。 其會從協助程式庫呼叫 `predict_image_from_url`，該協助程式庫會下載影像並使用 TensorFlow 模型傳回預測。 然後，函式會傳回包含結果的 HTTP 回應。

因為 HTTP 端點會由裝載於另一個網域的網頁呼叫，所以 HTTP 回應會包含 `Access-Control-Allow-Origin` 標頭以符合瀏覽器的跨原始資源共用 (CORS) 需求。

> [!NOTE]
> 在生產應用程式中，將 `*` 變更為網頁的特定來源以提高安全性。

### <a name="run-the-function-app"></a>執行函式應用程式

請確定 Python 虛擬環境仍保持啟用，並使用下列命令啟動函式應用程式。

```console
func start
```

在瀏覽器中，開啟此 URL 以使用貓相片的 URL 呼叫您的函式。 確認傳回的是有效的預測結果。

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

讓函式應用程式保持執行。

### <a name="run-the-web-app"></a>執行 Web 應用程式

frontend  資料夾內有一個簡單的 Web 應用程式，其會取用函式應用程式中的 HTTP API。

開啟另一個  終端機，並變更為 frontend  資料夾。 使用 Python 3.6 啟動 HTTP 伺服器。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

在瀏覽器中，瀏覽至終端機內顯示的 HTTP 伺服器 URL。 此時應該會出現 Web 應用程式。 在文字方塊中輸入下列其中一個相片 URL。 您也可以使用可公開存取的貓狗相片 URL。

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

當您按一下 [提交] 時，系統會呼叫函式應用程式，並在該頁面上顯示結果。

## <a name="clean-up-resources"></a>清除資源
本教學課程全都會在機器本機上執行，因此沒有需要清除的 Azure 資源或服務。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure Functions 來建置和自訂 HTTP API，以透過 TensorFlow 模型進行預測。 您也已經了解如何從 Web 應用程式呼叫 API。

不論多麼複雜的 API 都可以使用本教學課程的技術來建置，而且都在 Azure Functions 提供的無伺服器計算模型上執行。

若要將函式應用程式部署至 Azure，請使用 [Azure Functions Core Tools](./functions-run-local.md#publish) 或 [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)。

> [!div class="nextstepaction"]
> [Azure Functions Python 開發人員指南](./functions-reference-python.md)
