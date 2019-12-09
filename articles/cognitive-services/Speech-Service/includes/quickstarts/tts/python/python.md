---
title: 快速入門：合成語音，Python - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 以 Python 合成語音
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 990e795d0331df41ff098ed498508d6ecfd58f0e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818476"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md)
> * [建立空的範例專案](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

或者，也可以從[語音 SDK 範例存放庫](https://aka.ms/csspeech/samples) \(英文\) 將本快速入門教學課程下載為 [Jupyter](https://jupyter.org) \(英文\) 筆記本，並以筆記本的形式執行它。

### <a name="sample-code"></a>範例程式碼

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>透過 Visual Studio Code 安裝及使用語音 SDK

1. 在您的電腦上下載並安裝 64 位元版本的 [Python](https://www.python.org/downloads/) \(英文\) (3.5 或更新版本)。
1. 下載並安裝 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 開啟 Visual Studio Code，然後安裝 Python 擴充功能。 從功能表選取 [檔案]   > [喜好設定]   > [擴充功能]  。 搜尋 **Python**。

   ![安裝 Python 擴充功能](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. 建立資料夾來儲存專案。 例如，使用 Windows 檔案總管。
1. 在 Visual Studio Code 中選取 [檔案]  圖示。 然後開啟您所建立的資料夾。

   ![開啟資料夾](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 選取 [新增檔案] 圖示，以建立新的 Python 來源檔案 `speechsdk.py`。

   ![建立檔案](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 將 [Python 程式碼](#sample-code)複製並貼上到新建立的檔案中，然後儲存它。
1. 插入您的語音服務訂用帳戶資訊。
1. 如果已經選取，Python 解譯器會顯示在視窗底部狀態列的左側。
   否則，會顯示可用 Python 直譯器的清單。 開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **Python:Select Interpreter**。 選擇適當的解譯器。
1. 您可以從 Visual Studio Code 內安裝語音 SDK Python 套件。 如果您選取的 Python 直譯器尚未安裝該套件，請按照以下步驟安裝。
   若要安裝語音 SDK 套件，請開啟終端機。 再次開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **Terminal:Create New Integrated Terminal** 來開啟終端機。
   在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech` 或系統所適用的命令。
1. 若要執行範例程式碼，請在編輯器內任意地方按一下滑鼠右鍵。 選取 [在終端機中執行 Python 檔案]  。
   出現提示時，請輸入一些文字。 合成的音訊很快就會播放。

   ![執行範例](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

如果您在遵循這些指示時遇到問題，請參閱更加詳盡的 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial) \(英文\)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
