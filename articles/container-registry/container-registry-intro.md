---
title: Azure 中的私人 Docker 容器登錄 - 概觀
description: Azure 容器登錄庫服務的簡介，此服務提供雲端式管理的私人 Docker Registry。
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ba75d196bdb53fab104ab6c01391e762b4a3841b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270518"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure 中的私人 Docker 容器登錄的簡介

Azure 容器登錄是可管理的 [Docker Registry](https://docs.docker.com/registry/) 服務，架構於開放原始碼的 Docker Registry 2.0。 建立及維護 Azure 容器登錄庫，以儲存和管理您的私人 [Docker 容器](https://www.docker.com/what-docker)映像。

使用 Azure 的容器登錄進行現有容器的開發與部署管線，或使用 [ACR 工作](#azure-container-registry-tasks)來建置 Azure 中的容器映像。 視需求建置，否則具有原始程式碼的完全自動組建會認可，且基礎映像更新組建會觸發。

如需 Docker 與容器的相關背景，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) (英文)。

## <a name="use-cases"></a>使用案例

從 Azure 容器登錄庫將映像提取到不同部署目標︰

* **可調整的協調流程系統**，它會管理整個主機叢集上的容器化應用程式，包括 [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/) 與 [Docker Swarm](https://docs.docker.com/swarm/)。
* **Azure 服務**，會支援依規模建置和執行的應用程式，包括 [Azure Kubernetes Service (AKS)](../aks/index.yml)[App Service](../app-service/index.yml)[Batch](../batch/index.yml)[Service Fabric](/azure/service-fabric/) 和其他應用程式。

開發人員也可以將推送到容器登錄庫，當做容器開發工作流程的一部分。 例如，從 [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) \(英文\) 或 [Jenkins](https://jenkins.io/) \(英文\) 等持續整合與部署工具中，將容器登錄設定為目標。

設定 ACR 工作，在應用程式基礎映像更新時，自動重建應用程式映像，或在您的小組將程式碼認可至 Git 存放庫時，自動建置映像。 建立多步驟工作，在雲端中平行地自動建置、測試及修補多個容器映像。

Azure 會提供工具 (例如 Azure 命令列介面、Azure 入口網站和 API 支援) 來管理您的 Azure 容器登錄。 您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

## <a name="key-concepts"></a>重要概念

* **登錄庫** - 在您的 Azure 訂用帳戶中建立一或多個容器登錄庫。 登錄可以三個 SKU 提供：[基本、標準和進階](container-registry-skus.md)，每個 SKU 都支援 Webhook 整合、使用 Azure Active Directory 的登錄驗證，以及刪除功能。 在與您的部署相同的 Azure 位置建立登錄，以利用容器映像接近網路的本機儲存體。 將進階登錄庫的[異地複寫](container-registry-geo-replication.md)功能用於進階複寫和容器映像散發案例。 完整的登錄名稱具有 `myregistry.azurecr.io` 形式。

  您可以使用採用 Azure Active Directory 的[服務主體](../active-directory/develop/app-objects-and-service-principals.md)或提供的管理員帳戶，對容器登錄進行[存取控制](container-registry-authentication.md)。 使用 Azure CLI 或標準 `docker login` 命令登入登錄。

* **存放庫** - 登錄包含一個或多個存放庫，這些存放庫是容器映像的虛擬群組，其名稱相同，但標籤或摘要不同。 Azure 容器登錄庫支援多層級的儲存機制命名空間。 有了多層級命名空間，您可以將與特定應用程式相關的映像集合為群組，或將特定開發或作業團隊的應用程式集合為群組。 例如︰

  * `myregistry.azurecr.io/aspnetcore:1.0.1` 表示全公司的映像
  * `myregistry.azurecr.io/warrantydept/dotnet-build` 表示用來建立 .NET 應用程式的映像，在保固部門之間共用
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` 表示 Web 映像，其屬於保固部門所擁有的客戶提交應用程式群組

* **映像** - 儲存在存放庫中，每個映像是 Docker 相容容器的唯讀快照集。 Azure 容器登錄庫可以包含 Windows 和 Linux 映像。 您可以控制您的所有容器部署的映像名稱。 使用標準 [Docker 命令](https://docs.docker.com/engine/reference/commandline/) 將映像推送到儲存機制，或從儲存機制提取映像。 除了容器映像外，Azure Container Registry 還會儲存[相關的內容格式](container-registry-image-formats.md)，例如 [Helm 圖表](container-registry-helm-repos.md)，以用來將應用程式部署至 Kubernetes。

* **容器** - 容器定義軟體應用程式及其相依性，包裹在完整的檔案系統中，包括程式碼、執行階段、系統工具和程式庫。 根據您從容器登錄庫提取的 Windows 或 Linux 映像，執行 Docker 容器。 在單一電腦上執行的容器共用作業系統核心。 Docker 容器可完全移植到所有主要 Linux 散發版本、macOS 和 Windows。

## <a name="azure-container-registry-tasks"></a>Azure Container Registry 工作

[Azure Container Registry 工作](container-registry-tasks-overview.md) (ACR 工作) 是 Azure Container Registry 內的一組功能，可在 Azure 中提供精簡而有效率的 Docker 容器映像建置。 使用 ACR 工作將 `docker build` 作業卸載至 Azure，讓您的開發內部迴圈延伸到雲端。 設定建置工作以自動化您的容器作業系統與架構修補管線，並在您的小組將程式碼認可至來源控制項時自動建置影像。

[多步驟工作](container-registry-tasks-overview.md#multi-step-tasks)提供適用於在雲端建置、測試及修補容器映像的步驟型工作定義與執行。 工作步驟會定義個別的容器映像建置和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立容器登錄庫](container-registry-get-started-portal.md)
* [使用 Azure CLI 建立容器登錄庫](container-registry-get-started-azure-cli.md)
* [使用 ACR 工作來自動執行 OS 和架構修補](container-registry-tasks-overview.md)
