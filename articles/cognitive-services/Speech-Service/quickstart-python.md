---
title: 快速入門：使用語音服務 SDK 以 Python 辨識語音
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音服務 SDK 以 Python 辨識語音
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723904"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>快速入門：從 Python 使用語音服務

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文說明如何透過適用於 Python 的語音 SDK 來使用語音服務。 它會說明如何從麥克風輸入辨識語音。

## <a name="prerequisites"></a>必要條件

開始之前，以下為必要條件清單：

* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* [Python 3.5 (64 位元)](https://www.python.org/downloads/) \(英文\) 或更新版本。
* Python 語音 SDK 套件可供 Windows (x64)、Mac (macOS X 10.12 版或更新版本)，以及 Linux (Ubuntu 16.04 或 18.04 x64 版) 使用。
* 在 Ubuntu 上，執行下列命令來安裝必要的套件：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* 在 Windows 上，您也會需要適用於您平台的[適用於 Visual Studio 2017 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。

## <a name="get-the-speech-sdk-python-package"></a>取得語音 SDK Python 套件

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

可透過在命令列上使用下列命令來從 [PyPI](https://pypi.org/) \(英文\) 安裝認知服務語音 SDK Python 套件：

```sh
pip install azure-cognitiveservices-speech
```

認知服務語音 SDK 目前的版本為 `1.2.0`。

## <a name="support-and-updates"></a>支援及更新

針對語音 SDK Python 套件的更新將會透過 PyPI 散發，並在[版本資訊](./releasenotes.md)頁面上宣佈。
如果有新版本可用，您可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令來更新至該版本。
您可以檢查 `azure.cognitiveservices.speech.__version__` 變數來確認目前所安裝的版本。

如果您遇到問題或遺失某個功能，請查看我們的[支援頁面](./support.md)。

## <a name="create-a-python-application-using-the-speech-sdk"></a>使用語音 SDK 建立 Python 應用程式

### <a name="running-the-sample-in-a-terminal"></a>在終端機中執行範例

您可以將本快速入門中的[程式碼](#quickstart-code)複製到原始程式檔 `quickstart.py`，並在您的 IDE 或主控台中執行它

```sh
python quickstart.py
```

或是從[認知服務語音範例存放庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) \(英文\) 將本快速入門教學課程下載為 [Jupyter](https://jupyter.org) \(英文\) 筆記本，並以筆記本的形式執行它。

### <a name="quickstart-code"></a>快速入門程式碼

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>安裝語音 SDK Python 套件並在 Visual Studio Code 中執行範例

1. 在您的電腦上[下載](https://www.python.org/downloads/) \(英文\) 並安裝 64 位元版本 (3.5 或更新版本) 的 Python。
1. [下載](https://code.visualstudio.com/Download)並安裝 Visual Studio Code。
1. 開啟 Visual Studio Code，從功能表選取 [檔案] > [喜好設定] > [延伸模組] 並搜尋 "Python" 來安裝 Python 延伸模組。
   ![安裝 [Python] 延伸模組](media/sdk/qs-python-vscode-python-extension.png)
1. 建立用來儲存專案的資料夾，例如使用 Windows 檔案總管。
1. 在 Visual Studio Code 中，按一下 [檔案] 圖示，然後開啟您所建立的資料夾。
   ![開啟資料夾](media/sdk/qs-python-vscode-python-open-folder.png)
1. 按一下 [新增檔案] 圖示，以建立新的 Python 來源檔案 `speechsdk.py`。
   ![建立檔案](media/sdk/qs-python-vscode-python-newfile.png)
1. 將 [Python 程式碼](#quickstart-code)複製並貼上到新建立的檔案中，然後儲存它。
1. 插入您的語音服務訂用帳戶資訊。
1. 如果已經選取 Python 解譯器，系統將會在視窗底部狀態列的左側顯示它。
   否則，您可以開啟 [命令選擇區] (`Ctrl+Shift+P`) 並輸入 **Python:Select Interpreter** 來顯示可用的 Python 解譯器清單，然後選擇適當的解譯器。
1. 如果尚未安裝適用於所選取 Python 解譯器的語音 SDK Python 套件，您可以輕鬆地從 Visual Studio Code 內完成此動作。
   若要安裝語音 SDK 套件，請再次開啟 [命令選擇區] (`Ctrl+Shift+P`)，然後輸入 **Terminal:Create New Integrated Terminal** 來開啟終端機。
   在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech`，或是適用於您系統的適當命令。
1. 若要執行範例程式碼，請以滑鼠右鍵按一下編輯器內的任何一個地方，然後選取 [在終端機中執行 Python 檔案]。
   在系統出現提示時說出幾個字，轉譯的文字應該很快就會顯示。
   ![執行範例](media/sdk/qs-python-vscode-python-run.png)

如果您在遵循這些指示時遇到問題，請參閱更加詳盡的 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial) \(英文\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Python 範例](https://aka.ms/csspeech/samples) \(英文\)
