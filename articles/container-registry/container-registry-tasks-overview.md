---
title: 使用 Azure Container Registry 工作 (ACR 工作) 自動建立及修補容器映射
description: ACR 工作簡介, 這是 Azure Container Registry 中的一套功能, 可在雲端中提供安全、自動化的容器映射組建、管理和修補。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 1459b6fc45bb3d875b4869d1dcb4302dec21eb96
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114809"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>使用 ACR 工作自動化容器映射組建和維護

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 不過, 仍然需要解決此應用程式虛擬化如何透過容器生命週期進行管理和修補。

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」是 Azure Container Registry 內的一套功能。 它能提供適用於 Linux、Windows 及 ARM 的雲端式容器映像建置，並可以自動化針對 Docker 容器的 [OS 和架構修補](#automate-os-and-framework-patching)。 「ACR 工作」不僅可透過依需求進行的容器映像建置，將您的「內部迴圈」開發週期延伸至雲端，也可讓您在認可原始程式碼和更新容器基底映像時，自動執行建置。 您可以使用基底映像更新觸發程序，自動進行作業系統和應用程式架構修補工作流程，維護安全的環境，同時還能依循不可變容器的主體。

使用「ACR 工作」以四種方式建置及測試容器映像：

* [快速工作](#quick-task)：在 Azure 中依需求建置及推送容器映像，而無須安裝本機 Docker 引擎。 請思考一下雲端中的 `docker build`、`docker push`。 從本機原始程式碼或 Git 存放庫進行建置。
* [在原始程式碼認可時建置](#automatic-build-on-source-code-commit)：將程式碼認可至 Git 存放庫時，自動觸發容器映像建置。
* [在基底映像更新時建置](#automate-os-and-framework-patching)：當映像的基底映像已更新時，觸發容器映像建置。
* [多步驟](#multi-step-tasks)工作:定義建置映像、以命令形式執行容器並將映像推送至登錄的多步驟工作。 ACR 工作的這項功能可支援隨選工作執行和平行映射組建、測試和推送作業。

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

使用熟悉`docker build`的格式時, Azure CLI 中的[az acr build][az-acr-build]命令會採用*內容*(要建立的一組檔案), 將它傳送 ACR 工作, 而且根據預設, 會在完成時將建立的映射推送至其登錄。

如需簡介, 請參閱在 Azure Container Registry 中[建立和執行容器映射](container-registry-quickstart-task-cli.md)的快速入門。  

下表顯示「ACR 工作」的幾個所支援內容位置範例：

| 內容位置 | 描述 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | GitHub 存放庫之主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子資料夾 | GitHub 存放庫中子資料夾內的檔案。 範例會顯示分支和子資料夾規格的組合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 藉由執行[az login][az-login]與[服務主體][az-login-service-principal], 您的 CI/CD 解決方案就可以發出[az acr build][az-acr-build]命令來啟動映射組建。

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

## <a name="automatic-build-on-source-code-commit"></a>在來源程式碼認可時自動建置

當程式碼認可至 GitHub 或 Azure DevOps 中的 Git 存放庫時, 請使用 ACR 工作自動觸發容器映射組建。 建立工作 (使用 Azure CLI 命令[az acr task][az-acr-task]設定) 可讓您指定 Git 存放庫, 並選擇性地指定分支和 Dockerfile。 當您的團隊將程式碼認可至存放庫時，「ACR 工作」建立的 Webhook 就會觸發存放庫中所定義容器映像的建置。

> [!IMPORTANT]
> 如果您先前已在預覽期間使用 `az acr build-task` 命令建立工作，則必須使用 [az acr task][az-acr-task] 命令重新建立這些工作。

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

「ACR 工作」之所以能夠真正增強您的容器建置工作流程，是因為它能夠偵測基底映像的更新。 當更新的基底映射推送至您的登錄, 或在公用儲存機制 (例如 Docker Hub) 中更新基底映射時, ACR 工作可以根據它自動建立任何應用程式映射。

容器映像可概括地分類為「基底」映像和「應用程式」映像。 您的基底映像通常包含您的應用程式建置所在的作業系統和應用程式架構，以及其他自訂項目。 這些基底映像本身通常是以公用上游映像為基礎，例如：[Alpine Linux][base-alpine]、 [Windows][base-windows]、 [.net][base-dotnet]或[node.js][base-node]。 您有數個應用程式映像可能會共用一個通用基底映像。

當上游維護程式 (例如重要 OS 安全性修補程式) 更新作業系統或應用程式架構映像時，您也必須更新您的基底映像以包含重要修正。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

由於「ACR 工作」會在建置容器映像時動態地探索基底映像相依性，因此它可以偵測到應用程式映像的基底映像何時更新。 「ACR 工作」會接著使用一個預先設定的[建置工作](container-registry-tutorial-base-image-update.md#create-a-task)，為您**自動重建每個應用程式映像**。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

當基底映射位於下列其中一個位置時, ACR 工作會追蹤基底映射更新:

* 執行工作所在的相同 Azure container registry
* 相同區域中的另一個 Azure container registry 
* Docker Hub 中的公用存放庫
* Microsoft Container Registry 中的公用存放庫

在第三個 ACR 工作教學課程中深入瞭解 OS 和架構修補、[使用 Azure Container Registry 工作自動化基底映射更新上的映射組建](container-registry-tutorial-base-image-update.md)。

## <a name="multi-step-tasks"></a>多步驟工作

多步驟工作提供以步驟為基礎的工作定義和執行, 以便在雲端中建立、測試及修補容器映射。 工作步驟會定義個別的容器映像建置和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 執行會針對執行中應用程式容器執行測試的 Web 應用程式測試容器
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="view-task-logs"></a>查看工作記錄

每個工作執行都會產生可供您檢查的記錄輸出, 以判斷工作步驟是否已順利執行。 如果您使用[az acr build](/cli/azure/acr#az-acr-build)、 [az acr run](/cli/azure/acr#az-acr-run)或[az acr task run](/cli/azure/acr/task#az-acr-task-run)命令來觸發工作, 則會將工作執行的記錄輸出串流處理到主控台, 並儲存以供日後抓取。 查看在 Azure 入口網站中執行之工作的記錄, 或使用[az acr task logs](/cli/azure/acr/task#az-acr-task-logs)命令。

從2019年7月開始, 登錄中工作執行的資料和記錄檔預設會保留30天, 然後自動清除。 如果您想要封存工作執行的資料, 請使用[az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run)命令來啟用保存。 下列範例會在登錄*myregistry*中啟用 [工作執行*cf11* ] 的封存。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>後續步驟

當您準備好透過在雲端中建立容器映射來自動執行 OS 和架構修補時, 請參閱三部分[ACR 工作教學課程系列](container-registry-tutorial-quick-task.md)。

您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
