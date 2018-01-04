---
title: "Azure Container Instances 教學課程 - 準備您的應用程式"
description: "Azure 容器執行個體教學課程的第 1 部分 3-準備應用程式部署至 Azure 容器執行個體"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: fc16be80e776d1472be775fa32354ba157d16545
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>建立容器以部署至 Azure Container Instances

Azure Container Instances 能夠將 Docker 容器部署至 Azure 基礎結構，而不需要佈建任何虛擬機器，或採用較高層級的任何服務。 在本教學課程中，您將以 Node.js 建置小型 Web 應用程式，然後封裝在可使用 Azure Container Instances 來執行的容器中。

在本文中，第一個序列，您：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式原始程式碼
> * 從應用程式來源中建立的容器映像
> * 在本機的 Docker 環境中測試之映像

在後續教學課程中，您會將映像上傳至 Azure Container Registry，然後部署至 Azure Container Instances。

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 版本 2.0.23 或更新版本。 執行 `az --version` 以尋找版本。 如果您要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

本教學課程假設使用者對核心 Docker 概念有基本認識，例如容器、容器映像和基本 `docker` 命令。 如有需要請參閱[開始使用 Docker] [ docker-get-started]為容器的基本概念的入門。

若要完成本教學課程中，您必須安裝在本機上的 Docker 開發環境。 Docker 提供套件，輕鬆地在任何設定 Docker [Mac][docker-mac]， [Windows][docker-windows]，或[Linux] [ docker-linux]系統。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 您必須完成本教學課程在本機電腦上安裝 Azure CLI 和 Docker 的開發環境。

## <a name="get-application-code"></a>取得應用程式程式碼

在本教學課程範例包含簡單的 web 應用程式內建[Node.js][nodejs]。 應用程式提供一個 HTML 靜態網頁，外觀如下所示：

![在瀏覽器中顯示的教學課程應用程式][aci-tutorial-app]

使用 git 來下載範例：

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>建置容器映像

範例儲存庫中提供的 Dockerfile 會示範如何建置容器。 它會從開始[官方 Node.js 映像][ docker-hub-nodeimage]根據[Alpine Linux][alpine-linux]，很適合用於小型發佈容器。 接著會將應用程式檔案複製到容器、使用節點封裝管理員來安裝相依性，最後就啟動應用程式。

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

使用[docker 建置][ docker-build]命令以建立容器映像，標記成*aci 教學課程應用*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

從輸出[docker 建置][ docker-build]命令會類似下列 （截斷來提高可讀性）：

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

使用[docker 映像][ docker-images]命令以查看建立映像：

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>在本機執行容器

在嘗試將容器部署至 Container Instances 之前，請先在本機執行，以確認可以運作。 `-d` 參數可讓容器在背景中執行，而 `-p` 可讓您將電腦上的任意連接埠對應至容器中的連接埠 80。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

開啟瀏覽器來移至 http://localhost:8080/，以確認容器正在執行。

![在本機瀏覽器中執行應用程式][aci-tutorial-app-local]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立可部署至 Azure Container Instances 的容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源
> * 從應用程式來源建立容器映像
> * 在本機測試容器

前往下一個教學課程，了解如何在 Azure Container Registry 中儲存容器映像。

> [!div class="nextstepaction"]
> [將映像推送至 Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
