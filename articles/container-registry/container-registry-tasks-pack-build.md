---
title: 從應用程式建立 Azure Container Registry 映射
description: 使用 az acr pack build 命令, 從應用程式建立容器映射並推送至 Azure Container Registry, 而不需要使用 Dockerfile。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/22/2019
ms.author: danlep
ms.openlocfilehash: 5100418651e24d74ad747e8c436ffce53c899a92
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68500902"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用雲端原生 Buildpack 從應用程式建立及推送映射

Azure CLI 命令`az acr pack build`會使用[Buildpacks](https://buildpacks.io/)中的[`pack`](https://github.com/buildpack/pack) CLI 工具來建立應用程式, 並將其映射推送至 Azure container registry。 這項功能可讓您從您的應用程式原始程式碼快速地建立容器映射, 而不需要定義 Dockerfile。

您可以使用 Azure Cloud Shell 或本機安裝的 Azure CLI 來執行本文中的範例。 如果您想要在本機使用, 則需要2.0.70 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="use-the-build-command"></a>使用 build 命令

若要使用雲端原生 Buildpacks 來建立及推送容器映射, 請執行[az acr pack build][az-acr-pack-build]命令。 [Az acr build][az-acr-build]命令`az acr pack build`會從 Dockerfile 來源和相關程式碼建立及推送映射, 而您可以直接指定應用程式來源樹狀結構。

當您執行`az acr pack build`時, 請至少指定下列內容:

* 您在其中執行命令的 Azure container registry
* 產生之影像的映射名稱和標記
* ACR 工作的其中一個[支援內容位置](container-registry-tasks-overview.md#quick-task), 例如本機目錄、GitHub 存放庫或遠端 tarball
* Buildpack 產生器映射的名稱, 例如`cloudfoundry/cnb:bionic`。  

`az acr pack build`支援 ACR 工作命令的其他功能, 包括已串流處理並儲存以供日後抓取的[執行變數](container-registry-tasks-reference-yaml.md#run-variables)和工作[執行記錄](container-registry-tasks-overview.md#view-task-logs)。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>範例:使用 Cloud Foundry 產生器建立 node.js 映射

下列範例會使用`cloudfoundry/cnb:bionic`產生器, 從[Azure 範例/nodejs--hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world)存放庫中的 node.js 應用程式建立容器映射:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

此範例會建立`node-app` `1.0`具有標記的映射, 並將其推送至*myregistry*容器登錄。 在這裡, 目標登錄名稱會明確地加到映射名稱前面。 如果未指定, 登錄 URL 就會自動加到映射名稱前面。

命令輸出會顯示建立和推送映射的進度。 

成功建立映射之後, 您可以使用 Docker 執行它 (如果已安裝)。 第一次登入您的登錄:

```azurecli
az acr login --name myregistry
```

執行映射:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

在您`localhost:1337`慣用的瀏覽器中流覽至, 以查看範例 web 應用程式。 按`[Ctrl]+[C]`以停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>範例:使用 Heroku builder 建立 JAVA 映射

下列範例會使用`heroku/buildpacks:18`產生器, 從[buildpack/sample-java 應用](https://github.com/buildpack/sample-java-app)程式存放庫中的 java 應用程式建立容器映射:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

此範例會建立`java-app`以命令的執行識別碼標記的映射, 並將其推送至*myregistry*容器登錄。

`--pull`參數會指定命令提取最新的產生器映射, 這是必要的, 因為 ACR 工作不會快取 Heroku builder 映射。

命令輸出會顯示建立和推送映射的進度。 

成功建立映射之後, 您可以使用 Docker 執行它 (如果已安裝)。 第一次登入您的登錄:

```azurecli
az acr login --name myregistry
```

執行映射, 以*runid*的映射標記取代:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

在您`localhost:8080`慣用的瀏覽器中流覽至, 以查看範例 web 應用程式。 按`[Ctrl]+[C]`以停止容器。


## <a name="next-steps"></a>後續步驟

當您使用`az acr pack build`建立並推送容器映射之後, 就可以將它部署到您選擇的目標, 如同任何映射。 Azure 部署選項包括在[App Service](../app-service/containers/tutorial-custom-docker-image.md)或[Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)之間執行, 以及其他專案。

如需 ACR 工作功能的詳細資訊, 請參閱[使用 ACR 工作自動化容器映射組建和維護](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
