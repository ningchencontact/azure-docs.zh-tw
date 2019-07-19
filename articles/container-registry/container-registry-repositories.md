---
title: Azure 入口網站中的 Azure Container Registry 存放庫
description: 如何檢視 Azure 入口網站中的 Azure Container Registry 存放庫。
services: container-registry
author: cristy
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: jeconnoc
ms.openlocfilehash: 22f84efee2b3996734e8e38c73d30ba891b745d9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310589"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>檢視 Azure 入口網站中的容器登錄存放庫

Azure Container Registry 可讓您將 Docker 容器映像儲存在存放庫中。 透過將映像儲存在存放庫中，您可以在隔離的環境中儲存映像的群組 (或映像的版本)。 當您將映像推送到登錄時，可以指定這些存放庫，然後在 Azure 入口網站中檢視其內容。

## <a name="prerequisites"></a>先決條件

* **Container registry**:在您的 Azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **DOCKER CLI**:在您的本機電腦上安裝[docker][docker-install] , 以提供 docker 命令列介面。
* **容器映射**:將映射推送至容器登錄。 如需如何發送和提取映像的指引，請參閱[發送和提取映像](container-registry-get-started-docker-cli.md)。

## <a name="view-repositories-in-azure-portal"></a>在 Azure 入口網站中檢視存放庫

您可以在 Azure 入口網站中看到裝載映像以及映像標籤的存放庫清單。

如果您依照[發送和提取映像](container-registry-get-started-docker-cli.md) 中的步驟進行 (而且後來並未刪除該映像)，您的容器登錄中應該會有 Nginx 映像。 該文章中的指示指定您要以 `/samples/nginx` 中的 "samples" 命名空間來標記映像。 做為重新整理程式, 在該文章中指定的[docker push][docker-push]命令為:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 因為 Azure Container Registry 支援這類多層存放庫命名空間，所以您可以將範圍設定為和特定應用程式相關的映像集合，或設定為不同開發或作業團隊的應用程式集合。 若要深入了解容器登錄中的存放庫，請參閱 [Azure 中的私人 Docker 容器登錄](container-registry-intro.md)。

檢視存放庫：

1. 登入 [Azure 入口網站][portal]
1. 選取您已將 Nginx 映像推送到其中的 **Azure Container Registry**
1. 選取 [存放庫]  以查看含有登錄中映像的存放庫清單
1. 選取存放庫以查看該存放庫內的映像標記

例如，如果您已依照[發送和提取映像](container-registry-get-started-docker-cli.md)中的指示來推送 Nginx 映像，您應會看到類似下面的內容：

![入口網站中的存放庫](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>後續步驟

您現在已了解檢視和使用入口網站中存放庫的基本概念，請嘗試以[Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) 叢集來使用 Azure Container Registry。

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
