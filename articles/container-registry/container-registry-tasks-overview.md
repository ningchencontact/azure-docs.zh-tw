---
title: ACR 工作概觀
description: An introduction to ACR Tasks, a suite of features in Azure Container Registry that provides secure, automated container image build, management, and patching in the cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456153"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automate container image builds and maintenance with ACR Tasks

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 What remains, however, is the need to address how this application virtualization is managed and patched over the container lifecycle.

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」是 Azure Container Registry 內的一套功能。 It provides cloud-based container image building for [platforms](#image-platforms) including Linux, Windows, and ARM, and can automate [OS and framework patching](#automate-os-and-framework-patching) for your Docker containers. ACR Tasks not only extends your "inner-loop" development cycle to the cloud with on-demand container image builds, but also enables automated builds triggered by source code updates, updates to a container's base image, or timers. For example, with base image update triggers, you can automate your OS and application framework patching workflow, maintaining secure environments while adhering to the principles of immutable containers.

## <a name="task-scenarios"></a>Task scenarios

ACR Tasks supports several scenarios to build and maintain container images and other artifacts. See the following sections in this article for details.

* **[Quick task](#quick-task)** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. 請思考一下雲端中的 `docker build`、`docker push`。
* **Automatically triggered tasks** - Enable one or more *triggers* to build an image:
  * **[Trigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger on base image update](#automate-os-and-framework-patching)** 
  * **[Trigger on a schedule](#schedule-a-task)** 
* **[Multi-step task](#multi-step-tasks)** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows. 

Each ACR Task has an associated [source code context](#context-locations) - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

Tasks can also take advantage of [run variables](container-registry-tasks-reference-yaml.md#run-variables), so you can reuse task definitions and standardize tags for images and artifacts.

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

Using the familiar `docker build` format, the [az acr build][az-acr-build] command in the Azure CLI takes a [context](#context-locations) (the set of files to build), sends it ACR Tasks and, by default, pushes the built image to its registry upon completion.

For an introduction, see the quickstart to [build and run a container image](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 By executing [az login][az-login] with a [service principal][az-login-service-principal], your CI/CD solution could then issue [az acr build][az-acr-build] commands to kick off image builds.

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

> [!TIP]
> If you want to build and push an image directly from source code, without a Dockerfile, Azure Container Registry provides the [az acr pack build][az-acr-pack-build] command (preview). This tool builds and pushes an image from application source code using [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Trigger task on source code update

Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a Git repository in GitHub or Azure DevOps. For example, configure a build task with the Azure CLI command [az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. When your team updates code in the repository, an ACR Tasks-created webhook triggers a build of the container image defined in the repo. 

ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| 觸發程序 | Enabled by default |
| ------- | ------------------ |
| 認可 | 是 |
| Pull request | 否 |

To configure the trigger, you provide the task a personal access token (PAT) to set the webhook in the GitHub or Azure DevOps repo.

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

「ACR 工作」之所以能夠真正增強您的容器建置工作流程，是因為它能夠偵測基底映像的更新。 When the updated base image is pushed to your registry, or a base image is updated in a public repo such as in Docker Hub, ACR Tasks can automatically build any application images based on it.

容器映像可概括地分類為「基底」映像和「應用程式」映像。 您的基底映像通常包含您的應用程式建置所在的作業系統和應用程式架構，以及其他自訂項目。 These base images are themselves typically based on public upstream images, for example: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], or [Node.js][base-node]. 您有數個應用程式映像可能會共用一個通用基底映像。

當上游維護程式 (例如重要 OS 安全性修補程式) 更新作業系統或應用程式架構映像時，您也必須更新您的基底映像以包含重要修正。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

由於「ACR 工作」會在建置容器映像時動態地探索基底映像相依性，因此它可以偵測到應用程式映像的基底映像何時更新。 「ACR 工作」會接著使用一個預先設定的[建置工作](container-registry-tutorial-base-image-update.md#create-a-task)，為您**自動重建每個應用程式映像**。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

For image builds from a Dockerfile, an ACR task tracks a base image update when the base image is in one of the following locations:

* 執行工作所在的相同 Azure 容器登錄
* 相同區域中的其他 Azure 容器登錄 
* Docker Hub 中的公用存放庫
* Microsoft 容器登錄中的公用存放庫

> [!NOTE]
> * The base image update trigger is enabled by default in an ACR task. 
> * Currently, ACR Tasks only tracks base image updates for application (*runtime*) images. ACR Tasks doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles. 

Learn more about OS and framework patching in the third ACR Tasks tutorial, [Automate image builds on base image update with Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>排程工作

Optionally schedule a task by setting up one or more *timer triggers* when you create or update the task. Scheduling a task is useful for running container workloads on a defined schedule, or running maintenance operations or tests on images pushed regularly to your registry. For details, see [Run an ACR task on a defined schedule](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>多步驟工作

Multi-step tasks provide step-based task definition and execution for building, testing, and patching container images in the cloud. Task steps defined in a [YAML file](container-registry-tasks-reference-yaml.md) specify individual build and push operations for container images or other artifacts. 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. Run the web application test container, which performs tests against the running application container
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>Context locations

下表顯示「ACR 工作」的幾個所支援內容位置範例：

| 內容位置 | 描述 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | GitHub 存放庫之主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子資料夾 | GitHub 存放庫中子資料夾內的檔案。 Example shows combination of a branch and subfolder specification. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps subfolder | Files within a subfolder in an Azure repo. Example shows combination of branch and subfolder specification. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Image platforms

By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the `--platform` tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, `--platform Linux/arm`). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, `--platform Linux/arm64/v8`):

| OS | 架構|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>View task logs

Each task run generates log output that you can inspect to determine whether the task steps ran successfully. If you use the [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run), or [az acr task run](/cli/azure/acr/task#az-acr-task-run) command to trigger the task, log output for the task run is streamed to the console and also stored for later retrieval. When a task is automatically triggered, for example by a source code commit or a base image update, task logs are only stored. View the logs for a task run in the Azure portal, or use the [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) command.

By default, data and logs for task runs in a registry are retained for 30 days and then automatically purged. If you want to archive the data for a task run, enable archiving using the [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) command. The following example enables archiving for the task run *cf11* in registry *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>後續步驟

When you're ready to automate container image builds and maintenance in the cloud, check out the [ACR Tasks tutorial series](container-registry-tutorial-quick-task.md).

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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
