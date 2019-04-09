---
title: 使用 Azure Container Registry 工作 (ACR 工作) 來自動執行 OS 和架構修補
description: 介紹「ACR 工作」，這是 Azure Container Registry 中的一套功能，可在雲端提供安全、自動化的容器映像建置和修補。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b97db09c477a940ca36129316613f5ceb4eb13b1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256187"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>使用 ACR 工作來自動執行 OS 和架構修補

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 不過，還必須解決此應用程式虛擬化修補的方式。

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」是 Azure Container Registry 內的一套功能。 它能提供適用於 Linux、Windows 及 ARM 的雲端式容器映像建置，並可以自動化針對 Docker 容器的 [OS 和架構修補](#automate-os-and-framework-patching)。 「ACR 工作」不僅可透過依需求進行的容器映像建置，將您的「內部迴圈」開發週期延伸至雲端，也可讓您在認可原始程式碼和更新容器基底映像時，自動執行建置。 您可以使用基底映像更新觸發程序，自動進行作業系統和應用程式架構修補工作流程，維護安全的環境，同時還能依循不可變容器的主體。

使用「ACR 工作」以四種方式建置及測試容器映像：

* [快速工作](#quick-task)：在 Azure 中依需求建置及推送容器映像，而無須安裝本機 Docker 引擎。 請思考一下雲端中的 `docker build`、`docker push`。 從本機原始程式碼或 Git 存放庫進行建置。
* [在原始程式碼認可時建置](#automatic-build-on-source-code-commit)：將程式碼認可至 Git 存放庫時，自動觸發容器映像建置。
* [在基底映像更新時建置](#automate-os-and-framework-patching)：當映像的基底映像已更新時，觸發容器映像建置。
* [多步驟工作](#multi-step-tasks):定義建置映像、以命令形式執行容器並將映像推送至登錄的多步驟工作。 ACR 工作支援隨工作執行和平行的映像建置、 測試和推送作業的這項功能。

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

Azure CLI 中的 [az acr build][az-acr-build]命令會使用熟悉的 `docker build` 格式來取得「內容」(要建置的一組檔案)、將它傳送給「ACR 工作」，然後依預設在完成時將所建置的映像推送至其登錄。

如需簡介，請參閱快速入門[建置和執行容器映像](container-registry-quickstart-task-cli.md)Azure Container Registry 中。  

下表顯示「ACR 工作」的幾個所支援內容位置範例：

| 內容位置 | 描述 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | GitHub 存放庫之主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | GitHub 存放庫中的提取要求。 | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| GitHub 子資料夾 | GitHub 存放庫中子資料夾內的檔案。 範例顯示的是指定 PR 和子資料夾的組合。 | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 透過[服務主體][az-login-service-principal]執行 [az login][az-login]，您的 CI/CD 解決方案可接著發出 [az acr build][az-acr-build] 命令來開始進行映像建置。

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

## <a name="automatic-build-on-source-code-commit"></a>在來源程式碼認可時自動建置

您可以使用「ACR 工作」，在將程式碼認可至 Git 存放庫時，自動觸發容器映像建置。 建置工作 (可使用 Azure CLI 命令 [az acr task][az-acr-task] 來設定) 可讓您指定 Git 存放庫，並視需要指定分支和 Dockerfile。 當您的團隊將程式碼認可至存放庫時，「ACR 工作」建立的 Webhook 就會觸發存放庫中所定義容器映像的建置。

> [!IMPORTANT]
> 如果您先前已在預覽期間使用 `az acr build-task` 命令建立工作，則必須使用 [az acr task][az-acr-task] 命令重新建立這些工作。

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

「ACR 工作」之所以能夠真正增強您的容器建置工作流程，是因為它能夠偵測基底映像的更新。 當已更新的基底映像被推送至您的登錄時，「ACR 工作」可根據它自動建置任何應用程式映像。

容器映像可概括地分類為「基底」映像和「應用程式」映像。 您的基底映像通常包含您的應用程式建置所在的作業系統和應用程式架構，以及其他自訂項目。 這些基底映像本身通常是以公用上游映像為基礎，例如：[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet] 或 [Node.js][base-node]。 您有數個應用程式映像可能會共用一個通用基底映像。

當上游維護程式 (例如重要 OS 安全性修補程式) 更新作業系統或應用程式架構映像時，您也必須更新您的基底映像以包含重要修正。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

由於「ACR 工作」會在建置容器映像時動態地探索基底映像相依性，因此它可以偵測到應用程式映像的基底映像何時更新。 「ACR 工作」會接著使用一個預先設定的[建置工作](container-registry-tutorial-base-image-update.md#create-a-task)，為您**自動重建每個應用程式映像**。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

若要了解 OS 和架構修補，請參閱第三個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在基底映像更新時自動執行映像建置](container-registry-tutorial-base-image-update.md)。

> [!NOTE]
> 只有當基底和應用程式映像都位於相同的 Azure 容器登錄，或當基底位於公用 Docker Hub 存放庫時，基底映像更新才會觸發建置。

## <a name="multi-step-tasks"></a>多步驟工作

多重步驟的工作提供的步驟為基礎的工作定義和建置、 測試和修復的容器映像，在雲端中執行。 工作步驟會定義個別的容器映像建置和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 執行會針對執行中應用程式容器執行測試的 Web 應用程式測試容器
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="next-steps"></a>後續步驟

當您準備好自動化 OS 和修補藉由建置您的容器映像，在雲端中的 framework 時，請參閱三段[ACR 工作的教學課程系列](container-registry-tutorial-quick-task.md)。

選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)並[Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)才能使用您的 Azure 容器登錄庫的延伸模組。 提取，並將映像推送至 Azure container registry，或者執行 ACR 工作，全部都在 Visual Studio Code 內。

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
