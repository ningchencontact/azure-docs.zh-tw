---
title: 教學課程 - 使用 Azure Container Registry 組建自動執行容器映像建置
description: 在本教學課程中，您將了解如何設定將原始程式碼認可至 Git 存放庫時可在雲端中自動觸發容器映像建置的建置工作。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>教學課程：使用 Azure Container Registry 組建自動執行容器映像建置

除了[快速建置](container-registry-tutorial-quick-build.md)以外，ACR 組建也支援透過*建置工作*的自動化 Docker 容器映像建置。 在本教學課程中，您會使用 Azure CLI 建立建置工作，以在您將原始程式碼認可至 Git 存放庫時在雲端中自動觸發映像建置。

在本教學課程中，系列的第二段：

> [!div class="checklist"]
> * 建立建置工作
> * 測試建置工作
> * 檢視組建狀態
> * 透過程式碼認可觸發建置工作

本教學課程假設您已完成[上一個教學課程](container-registry-tutorial-quick-build.md)中的步驟。 如果您尚未完成上一個教學課程的[必要條件](container-registry-tutorial-quick-build.md#prerequisites)一節中的步驟，請先加以完成，再繼續操作。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您想要在本機使用 Azure CLI，您必須安裝 Azure CLI **2.0.32** 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0][azure-cli]。

## <a name="prerequisites"></a>先決條件

### <a name="get-sample-code"></a>取得範例程式碼

本教學課程假設您已完成[上一個教學課程](container-registry-tutorial-quick-build.md)中的步驟，並已派生和複製範例存放庫。 如果您尚未完成上一個教學課程的[必要條件](container-registry-tutorial-quick-build.md#prerequisites)一節中的步驟，請先加以完成，再繼續操作。

### <a name="container-registry"></a>容器登錄

您的 Azure 訂用帳戶中必須要有 Azure 容器登錄，才能完成本教學課程。 如果您需要登錄，請參閱[上一個教學課程](container-registry-tutorial-quick-build.md)或[快速入門：使用 Azure CLI 建立容器登錄](container-registry-get-started-azure-cli.md)。

## <a name="build-task"></a>建置工作

建置工作會定義自動化建置的屬性，包括容器映像原始程式碼的位置和觸發建置的事件。 建置工作中定義的事件發生時 (例如認可至 Git 存放庫)，ACR 組建即會在雲端中起始容器映像建置。 根據預設，它接著會將已成功建置的映像推送至工作中指定的 Azure 容器登錄。

ACR 組建目前支援下列的建置工作觸發程序：

* 認可至 Git 存放庫
* 基底映像更新

## <a name="create-a-build-task"></a>建立建置工作

在本節中，您會先建立用於 ACR 組建的 GitHub 個人存取權杖 (PAT)。 接著，您會建立建置工作，以在程式碼認可至存放庫的分支時觸發建置。

### <a name="create-a-github-personal-access-token"></a>建立 GitHub 個人存取權杖

為了在認可至 Git 存放庫時觸發建置，ACR 組建需要以個人存取權杖 (PAT) 存取存放庫。 請依照下列步驟在 GitHub 中產生 PAT：

1. 瀏覽至 GitHub 上的 PAT 建立頁面 (https://github.com/settings/tokens/new)
1. 輸入權杖的簡短**說明**，例如「ACR 組建工作示範」
1. 在 **repo** 下，啟用 **repo:status** 和 **public_repo**

   ![GitHub 中的個人存取權杖產生頁面的螢幕擷取畫面][build-task-01-new-token]

1. 選取 [產生權杖] 按鈕 (系統可能會要求您確認密碼)
1. 在**安全的位置**複製並儲存產生的權杖 (當您在下一節定義建置工作時，將會使用此權杖)

   ![GitHub 中已產生的個人存取權杖的螢幕擷取畫面][build-task-02-generated-token]

### <a name="create-the-build-task"></a>建立建置工作

現在，您已完成啟用 ACR 組建以讀取認可狀態以及在存放庫中建立 Webhook 所需的步驟，接下來您可以建立建置工作，以在認可至存放庫時觸發容器映像建置。

首先，請在這些殼層環境變數中填入您的環境適用的值。 這並不是必要動作，但可簡化在本教學課程中執行多行 Azure CLI 命令的作業。 若未填入這些值，則必須範例命令中的每個對應之處手動取代各個值。

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

現在，請執行下列 [az acr build-task create][az-acr-build-task-create] 命令以建立建置工作：

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

此建置工作會指定只要有程式碼認可至 `--context` 所指定之存放庫中的*主要*分支，ACR 組建即會從該分支中的程式碼建置容器映像。 `--image` 引數會針對映像標記的版本部分指定 `{{.Build.ID}}` 的參數化值，以確保建置的映像會與特定的組建相互關聯，並加上唯一標記。

成功執行的 [az acr build-task create][az-acr-build-task-create] 命令會產生如下的輸出：

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>測試建置工作

現在，您已有定義組建的建置工作。 若要測試組建定義，請執行 [az acr build-task run][az-acr-build-task-run] 命令以手動觸發建置：

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

根據預設，`az acr build-task run` 命令會在您執行命令時將記錄輸出串流處理至主控台。 在此處，輸出會顯示組建 **aa2** 已排入佇列並建置。

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>檢視組建狀態

有時候，查看您未以手動方式觸發的現行建置處於何種狀態，將有所幫助。 例如，對藉由原始程式碼認可觸發的建置進行疑難排解時。 在本節中，您會觸發手動建置，但抑制將組建記錄檔串流處理至主控台的預設行為。 然後，您會使用 `az acr build-task logs` 命令監視進行中的建置。

首先，請依照先前的相同方式手動觸發建置，但指定 `--no-logs` 引數以抑制記錄至主控台的行為：

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

接著，使用 `az build-task logs` 命令檢視目前執行之建置的記錄：

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

目前執行之建置的記錄會串流處理至您的主控台，並看起來應類似於下列輸出 (在此顯示截斷內容)：

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>透過認可觸發建置

現在您已藉由手動執行測試了建置工作，接下來請透過原始程式碼的變更自動加以觸發。

首先，請確定您位於[存放庫][sample-repo]的本機複本所在的目錄中：

```azurecli-interactive
cd acr-build-helloworld-node
```

接著，執行下列命令以建立新檔案，然後將其認可並推送至 GitHub 上的存放庫分支：

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

在您執行 `git push` 命令時，系統可能會要求您提供 GitHub 認證。 請提供您的 GitHub 使用者名稱，並輸入您先前為密碼建立的個人存取權杖 (PAT)。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

在您將認可推送至存放庫後，ACR 組建所建立的 Webhook 即會在 Azure Container Registry 中引發並啟動建置。 請顯示目前執行之建置的組建記錄檔，以確認並監視建置進度：

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

輸出會類似於下列內容，顯示目前正在執行 (或最後執行) 的建置：

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>列出建置

若要查看 ACR 組建為您的登錄完成的建置清單，請執行 [az acr build-task list-builds][az-acr-build-task-list-builds] 命令：

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

此命令的輸出應類似於下列內容。 ACR 組建已執行的建置會顯示出來，且最新建置的 [觸發程序] 資料行中會出現「Git 認可」：

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用建置工作，在將原始程式碼認可至 Git 存放庫時於 Azure 中自動觸發容器映像建置。 請進入下一個教學課程，以了解如何建立會在容器映像的基底映像更新時觸發建置的建置工作。

> [!div class="nextstepaction"]
> [在基底映像更新時自動執行建置](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
