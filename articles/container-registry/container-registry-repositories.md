---
title: "在 Azure 入口網站的 azure 容器登錄中儲存機制"
description: "如何在 Azure 入口網站中檢視 Azure 容器登錄中儲存機制。"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 593972e972207a27d1232fcb0c1bf220ac3a8def
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>在 Azure 入口網站中檢視容器登錄儲存機制

Azure 容器登錄中，可讓您儲存 Docker 容器映像儲存機制中。 將映像儲存在儲存機制中，您可以在隔離的環境中儲存的映像 （或影像的版本） 的群組。 當您將映像推送到您的登錄，並在 Azure 入口網站中檢視其內容時，您可以指定這些儲存機制。

## <a name="prerequisites"></a>必要條件

* **容器登錄中**： 在您的 Azure 訂用帳戶中建立容器登錄中。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **Docker CLI**： 安裝[Docker] [ docker-install]在本機電腦，可以提供您與 Docker 命令列介面。
* **容器映像**： 推入至您容器登錄中的映像。 如需如何發送和提取映像的指引，請參閱[發送和提取映像](container-registry-get-started-docker-cli.md)。

## <a name="view-repositories-in-azure-portal"></a>在 Azure 入口網站中檢視儲存機制

您可以看到主控您的映像，以及映像標籤，在 Azure 入口網站的儲存機制的清單。

如果您遵循的步驟[發送和提取映像](container-registry-get-started-docker-cli.md)（及後續未刪除的映像），您應該在容器登錄中具有 Nginx 映像。 在該文件中的指示可讓您指定您在標記影像的命名空間，而 < 範例 > `/samples/nginx`。 重新整理程式，為[docker push] [ docker-push]指定該發行項中的命令是：

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 由於 Azure 容器登錄中支援這類的多層級的存放庫命名空間，可限制範圍與特定的應用程式或集合的應用程式，以不同的開發或操作團隊的影像集合。 若要深入了解容器登錄中的存放庫，請參閱 [Azure 中的私人 Docker 容器登錄](container-registry-intro.md)。

若要檢視的儲存機制：

1. 登入[Azure 入口網站][portal]
1. 選取**Azure 容器登錄中**推入 Nginx 映像
1. 選取**儲存機制**，查看包含在登錄中的映像儲存機制清單
1. 選取儲存機制，以查看該儲存機制內的映像標記

例如，如果推送 Nginx 映像中的指示[發送和提取映像](container-registry-get-started-docker-cli.md)，您應該會看到類似的項目：

![入口網站中的存放庫](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>後續步驟

您現在知道檢視與使用入口網站中的儲存機制的基本概念，請嘗試使用 Azure 容器登錄中以[Azure 容器服務 (AKS)](../aks/tutorial-kubernetes-prepare-app.md)叢集。

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
