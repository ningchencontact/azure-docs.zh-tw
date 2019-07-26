---
title: 教學課程 - 在基底映像更新時自動執行容器映像建置 - Azure Container Registry 工作
description: 在本教學課程中，您將了解如何設定 Azure Container Registry 工作，以在基底映像更新時自動觸發雲端中的容器映像建置。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 496aa065b3b10eac546dbe41f5a2650acc112d29
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310509"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>教學課程：在 Azure Container Registry 中更新基底映像時自動執行容器映像建置 

ACR 工作支援在容器的基底映像更新時自動執行建置，例如，當您在其中一個基底映像中修補作業系統或應用程式架構時。 在本教學課程中，您將了解如何在 ACR 工作中建立工作，以在容器的基底映像已推送至登錄時於雲端中觸發建置。

在本教學課程中 (系列的最後一個)：

> [!div class="checklist"]
> * 建置基底映像
> * 建立應用程式映像建置工作
> * 更新基底映像以觸發應用程式映像工作
> * 顯示已觸發的工作
> * 確認更新的應用程式映像

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您想要在本機使用 Azure CLI，您必須安裝 Azure CLI **2.0.46** 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="prerequisites"></a>必要條件

### <a name="complete-the-previous-tutorials"></a>完成先前的教學課程

本教學課程假設您已完成本系列前兩個教學課程中的步驟，其間您完成了下列作業：

* 建立 Azure Container Registry
* 派生範例存放庫
* 複製範例存放庫
* 建立 GitHub 個人存取權杖

如果您尚未完成前兩個教學課程，請先加以完成，再繼續操作：

[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)

[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>建立環境

請在這些殼層環境變數中填入您的環境適用的值。 此步驟並不是必要動作，但可簡化在本教學課程中執行多行 Azure CLI 命令的作業。 若未填入這些環境變數，則必須在命令範例中的每個對應之處手動取代各個變數。

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>基礎映像

定義大多數容器映像的 Dockerfile，會指定供其作為基礎的父映像，通常稱為其*基底映像*。 基底映像通常包含會套用容器其餘各層的作業系統，例如 [Alpine Linux][base-alpine] or [Windows Nano Server][base-windows]。 此外也可能包含應用程式架構，例如 [Node.js][基底節點] 或 [.NET Core][base-dotnet]。

### <a name="base-image-updates"></a>基底映像更新

基底映像常會由映像維護程式進行更新，以在映像中納入作業系統或架構的新功能或增強功能。 安全性修補程式是基底映像進行更新的另一個常見原因。

基底映像進行更新時，您將需要據以在登錄中重建任何容器映像，以納入新功能和修正程式。 ACR 工作具有在容器的基底映像更新時自動為您建置映像的能力。

### <a name="tasks-triggered-by-a-base-image-update"></a>由基底映像更新觸發的工作

* 目前針對來自 Dockerfile 的映像組建，ACR 工作會偵測相同 Azure Container Registry 中的基底映像、公用 Docker Hub 存放庫或 Microsoft Container Registry 中公用存放庫的相依性。 如果 `FROM` 陳述式所指定的基底映像位於其中一個位置，則 ACR 工作會新增一個 Hook，以確保映像會在其基底有所更新時進行重建。

* 當您使用 [az acr task create][az-acr-task-create] 命令建立 ACR 工作時，工作預設為「啟用」  由基底映像更新觸發。 也就是 `base-image-trigger-enabled` 屬性設定為 True。 如果您想要在工作中停用此行為，請將屬性更新為 False。 例如，執行以下 [az acr task update][az-acr-task-update] 命令：

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* 若要讓 ACR 工作能夠判斷和追蹤容器映像的相依性 (包括其基底映像)，您必須先觸發工作**至少一次**。 例如，使用 [az acr task run][az-acr-task-run] 命令以手動觸發工作。

* 若要在基底映像更新時觸發工作，基底映像必須具有「穩定」  標籤，例如 `node:9-alpine`。 這個標籤通常適用於隨著 OS 和架構修補程式更新到最新穩定版本的基底映像。 如果基底映像是隨著新版本標籤更新，它就不會觸發工作。 如需有關映像標籤的詳細資訊，請參閱[最佳做法指引](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)。 

### <a name="base-image-update-scenario"></a>基底映像更新案例

本教學課程將逐步說明基底映像更新案例。 [程式碼範例][code-sample]包含兩個 Dockerfile：一個應用程式映像，及其指定作為基底的映像。 在以下幾節中，您會建立一項 ACR 工作，以在基底映像的新版本推送至相同容器登錄時自動觸發應用程式映像的建置。

[Dockerfile-app][dockerfile-app]：一個小型 Node.js Web 應用程式，會呈現一個靜態網頁，顯示它所依據的 Node.js 版本。 系統會模擬版本字串：它會顯示基底映像中定義的環境變數 `NODE_VERSION` 的內容。

[Dockerfile-base][dockerfile-base]：`Dockerfile-app` 指定作為其基底的映像。 它本身會以[節點][base-node]映像為基礎，且包含 `NODE_VERSION` 環境變數。

在以下幾節中，您會建立工作、更新基底映像 Dockerfile 中的 `NODE_VERSION` 值，然後使用 ACR 工作來建置基底映像。 當 ACR 工作將新的基底映像推送至您的登錄時，它會自動觸發應用程式映像的建置。 您可以選擇性地在本機執行應用程式容器映像，以查看已建置的映像中不同的版本字串。

在本教學課程中，您的 ACR 工作會建置並推送在 Dockerfile 中指定的應用程式容器映像。 ACR 工作也可執行[多步驟工作](container-registry-tasks-multi-step.md)，使用 YAML 檔案來定義相關步驟，以建置、推送並選擇性地測試多個容器。

## <a name="build-the-base-image"></a>建置基底映像

首先請使用 ACR 工作*快速工作*來建置基底映像。 如本系列的[第一個教學課程](container-registry-tutorial-quick-task.md)所討論的，此程序不僅會建置映像，也會在建置成功時將映像推送至您的容器登錄。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>建立工作

接著，請使用 [az acr task create][az-acr-task-create] 建立工作：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 如果您先前已在預覽期間使用 `az acr build-task` 命令建立工作，則必須使用 [az acr task][az-acr-task] 命令重新建立這些工作。

此工作類似於[上一個教學課程](container-registry-tutorial-build-task.md)中建立的快速工作。 它會指示 ACR 工作在認可推送至 `--context` 所指定的存放庫時觸發映像建置。 在上一個教學課程中用來建置映像的 Dockerfile 會指定公用基底映像 (`FROM node:9-alpine`)，而此工作中的 Dockerfile [Dockerfile-app][dockerfile-app]，則是會指定相同登錄中的基底映像：

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

此組態可以讓稍後在本教學課程中的基底映像模擬架構修補程式變得容易。

## <a name="build-the-application-container"></a>建置應用程式容器

請使用 [az acr task run][az-acr-task-run] 手動觸發工作，並建置應用程式映像。 這個步驟可確保工作會在基底映像上追蹤應用程式映像的相依性。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

工作完成後，如果您想要完成下列選擇性步驟，請記下**回合識別碼** (例如 "da6")。

### <a name="optional-run-application-container-locally"></a>選用：在本機執行應用程式容器

如果您在本機工作 (而不是在 Cloud Shell 中)，且您已安裝 Docker，請先執行容器以檢視在網頁瀏覽器中呈現的應用程式，再重建其基底映像。 如果您使用 Cloud Shell，請略過本節 (Cloud Shell 不支援 `az acr login` 或 `docker run`)。

首先，使用 [az acr login][az-acr-login] 驗證您的容器登錄：

```azurecli
az acr login --name $ACR_NAME
```

現在，請使用 `docker run` 在本機執行容器。 請將 **\<run-id\>** 取代為在上一個步驟的輸出中找到的回合識別碼 (例如 "da6")。 此範例將容器命名為 `myapp`，並且包含 `--rm` 參數以在您停止容器時將其移除。

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在瀏覽器中瀏覽至 `http://localhost:8080`，您應該會看到 Node.js 版本號碼呈現於網頁中，如下所示。 在後續步驟中，您可以在版本字串中加上 "a"，以變更版本。

![呈現在瀏覽器中的範例應用程式的螢幕擷取畫面][base-update-01]

若要停止和移除容器，請執行下列命令：

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>列出組建

接著，使用 [az acr task list-runs][az-acr-task-list-runs] 命令，列出 ACR 工作已為登錄完成的工作回合：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

如果您已完成上一個教學課程 (且未刪除登錄)，您應該會看到如下的輸出。 請記下工作回合數目和最新的回合識別碼，以便在下一節更新基底映像後比較輸出。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>更新基底映像

在此您將模擬基底映像中的架構修補程式。 請編輯 **Dockerfile-base**，並在 `NODE_VERSION` 中定義的版本號碼後面加上 "a"：

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

執行快速工作，以建置經過修改的基底映像。 請記下輸出中的**回合識別碼**。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

當建置完成，且 ACR 工作已將新的基底映像推送至您的登錄後，它會觸發應用程式映像的建置。 您先前建立的工作可能需要一些時間才能觸發應用程式映像建置，因為它必須偵測最新建置並推送的基底映像。

## <a name="list-updated-build"></a>列出更新的組建

現在您已更新基底映像，接下來可以再次列出您的工作回合，將其與先前的清單比較。 若起初輸出並無差異，請定期執行命令，以查看清單中出現的新工作回合。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

輸出大致如下。 最後執行之建置的 TRIGGER 應為 "Image Update"，表示工作是由基底映像的快速工作所起始。

```console
$ az acr task list-builds --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

如果您想要執行下列選擇性步驟以執行新建置的容器，並查看更新的版本號碼，請記下映像更新觸發之組建的 **RUN ID** 值 (在上述輸出中，其值為 "da8")。

### <a name="optional-run-newly-built-image"></a>選用：執行新建置的映像

如果您在本機工作 (而不是在 Cloud Shell 中)，且您已安裝 Docker，請在新的應用程式映像建置完成後執行該映像。 請將 `<run-id>` 取代為您在上一個步驟中取得的 RUN ID。 如果您使用 Cloud Shell，請略過本節 (Cloud Shell 不支援 `docker run`)。

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在瀏覽器中瀏覽至 http://localhost:8081 ，您應該會在網頁中看到更新的 Node.js 版本號碼 (附有 "a")：

![呈現在瀏覽器中的範例應用程式的螢幕擷取畫面][base-update-02]

務必留意的是，您是使用新的版本號碼更新**基底**映像，但最後建置的**應用程式**映像會顯示新版本。 ACR 工作會取用您對基底映像的變更，並自動重建您的應用程式映像。

若要停止和移除容器，請執行下列命令：

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>清除資源

若要移除您在本教學課程系列中建立的所有資源 (包括容器登錄、容器執行個體、金鑰保存庫和服務主體)，請發出下列命令：

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用工作，在映像的基底映像有所更新時自動觸發容器映像建置。 接下來請了解關於容器登錄驗證的資訊。

> [!div class="nextstepaction"]
> [Azure Container Registry 中的驗證](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
