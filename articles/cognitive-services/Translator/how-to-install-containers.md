---
title: 安裝和執行容器-翻譯工具文字
titleSuffix: Azure Cognitive Services
description: 在本逐步解說教學課程中，如何下載、安裝及執行翻譯工具文字分析的容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501042"
---
# <a name="install-and-run-translator-text-containers"></a>安裝並執行翻譯工具文字容器

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

容器可讓您在自己的環境中執行翻譯工具文字 Api，而且非常適合特定的安全性和資料控管需求。

## <a name="prerequisites"></a>必要條件

使用翻譯工具文字容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。|

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成並提交[認知翻譯工具文字容器要求表單](https://aka.ms/translatorcontainerform)，以要求容器的存取權。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>主機電腦

主機是 x64 型電腦，其 Linux OS 會執行 Docker 容器。 它可以是您內部部署的電腦，或是在 Azure 中裝載服務的 Docker，例如：

* Azure Kubernetes Service。
* Azure 容器實例。
* 部署到 Azure Stack 的[Kubernetes](https://kubernetes.io/)叢集。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表描述最低和建議的 CPU 核心（至少 2.6 ghz 或更快），以及記憶體（以 gb 為單位），以配置給每個翻譯工具文字容器。

| 容器 | 最小值 | 語言組 |
|-----------|---------|---------------|
| 翻譯文字 | 4核心，4 GB 記憶體 | 4 |

針對每個語言組，建議使用 1 GB 的記憶體。 根據預設，翻譯工具文字容器有3或4種語言組，視您正在執行的 `<image-tag>` 而定。 如需詳細資訊，請參閱[支援的語言和翻譯](#supported-languages-and-translation)。 核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，以做為 `docker run` 命令的一部分使用。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

下列容器存放庫中提供翻譯工具文字的容器映射。 此表格也會對應容器映射標籤及其對應的支援語言。

| 容器 | 影像標記 | 支援的語言 |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`、`zh-CN`、`de-DE` 和 `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`、`fr-FR` 和 `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>翻譯工具文字容器的 Docker pull

若要執行[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令，您必須先存取 container registry。 您可以從 Azure CLI 使用[`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login)命令登入 Azure Container Registry。

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

此命令將會使用對應的 Azure Container Registry 來驗證您目前的使用者。 現在，您可以自由地執行 `docker pull` 命令。

> [!NOTE]
> 根據您所需的語言支援，提供對應的 `<image-tag>`。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. [執行容器](#run-the-container-with-docker-run)。 [ 命令有相關](translator-text-container-config.md#example-docker-run-commands)範例`docker run`可供參考。
1. [查詢容器的轉譯端點](#query-the-containers-translate-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行三個容器的其中一個。 `docker run` 命令的[範例](translator-text-container-config.md#example-docker-run-commands)可供使用。

### <a name="run-container-example-of-docker-run-command"></a>Docker run 命令的執行容器範例

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

此命令：

* 從容器映射執行翻譯工具文字容器
* 配置4個 CPU 核心和 4 gb 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 接受最終使用者合約（EULA）
* 在容器結束之後自動將其移除。 容器映射仍然可以在主機電腦上使用

### <a name="how-to-collect-docker-logs"></a>如何收集 docker 記錄

基於疑難排解目的，從容器的執行中查看 docker 記錄可能會很有用。 首先，使用格式化旗標來執行[docker ps](https://docs.docker.com/engine/reference/commandline/ps/)命令，以限制所有容器所顯示的詳細資料。

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

然後，針對有問題的對應容器，使用 `<Container ID>` 執行[docker logs](https://docs.docker.com/engine/reference/commandline/logs/)命令來查看其記錄。

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

上述的 docker logs 命令會收集過去四小時的「錯誤」記錄檔。

## <a name="supported-languages-and-translation"></a>支援的語言和翻譯

`POST /translate` 方法支援下列語言的轉換，從*英文*移至目的語言，反之亦然。 請注意，雖然您可以使用其中一種語言來移至與英文，但*不能*從*非英文*語言移至另一種非*英文*語言。

> [!NOTE]
> 為了達到最佳品質，取用者應該只傳送每個要求一個句子。

| 語言轉換 | 語言 ISO 轉換 | 影像標籤 |
|--|--|:--|
| 英文： left_right_arrow：中文 | `en-US`： left_right_arrow： `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| 英文： left_right_arrow：俄文 | `en-US`： left_right_arrow： `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| 英文： left_right_arrow：阿拉伯文 | `en-US`： left_right_arrow： `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| 英文： left_right_arrow：德文 | `en-US`： left_right_arrow： `de-DE` | `ar_de_en_ru_zh_1.0.0`和`de_en_es_fr_1.0.0` |
| 英文： left_right_arrow：西班牙文 | `en-US`： left_right_arrow： `es-ES` | `de_en_es_fr_1.0.0` |
| 英文： left_right_arrow：法文 | `en-US`： left_right_arrow： `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> 必須指定 `Eula`，才能執行容器;否則，容器將無法啟動。

## <a name="query-the-containers-translate-endpoint"></a>查詢容器的轉譯端點

容器提供以 REST 為基礎的轉譯端點 API。 此端點的數個範例使用方式如下：

# <a name="curltabcurl"></a>[cURL](#tab/curl)


從您想要的 CLI 執行下列捲曲命令。

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> 如果您在容器準備就緒之前嘗試此捲曲的 POST，您最後會收到「服務暫時無法使用」的回應。 只要等到容器準備就緒，然後再試一次。

下列輸出將會列印到主控台。

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

流覽至 swagger 頁面， http://localhost:5000/swagger/index.html

1. 選取 **/Translate 後**
1. 選取 [立即**試用**]
1. 輸入**From**參數作為 `en-US`
1. 輸入做為 `de-DE` 的**To**參數
1. 將 [ **api 版本**] 參數輸入為 `3.0`
1. 在 [**文字**] 底下，將 `string` 取代為下列 JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. 選取 [**執行**]，產生的翻譯會在**回應主體**中輸出。 您應該會預期類似下列的內容：
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. 啟動 Visual Studio，並建立新的主控台應用程式。
1. 編輯 `*.csproj` 檔案以新增 [`<LangVersion>7.1</LangVersion>`] 節點-這會C#指定7.1。
1. 新增[Newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 套件，版本11.0.2。
1. 在 `Program.cs`中，將所有現有的程式碼取代為下列內容：
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. 按 **F5** 鍵執行程式。
1. 下列輸出將會列印到主控台。
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](translator-text-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已瞭解下載、安裝及執行翻譯工具文字容器的概念和工作流程。 摘要說明：

* 翻譯工具文字提供 Docker 的多個 Linux 容器，並封裝各種語言組。
* 容器映射會從「容器預覽」登錄下載。
* 容器映像在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫翻譯工具文字容器中的作業。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](translator-text-container-config.md)，以取得設定的資訊
* 如要解決與功能相關的問題，請參閱容器的常見問題[（FAQ）](../containers/container-faq.md) 。
