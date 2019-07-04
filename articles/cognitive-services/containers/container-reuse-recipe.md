---
title: 針對 Docker 容器的配方
titleSuffix: Azure Cognitive Services
description: 您可以使用這些容器的配方，建立可重複使用的認知服務容器。 容器可以建立部分或所有的組態設定，使它們不需要在容器啟動時。 一旦您有這個新的圖層的容器 （設定），且您已在本機測試，您可以儲存在容器登錄中的容器。 當容器啟動時，它只需要目前不會在容器中儲存這些設定。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445814"
---
# <a name="create-containers-for-reuse"></a>建立容器以供重複使用

您可以使用這些容器的配方，建立可重複使用的認知服務容器。 容器可以使用部分或所有的組態設定來建置，使它們_不_容器啟動時所需。

一旦您有這個新的圖層的容器 （設定），且您已在本機測試，您可以儲存在容器登錄中的容器。 當容器啟動時，它只需要目前不會在容器中儲存這些設定。 私用登錄容器提供組態的位置，讓您將這些設定。

## <a name="docker-run-syntax"></a>Docker 執行語法

任何`docker run`本文件中的範例假設 Windows 主控台中的，使用`^`行接續字元。 請考慮下列供自己使用：

* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用的 Windows，以外的作業系統或非 Windows 主控台的主控台，使用正確的主控台/終端機、 資料夾語法掛接，和您的主控台和系統的行接續字元。  認知服務容器為 Linux 作業系統，因為目標掛接使用 Linux 型資料夾語法。
* `docker run` 範例會使用目錄關閉`c:`以避免任何權限衝突，在 Windows 上的磁碟機。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。

## <a name="store-no-configuration-settings-in-image"></a>將任何組態設定儲存在映像

此範例`docker run`針對每個服務的命令要將任何組態設定儲存在容器中。 當您從主控台或登錄的服務啟動容器時，這些組態設定，就必須傳入。 私用登錄容器提供組態的位置，讓您將這些設定。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重複使用的配方： 儲存所有容器的組態設定

若要儲存的所有組態設定，建立`Dockerfile`這些設定。

這種方法的問題：

* 新的容器具有不同的名稱和標記從原始的容器。
* 若要變更這些設定，您必須變更 Dockerfile 的值、 重建映像，並重新發行至您的登錄。
* 如果有人取得您的容器登錄庫或本機主機的存取權，他們才能執行容器，並使用認知服務端點。
* 如果您的認知服務不需要輸入的掛接，不要新增`COPY`幾行，以您的 Dockerfile。

建立 Dockerfile 中，從您要使用現有的認知服務容器，然後使用 Dockerfile 中的 docker 命令，來設定或資訊提取容器需要。

此範例中：

* 設定的計費的端點`{BILLING_ENDPOINT}`從主機的環境索引鍵使用`ENV`。
* 設定計費 API 金鑰，`{ENDPOINT_KEY}`從主應用程式的環境索引鍵使用 ' ENV。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重複使用的配方： 儲存與容器的帳單設定

此範例示範如何從 Dockerfile 建立文字分析的情感容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

建置及執行容器[本機](#how-to-use-container-on-your-local-host)或從您[私用登錄容器](#how-to-add-container-to-private-registry)視。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重複使用的配方： 儲存計費，並掛接與容器的設定

此範例示範如何使用語言理解、 儲存來自 Dockerfile 的計費和模型。

* Language Understanding (LUIS) 模型檔案從主機複製檔案系統使用`COPY`。
* LUIS 容器支援多個模型。 如果所有模型都儲存在相同的資料夾中，所有您需要一個`COPY`陳述式。
* 從模型的輸入目錄的相對父執行 docker 檔案。 如下列範例中，執行`docker build`並`docker run`命令，從相對的父代`/input`。 第一個`/input`上`COPY`命令是主機電腦的目錄。 第二個`/input`是容器的目錄。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

建置及執行容器[本機](#how-to-use-container-on-your-local-host)或從您[私用登錄容器](#how-to-add-container-to-private-registry)視。

## <a name="how-to-use-container-on-your-local-host"></a>如何使用您的本機主機上的容器

若要建置 Docker 檔案，取代`<your-image-name>`映像的新名稱，然後使用：

```console
docker build -t <your-image-name> .
```

若要執行的映像，並將它移除，在容器停止時 (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何將容器新增至私人登錄

請遵循下列步驟來使用 Dockerfile，並將新的映像放在您私人容器登錄。  

1. 建立`Dockerfile`使用中重複使用的配方的文字。 A`Dockerfile`沒有延伸模組。

1. 以您自己的值，取代角括號中的任何值。

1. 建立檔案的映像中的命令列或終端機中，使用下列命令。 角括號中的值取代`<>`使用您自己的容器名稱和標記。  

    [標記] 選項， `-t`，可新增您已變更之容器的相關資訊。 例如，容器名稱的`modified-LUIS`指出原始容器有已分層。 標記名稱`with-billing-and-model`表示已經過修改的 Language Understanding (LUIS) 容器的方式。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 從主控台登入 Azure CLI。 此命令會開啟瀏覽器，並需要驗證。 驗證之後，您可以關閉瀏覽器，然後在主控台中繼續工作。

    ```azure-cli
    az login
    ```

1. 從主控台登入您的私人登錄使用 Azure CLI。

    角括號中的值取代`<my-registry>`，以登錄名稱。  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    如果您獲指派服務主體，您也可以登入 docker 登入。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 標記的私用登錄位置的容器。 角括號中的值取代`<my-registry>`，以登錄名稱。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果您不使用標籤名稱，`latest`隱含的。

1. 將新的映像推送至私人容器登錄。 當您檢視您的私人容器登錄時，使用下列 CLI 命令中的容器名稱會儲存機制的名稱。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立和使用 Azure 容器執行個體](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->