---
title: 使用 Visual Studio Code 在 Python 中建立及部署 Azure Functions
description: 如何使用適用于 Azure Functions 的 Visual Studio Code 擴充功能, 在 Python 中建立無伺服器函式, 並將其部署至 Azure。
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 43fee2ce25e358bbcff915d2fbef96bf4b7c1a0c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233114"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>使用 Visual Studio Code 將 Python 部署至 Azure Functions

在本教學課程中, 您會使用 Visual Studio Code 和 Azure Functions 延伸模組來建立具有 Python 的無伺服器 HTTP 端點, 並同時將連接 (或「系結」) 新增至儲存體。 Azure Functions 在無伺服器環境中執行程式碼, 而不需要布建虛擬機器或發佈 web 應用程式。 Visual Studio Code 的 Azure Functions 擴充功能可透過自動處理許多設定問題, 來大幅簡化使用函式的程式。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 安裝 Azure Functions 擴充功能
> * 建立 HTTP 觸發的函式
> * 本機的調試
> * 同步處理應用程式設定
> * 查看串流記錄
> * 連線到 Azure 儲存體

如果您在本教學課程中的任何步驟遇到問題, 我們很樂意聆聽詳細資料。 使用每個區段結尾處的 [**我遇到問題**] 按鈕來提交詳細的意見反應。

## <a name="prerequisites"></a>必要條件

- [Azure 訂用帳戶](#azure-subscription)。
- [具有 Azure Functions 延伸模組的 Visual Studio Code](#visual-studio-code-python-and-the-azure-functions-extension) 。
- [Azure Functions Core Tools](#azure-functions-core-tools)。

### <a name="azure-subscription"></a>Azure 訂用帳戶

如果您沒有 Azure 訂用帳戶, 請[立即註冊](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)免費的30天帳戶, 其 Azure 點數為 $200, 以試用服務的任何組合。

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code、Python 和 Azure Functions 延伸模組

安裝下列軟體:

- Azure Functions 所需的 Python 3.6. x。 [Python 3.6.8](https://www.python.org/downloads/release/python-368/)是最新的 3.6. x 版。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Python 延伸](https://marketplace.visualstudio.com/items?itemName=ms-python.python)模組, 如[Visual Studio Code python 教學課程-必要條件](https://code.visualstudio.com/docs/python/python-tutorial)所述。
- [Azure Functions 延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)模組。 如需一般資訊, 請造訪[vscode-Azurefunctions GitHub 存放庫](https://github.com/Microsoft/vscode-azurefunctions)。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

遵循[使用 Azure Functions Core Tools](functions-run-local.md#v2)之作業系統的指示。 這些工具本身是以 .NET Core 撰寫, 而核心工具套件最好是使用 node.js 套件管理員 npm 來安裝, 這也是為什麼您需要安裝 .NET Core 和 node.js 的原因, 即使是 Python 程式碼也一樣。 不過, 您可以使用上述檔中所述的「延伸模組配套」來略過 .NET Core 需求。 不論是哪種情況, 您都只需要安裝這些元件一次, Visual Studio Code 會自動提示您安裝任何更新。

### <a name="sign-in-to-azure"></a>登入 Azure

安裝函式擴充功能之後, 請前往**azure 登入 azure 帳戶:函數**瀏覽器中, 選取 [登**入 Azure**], 然後遵循提示。

![透過 Visual Studio Code 登入 Azure](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

登入之後, 請確認您 Azure 訂用帳戶的電子郵件帳戶出現在狀態列中:

![顯示 Azure 帳戶的 Visual Studio Code 狀態列](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

您指派給訂用帳戶的名稱也會出現在**Azure 中:函數**瀏覽器 (下圖中的「主要」):

![顯示訂閱 Visual Studio Code Azure App Service explorer](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> 如果您遇到「**找不到名稱為 [訂用帳戶識別碼] 的訂用**帳戶」錯誤, 這可能是因為您位於 proxy 後方, 而無法連線到 Azure API。 在`HTTP_PROXY`您`HTTPS_PROXY`的終端機中使用您的 proxy 資訊來設定和環境變數:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>驗證必要條件

若要確認是否已安裝所有的 Azure Functions 工具, 請開啟 [Visual Studio Code 命令選擇區 (F1) **], 然後選取 [終端機]:建立新的整合**式終端機命令, 一旦終端機開啟, 請`func`執行下列命令:

![檢查 Azure Functions 核心工具必要條件](media/tutorial-vs-code-serverless-python/check-prereqs.png)

以 Azure Functions 標誌開頭的輸出 (您必須向上滾動輸出) 表示 Azure Functions Core Tools 存在。

如果無法辨識命令, 請確認您安裝 Azure Functions Core Tools 的資料夾已包含在 PATH 環境變數中。 `func`

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>建立函式

1. Azure Functions 的程式碼是在函式_專案_中進行管理, 您會先建立該函式, 然後再建立程式碼。 在**Azure 中:函數**瀏覽器 (使用左側的 Azure 圖示開啟), 選取 [**新增專案**] 命令圖示, 或開啟命令選擇區 (F1), 然後選取 **[Azure Functions:建立新專案]** 。

    ![函數瀏覽器中的 [建立新專案] 按鈕](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. 在下列提示中:

    | Prompt | 值 | 描述 | 
    | --- | --- | --- |
    | 指定專案的資料夾 | 目前開啟的資料夾 | 要在其中建立專案的資料夾。 您可能想要在子資料夾中建立專案。 |
    | 為您的函式應用程式專案選取語言 | **Python** | 用於函式的語言, 可決定用於程式碼的範本。 |
    | 選取您專案第一個函式的範本 | **HTTP 觸發程序** | 每當有對函式端點發出的 HTTP 要求時, 就會執行使用 HTTP 觸發程式的函式。 (Azure Functions 有各種不同的觸發程式。 若要深入瞭解, 請參閱[我可以如何處理函數？](functions-overview.md#what-can-i-do-with-functions))。 |
    | 提供函式名稱 | HttpExample | 名稱會用於包含函式程式碼和設定資料的子資料夾, 同時也會定義 HTTP 端點的名稱。 使用 "HttpExample", 而不是接受預設的 "HTTPTrigger", 以區別函式本身與觸發程式。 |
    | 授權等級 | **Function** | 對函式端點發出的呼叫需要函式[金鑰](functions-bindings-http-webhook.md#authorization-keys)。 |
    | 選取您要如何開啟專案 | **在目前視窗中開啟** | 在 [目前 Visual Studio Code] 視窗中開啟專案。 |

1. 一小段時間之後, 會出現一則訊息, 指出已建立新的專案。 在**Explorer**中, 有為函式建立的子資料夾。 

1. 如果尚未開啟, 請 *\_ \_ \_開啟\_* 包含預設函式程式碼的 .py 檔案:

    [![建立新 Python 函數專案的結果](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > 當您開啟 *\_ \_.py 時, 若 Visual Studio Code 告訴您未選取 python 解譯器, 請開啟命令選擇區 (F1), 然後選取 Python:\_ \_* **選取 [** 解譯器命令], 然後選取本機`.env`資料夾中的虛擬環境 (已建立為專案的一部分)。 環境必須特別以 Python 3.6 x 為基礎, 如先前的[必要條件](#prerequisites)中所述。
    >
    > ![選取以專案建立的虛擬環境](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>檢查程式碼檔案

新建立的_HttpExample_函式子資料夾中有三個檔案:  *\_ \_init\_ \_. .py*包含函式的程式碼, 函式會將函式描述為 Azure函數和*範例 .dat*是範例資料檔案。 如有需要, 您可以刪除*範例 .dat* , 因為它只會顯示您可以將其他檔案新增至子資料夾。

讓我們先看一下函式, 然後再查看 .py 中 *\_ \_ \_ \_* 的程式碼。

### <a name="functionjson"></a>function.json

函數. json 檔案會提供 Azure Functions 端點的必要設定資訊:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

屬性會識別程式碼的啟動檔案, 而且該程式碼必須包含名為`main`的 Python 函式。 `scriptFile` 只要此處指定的檔案包含`main`函式, 您就可以將程式碼組成多個檔案。

`bindings`元素包含兩個物件, 一個用來描述傳入的要求, 另一個則用來描述 HTTP 回應。 針對連入要求`"direction": "in"`(), 函式會回應 HTTP GET 或 POST 要求, 並要求您提供函數金鑰。 回應 (`"direction": "out"`) 是 HTTP 回應, 會傳回從 Python 函式傳回的`main`任何值。

### <a name="__initpy__"></a>\_\_init.py\_\_

當您建立新的函式時, Azure Functions 會在 *\_.py 中\_ \_ \_* 提供預設的 Python 程式碼:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

程式碼的重要部分如下所示:

- 您必須從`func` `azure.functions`匯入; 匯入記錄模組是選擇性的, 但建議使用。
- 所需`main`的 Python 函式`func.request`會接收`req`名為的物件, 並傳回`func.HttpResponse`類型的值。 您可以在 func 中深入瞭解這些物件的功能[。HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python)和[func。HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python)參考。
- `main`然後, 的主體會處理要求並產生回應。 在此情況下, 程式`name`代碼會在 URL 中尋找參數。 失敗時, 它會檢查要求本文是否包含 json (使用`func.HttpRequest.get_json`), 以及 json 是否`name`包含值 (使用`get`所傳回`get_json`的 json 物件的方法)。
- 如果找到名稱, 程式碼會傳回附加名稱的字串 "Hello";否則會傳回錯誤訊息。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>本機的調試

1. 當您建立函式專案時, Visual Studio Code 擴充功能也會在中`.vscode/launch.json`建立啟動設定, 其中包含名為 [**附加至 Python**函式] 的單一設定。 此設定表示您可以直接選取**F5**或使用 Debug explorer 來啟動專案:

    ![顯示功能啟動設定的 Debug explorer](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. 當您啟動偵錯工具時, 系統會開啟一個終端機, 其中顯示來自 Azure Functions 的輸出, 包括可用端點的摘要。 如果您使用 "HttpExample" 以外的名稱, 您的 URL 可能會不同:

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. 使用**Ctrl + 按一下**或**Cmd + 按一下**[Visual Studio Code**輸出**] 視窗中的 URL, 將瀏覽器開啟至該位址, 或啟動瀏覽器並貼上相同的 url。 在任一情況下, 端點都`api/<function_name>`是, 在此`api/HttpExample`案例中為。 不過, 由於該 URL 不包含 name 參數, 因此瀏覽器視窗應該只顯示「請在查詢字串或要求本文中傳遞名稱」, 視程式碼中的該路徑而定。

1. 現在, 請嘗試新增名稱參數給使用, 例如`http://localhost:7071/api/HttpExample?name=VS%20Code`, 瀏覽器視窗應該會顯示訊息 "Hello Visual Studio Code!", 示範您已執行該程式碼路徑。

1. 若要傳遞 JSON 要求本文中的名稱值, 您可以使用與 JSON 內嵌相同的工具:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    或者, 建立包含`{"name":"Visual Studio Code"}`和使用命令`curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`的檔案 (例如*資料. json* )。

1. 若要對函式進行偵錯工具, 請在讀取`name = req.params.get('name')`並對 URL 提出要求的那一行上設定中斷點。 Visual Studio Code 偵錯工具應該會在該行停止, 讓您檢查變數並逐步執行程式碼。 (如需基本偵錯工具的簡短逐步解說, 請參閱[Visual Studio Code 教學課程-設定和執行偵錯工具](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger))。

1. 當您認為您已在本機徹底測試過函式時, 請停止偵錯工具 (使用 [ **Debug**  > ] [停止] [**調試**程式] 功能表命令, 或偵錯工具工具列上的 [**中斷連線]** 命令)。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>部署至 Azure Functions

在這些步驟中, 您會使用函式擴充功能來建立 Azure 中的函式應用程式, 以及其他必要的 Azure 資源。 函式應用程式可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 此外, 它也需要 Azure 儲存體帳戶來進行資料和[主控方案](functions-scale.md#hosting-plan-support)。 所有這些資源都組織在單一資源群組內。

1. 在 Azure：**函數**瀏覽器中, 選取 **部署至函數應用程式** 命令, 或開啟命令選擇區 (F1 **) 並選取 Azure Functions:部署至函數應用程式**命令。 同樣地, 函數應用程式是您的 Python 專案在 Azure 中執行的位置。

    ![部署至函數應用程式命令](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. 出現提示時, 請選取 [**在 azure 中建立新的函數應用程式**], 並提供在 azure 中唯一的名稱 (通常使用您的個人或公司名稱以及其他唯一識別碼; 您可以使用字母、數位和連字號)。 如果您先前已建立函數應用程式, 其名稱會出現在此選項清單中。

1. 延伸模組會執行下列動作, 您可以在 Visual Studio Code 快顯訊息和 [**輸出**] 視窗中觀察此程式 (此程式需要幾分鐘的時間):

    - 使用您所提供的名稱 (移除連字號) 來建立資源群組。
    - 在該資源群組中, 建立儲存體帳戶、主控方案和函數應用程式。 根據預設, 會建立取用[方案](functions-scale.md#consumption-plan)。 若要在專屬方案中執行您的函式, 您需要[使用 advanced create 選項來啟用發佈](functions-develop-vs-code.md)功能。
    - 將程式碼部署至函數應用程式。

    **Azure:[** 函數瀏覽器] 也會顯示進度:

    ![Azure 中的部署進度列指示器:函數瀏覽器](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. 部署完成後, Azure Functions 延伸模組會顯示訊息, 其中包含三個額外動作的按鈕:

    ![指出部署成功的訊息, 含有其他動作](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    針對 [**串流記錄**] 和 [**上傳設定**], 請參閱下一節。 如需**視圖輸出**, 請參閱後面的步驟5。

1. 部署之後, [**輸出**] 視窗也會顯示 Azure 上的公用端點:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    使用此端點來執行您在本機進行的相同測試, 並在要求本文中使用 URL 參數和/或要求與 JSON 資料。 公用端點的結果應符合您稍早測試的本機端點結果。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>串流記錄

記錄串流的支援目前正在開發中, 如 Azure Functions 延伸模組的[問題 589](https://github.com/microsoft/vscode-azurefunctions/issues/589)中所述。 [部署訊息] 快顯視窗中的 [**串流記錄**] 按鈕最後會將 Azure 上的記錄輸出連接到 Visual Studio Code。 您也可以用滑鼠右鍵按一下 [函數] 專案, 然後選取 [**開始串流記錄**] 或 [**停止串流記錄**], 啟動和停止**Azure Functions** explorer 上的記錄資料流程。

不過, 這些命令目前尚未運作。 藉由執行下列命令, 將取代`<app_name>`為您在 Azure 上的函式應用程式名稱, 以在瀏覽器中使用記錄串流:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>將本機設定同步處理至 Azure

[部署訊息] 快顯視窗中的 [**上傳設定**] 按鈕會將您對*本機. 設定 json*檔案所做的任何變更套用至 Azure。 您也可以藉由展開函式專案節點, 以滑鼠右鍵按一下 [**應用程式設定**], 然後選取 **[上傳本機設定**], 在**Azure Functions** explorer 上叫用命令。您也可以使用命令選擇區來選取**Azure Functions:[上傳**本機設定] 命令。

上傳設定會更新任何現有的設定, 並新增在 [*本機] 設定*中定義的任何新設定。 上傳並不會從本機檔案中未列出的 Azure 移除任何設定。 若要移除這些設定, 請展開 [ **Azure Functions** explorer] 中的 [**應用程式設定**] 節點, 以滑鼠右鍵按一下設定, 然後選取 [**刪除設定**]。您也可以直接在 Azure 入口網站上編輯設定。

若要將透過入口網站或透過**Azure Explorer**進行的任何變更套用至*本機的設定 json*檔案, 請以滑鼠右鍵按一下 [**應用程式設定**] 節點, 然後選取 [**下載遠端設定**] 命令。 您也可以使用命令選擇區來選取**Azure Functions:[下載遠端**設定] 命令。

## <a name="add-a-second-function"></a>新增第二個函式

在您的第一個部署之後, 您可以對程式碼進行變更, 例如新增額外的函式, 然後重新部署至相同的函式應用程式。

1. 在 Azure：**函數**瀏覽器中, 選取 **建立**函式 **命令, 或使用 Azure Functions:從命令**選擇區建立函數。 指定函數的下列詳細資料:

    - 範本：HTTP 觸發程序
    - 名稱："DigitsOfPi"
    - 授權層級:匿名

1. 在 [Visual Studio Code 檔案] 中, 您的函式名稱會再次包含名為 *\_ \_ \_ \_.py*、 *function. json*和*範例 .dat*的子資料夾。

1. 取代 .py 的內容 *\_ \_ ,以符合\_下列程式碼, 其會產生包含 PI 值的字串到 URL 中指定的數位數 (此程式碼只會使用 url 參數)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. 因為程式`"methods"`代碼僅支援 HTTP GET, 請修改函式, 讓集合只`"get"`包含 (也就是移除`"post"`)。 整個檔案應會如下所示:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. 選取**F5**或選取 [ **Debug**  > ] [**開始調試**程式] 功能表命令來啟動偵錯工具。 [**輸出**] 視窗現在應該會在您的專案中顯示這兩個端點:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. 在瀏覽器中, 或從捲曲的中, 對`http://localhost:7071/api/DigitsOfPi?digits=125`發出要求並觀察輸出。 (您可能會注意到, 程式碼演算法並不完全精確, 但我們會留下您的改良功能!)當您完成時, 請停止偵錯工具。

1. 使用**Azure 中的 [**部署至函數應用程式**] 來重新部署程式碼:函數**瀏覽器。 若出現提示, 請選取先前建立的函數應用程式。

1. 部署完成之後 (需要幾分鐘的時間!), [**輸出**] 視窗會顯示您可以重複測試的公用端點。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>新增系結以將訊息寫入至 Azure 儲存體

系結可讓您將函式程式碼連接至資源 (例如 Azure 儲存體), 而不需要撰寫任何資料存取程式碼。 系結會在函式 *. json*檔案中定義, 而且可以同時代表輸入和輸出。 函數可以使用多個輸入和輸出系結, 但只能有一個觸發程式。 若要深入了解，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

在本節中, 您會將儲存體系結新增至稍早在本教學課程中建立的 HttpExample 函數。 函式會使用此系結, 透過每個要求將訊息寫入至儲存體。 有問題的儲存體會使用函數應用程式所使用的相同預設儲存體帳戶。 不過, 如果您打算大量使用儲存空間, 您會想要考慮建立個別的帳戶。

1. 開啟 [命令選擇區] 並選取 **[Azure Functions], 將 Azure Functions 專案的遠端設定同步至您的*本機. 設定 json*檔案:下載遠端設定**。 開啟 [*本機. 設定*], 並檢查它是否包含的值`AzureWebJobsStorage`。 該值是儲存體帳戶的連接字串。

1. 在資料夾中, 以滑鼠右鍵按一下 [*函數. json*], 然後選取 [新增系結]: `HttpExample`

    ![在 Visual Studio Code explorer 中新增 binding 命令](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. 在 Visual Studio Code 的後續提示中, 選取或提供下列值:

    | Prompt | 要提供的值 |
    | --- | --- |
    | 設定系結方向 | 出 |
    | 以方向向外選取系結 | Azure 佇列儲存體 |
    | 用來在程式碼中識別此系結的名稱 | msg |
    | 訊息將傳送至其中的佇列 | outqueue |
    | 從 [*本機設定*] 選取 [設定] (要求儲存體連接) | AzureWebJobsStorage |

1. 進行這些選擇之後, 請確認已將下列系結新增至您的*函數 json*檔案:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. 現在您已設定系結, 您可以在函式程式碼中使用它。 同樣地`main` , 新定義的系結會出現在您的程式碼中, 做為 *\_.py \_ \_ \_* 中函式的引數。 例如, 您可以修改 HttpExample 中 *\_的\_ \_ \_.py*檔案, 以符合下列各項, 這會顯示使用`msg`引數來寫入時間戳記訊息, 其名稱使用於邀請. 批註會說明特定的變更:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. 若要在本機測試這些變更, 請選取**F5**或選取 [ **Debug**  > **開始調試**程式] 功能表命令, 以在 Visual Studio Code 中再次啟動偵錯工具。 如同 [**輸出**] 視窗應該會在您的專案中顯示端點。

1. 在瀏覽器中, 造訪 URL `http://localhost:7071/api/HttpExample?name=VS%20Code`以建立對 HttpExample 端點的要求, 也應該將訊息寫入佇列。

1. 若要確認訊息已寫入至 "outqueue" 佇列 (如系結中所命名), 您可以使用下列三種方法的其中一種:

    1. 登入[Azure 入口網站](https://portal.azure.com), 並移至包含函式專案的資源群組。 在該資源群組中, 找出並開啟專案的儲存體帳戶, 然後移至 [**佇列**]。 在該頁面上, 移至 [outqueue], 這應該會顯示所有已記錄的訊息。

    1. 使用與 Visual Studio 整合的 Azure 儲存體總管 (特別是[檢查輸出佇列](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue)一節中[Visual Studio Code Azure 儲存體](functions-add-output-binding-storage-queue-vs-code.md)所述), 開啟並檢查佇列。

    1. 如[查詢儲存體](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)佇列中所述, 使用 Azure CLI 來查詢儲存體佇列。
    
1. 若要在雲端中進行測試, 請使用**Azure 中的 [**部署至函數應用程式**] 來重新部署程式碼:函數**瀏覽器。 若出現提示, 請選取先前建立的函數應用程式。 部署完成之後 (需要幾分鐘的時間!), [**輸出**] 視窗會再次顯示您可以重複測試的公用端點。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>清除資源

您所建立的函數應用程式包含可能產生最低成本的資源 (請參閱[函數定價](https://azure.microsoft.com/pricing/details/functions/))。 若要清除資源, 請在**Azure 中的函數應用程式上按一下滑鼠右鍵:函數**瀏覽器, 然後選取 [**刪除] 函數應用程式**。 您也可以造訪[Azure 入口網站](https://portal.azure.com), 從左側流覽窗格中選取 [**資源群組**], 選取在本教學課程的程式中建立的資源群組, 然後使用 [**刪除資源群組**] 命令。

## <a name="next-steps"></a>後續步驟

恭喜您完成將 Python 程式碼部署到 Azure Functions 的逐步解說! 您現在已經準備好建立更多無伺服器功能。

如先前所述, 您可以造訪 GitHub 存放庫[vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions)以深入瞭解函式擴充功能。 同時也歡迎您的問題與投稿。

閱讀[Azure Functions 總覽](functions-overview.md), 探索您可以使用的不同觸發程式。

若要深入瞭解您可以從 Python 使用的 Azure 服務, 包括資料儲存體以及 AI 和 Machine Learning 服務, 請造訪[Azure Python 開發人員中心](/azure/python/?view=azure-python)。

另外還有其他適用于 Visual Studio Code 的 Azure 擴充功能, 您可能會覺得有用。 只要在 [擴充功能] explorer 中搜尋 "Azure":

![適用于 Visual Studio Code 的 Azure 擴充功能](media/tutorial-vs-code-serverless-python/azure-extensions.png)

一些常用的擴充功能包括:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
