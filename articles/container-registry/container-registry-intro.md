---
title: Azure 中的私人 Docker 容器登錄
description: Azure 容器登錄庫服務的簡介，此服務提供雲端式管理的私人 Docker Registry。
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 883da7d1487922f5cd986a67d7a7bded78ea3759
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058075"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure 中的私人 Docker 容器登錄的簡介

Azure 容器登錄是可管理的 [Docker Registry](https://docs.docker.com/registry/) 服務，架構於開放原始碼的 Docker Registry 2.0。 建立及維護 Azure 容器登錄庫，以儲存和管理您的私人 [Docker 容器](https://www.docker.com/what-docker)映像。

將 Azure 中的容器登錄與您現有的容器開發與部署管線搭配使用。 使用 Azure Container Registry Build (ACR Build) 在 Azure 中建置容器映像。 視需求建置，否則具有原始程式碼的完全自動組建會認可，且基礎映像更新組建會觸發。

如需 Docker 與容器的相關背景，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) (英文)。

## <a name="use-cases"></a>使用案例

從 Azure 容器登錄庫將映像提取到不同部署目標︰

* **可調整的協調流程系統**，會管理整個主機叢集上容器化的應用程式，包括 [DC/OS](https://docs.mesosphere.com/)、[Docker Swarm](https://docs.docker.com/swarm/)、 [Kubernetes](http://kubernetes.io/docs/)。
* **Azure 服務**，會支援依規模建置和執行的應用程式，包括 [Azure Kubernetes Service (AKS)](../aks/index.yml)[App Service](/app-service/index.md)[Batch](../batch/index.yml)[Service Fabric](/azure/service-fabric/) 和其他應用程式。

開發人員也可以將推送到容器登錄庫，當做容器開發工作流程的一部分。 例如，以容器登錄庫為目標，並以 [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) 或 [Jenkins](https://jenkins.io/) 等持續整合與部署工具為起點。

設定 [ACR Build](#azure-container-registry-build) 組建工作以便在其基礎映像更新時自動重新建置應用程式映像。 當您的小組將程式碼認可至 Git 存放庫時，使用 ACR Build 自動化映像組建。 *ACR Build 目前為預覽狀態。*

## <a name="key-concepts"></a>重要概念

* **登錄庫** - 在您的 Azure 訂用帳戶中建立一或多個容器登錄庫。 登錄可以三個 SKU 提供：[基本、標準和進階](container-registry-skus.md)，每個 SKU 都支援 Webhook 整合、使用 Azure Active Directory 的登錄驗證，以及刪除功能。 在與您的部署相同的 Azure 位置建立登錄，以利用容器映像接近網路的本機儲存體。 將進階登錄庫的[異地複寫](container-registry-geo-replication.md)功能用於進階複寫和容器映像散發案例。 完整的登錄名稱具有 `myregistry.azurecr.io` 形式。

  使用 Azure Active Directory 支持的[服務主體](../active-directory/active-directory-application-objects.md)或提供的管理員帳戶，[控制](container-registry-authentication.md)對容器登錄庫的存取。 執行標準 `docker login` 命令驗證登錄庫。

* **儲存機制** - 登錄庫會包含一個或多個儲存機制，儲存機制是容器映像的群組。 Azure 容器登錄庫支援多層級的儲存機制命名空間。 有了多層級命名空間，您可以將與特定應用程式相關的映像集合為群組，或將特定開發或作業團隊的應用程式集合為群組。 例如︰

  * `myregistry.azurecr.io/aspnetcore:1.0.1` 表示全公司的映像
  * `myregistry.azurecr.io/warrantydept/dotnet-build` 表示用來建立 .NET 應用程式的映像，在保固部門之間共用
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` 表示 Web 映像，其屬於保固部門所擁有的客戶提交應用程式群組

* **映像** - 儲存在儲存機制中，每個映像是 Docker 容器的唯讀快照。 Azure 容器登錄庫可以包含 Windows 和 Linux 映像。 您可以控制您的所有容器部署的映像名稱。 使用標準 [Docker 命令](https://docs.docker.com/engine/reference/commandline/) 將映像推送到儲存機制，或從儲存機制提取映像。

* **容器** - 容器定義軟體應用程式及其相依性，包裹在完整的檔案系統中，包括程式碼、執行階段、系統工具和程式庫。 根據您從容器登錄庫提取的 Windows 或 Linux 映像，執行 Docker 容器。 在單一電腦上執行的容器共用作業系統核心。 Docker 容器可完全移植到所有主要 Linux 散發版本、macOS 和 Windows。

## <a name="azure-container-registry-build-preview"></a>Azure Container Registry 組建 (預覽)

[Azure Container Registry Build](container-registry-build-overview.md) (ACR Build) 是 Azure Container Registry 內的一組功能，可在 Azure 中提供精簡而有效率的 Docker 容器映像組建。 使用 ACR Build 將 `docker build` 作業卸載至 Azure，讓您的開發內部迴圈擴充至雲端。 設定建置工作以自動化您的容器作業系統與架構修補管線，並在您的小組將程式碼認可至來源控制項時自動建置影像。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立容器登錄庫](container-registry-get-started-portal.md)
* [使用 Azure CLI 建立容器登錄庫](container-registry-get-started-azure-cli.md)
* [使用 ACR Build 自動化作業系統和架構修補](container-registry-build-overview.md) (預覽)
