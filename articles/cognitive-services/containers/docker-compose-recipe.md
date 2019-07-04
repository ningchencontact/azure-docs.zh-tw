---
title: Docker compose 容器配方
titleSuffix: Azure Cognitive Services
description: 了解如何部署認知服務的多個容器。 此程序會示範如何協調多個 Docker 容器映像，透過 Docker Compose。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445783"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>使用 Docker Compose 的私人網路中的多個容器

了解如何部署認知服務的多個容器。 此程序會示範如何協調多個 Docker 容器映像，透過 Docker Compose。

> [Docker Compose](https://docs.docker.com/compose/)是一種工具可定義及執行多容器 Docker 應用程式。 以 Compose，您可以使用 YAML 檔案來設定您的應用程式服務。 然後，使用單一命令，建立並啟動所有服務，從您的組態。

請在適當時機協調的單一主機電腦上的多個容器映像可以是吸引人。 在本文中，我們會提取的辨識文字服務和表單的辨識器服務在一起。

## <a name="prerequisites"></a>必要條件

此程序需要必須安裝並在本機執行的多個工具。

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* [Docker 引擎](https://www.docker.com/products/docker-engine)，並驗證 Docker CLI 可在主控台視窗中運作。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
  * **電腦視覺**資源 F0 或標準定價層只。
  * **形成辨識器**資源 F0 或標準定價層只。
  * 有 S0 定價層的**認知服務**資源。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[認知服務語音容器要求表單](https://aka.ms/speechcontainerspreview/)要求存取至容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker compose 檔案

YAML 檔案會定義要部署的所有服務。 這些服務都依賴在`DockerFile`或現有的容器映像，在本例中我們將使用兩個預覽映像。 複製並貼上下列 YAML 檔案，然後將它儲存成*docker compose.yaml*。 提供適當_apikey_，_計費_，並_端點 URI_中的值_docker compose.yml_下列檔案。

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 在指定的主機電腦上建立目錄`volumes`節點。 這是必要的因為目錄都必須存在，再嘗試將磁碟區的繫結掛接映像。

## <a name="start-the-configured-docker-compose-services"></a>開始設定的 docker compose 的服務

Docker compose 檔案可允許所有已定義的服務的生命週期; 管理從開始/停止和重新建置服務，檢視服務狀態，以及記錄資料流。 從專案目錄開啟命令列介面 (其中*docker compose.yaml*檔案位於)。

> [!NOTE]
> 若要避免錯誤，請確定主機電腦正在正確共用與磁碟機**Docker 引擎**。 例如，如果*e:\publicpreview*做為目錄中*docker compose.yaml*共用*E 磁碟機*使用 docker。

從命令列介面中，執行下列命令來啟動 （或重新啟動） 定義的所有服務中*docker compose.yaml*:

```console
docker-compose up
```

第一次執行`docker-compose up`命令搭配此組態中， **Docker**將會提取下設定的映像`services`節點-下載/掛接它們：

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

下載映像時，則映像服務已經啟動。

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>請確認服務可用性

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

以下是範例輸出：

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>測試 recognize 文字容器

開啟主機電腦上的瀏覽器並瀏覽至`localhost`使用從指定的連接埠*docker compose.yaml*，例如`http://localhost:5021/swagger/index.html`。 您可以使用此 」 功能，請嘗試来測試 recognize 文字端點的 api。

![辨識文字 Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>測試表單辨識器容器

開啟主機電腦上的瀏覽器並瀏覽至`localhost`使用從指定的連接埠*docker compose.yaml*，例如`http://localhost:5010/swagger/index.html`。 您可以使用此 」 功能，請嘗試来測試表單辨識器端點的 api。

![表單辨識器 Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](../cognitive-services-container-support.md)