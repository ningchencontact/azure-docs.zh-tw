---
title: 將自我裝載的 Azure API 管理閘道部署到 Docker |Microsoft Docs
description: 瞭解如何將自我裝載的 Azure API 管理閘道部署至 Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768498"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>將 Azure API 管理自我裝載閘道部署至 Docker

本文提供將自我裝載的 Azure API 管理閘道部署到 Docker 環境的步驟。

> [!NOTE]
> 自我裝載閘道功能目前為預覽狀態。 在預覽期間，自我裝載閘道僅適用于開發人員和進階層，不需要額外付費。 開發人員層僅限於單一自我裝載的閘道部署。

## <a name="prerequisites"></a>必要條件

- 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
- 建立 Docker 環境。 [Docker For Desktop](https://www.docker.com/products/docker-desktop)是適用于開發和評估用途的絕佳選項。 如需有關 docker 的所有版本、其功能，以及 Docker 本身的完整檔，請參閱[docker 檔](https://docs.docker.com)。
- [在您的 API 管理實例中布建閘道資源](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 自我裝載閘道會封裝為 x86-64 以 Linux 為基礎的 Docker 容器。

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>將自我裝載閘道部署到 Docker

1. 從 [**設定**] 底下選取 [**閘道**]。
2. 選取您想要部署的閘道資源。
3. 選取 [**部署**]。
4. 請注意，[**權杖**] 文字方塊中的新權杖會使用預設的**到期**和**秘密金鑰**值自動產生。 如有需要，請調整任一或兩者，然後選取 [**產生**] 以建立新的權杖。
4. 請確定已在 [**部署腳本**] 底下選取 [ **Docker** ]。
5. 選取**環境**旁的 [ **env**檔案] 連結以下載檔案。
6. 選取位於 [**執行**] 文字方塊右邊的**複製**圖示，將 Docker 命令儲存到剪貼簿。
7. 將命令貼到 [終端機] （或命令）視窗。 視需要調整埠對應和容器名稱。 請注意，此命令會預期所下載的環境檔案會出現在目前的目錄中。
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 執行命令。 命令會指示您的 Docker 環境執行容器，使用從 Microsoft Container Registry 下載的自我裝載閘道映射，並將容器的 HTTP （8080）和 HTTPS （8081）埠對應至主機上的埠80和443。
9. 執行下列命令來檢查閘道 pod 是否正在執行：
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. 返回 Azure 入口網站，並確認您剛才部署的閘道節點回報狀況良好狀態。

![閘道狀態](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> 使用 <code>console docker container logs <gateway-name></code> 命令來查看自我裝載閘道記錄的快照集。
>
> 使用 <code>docker container logs --help</code> 命令來查看所有記錄檔的查看選項。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)。
* [設定自我裝載閘道的自訂功能變數名稱](api-management-howto-configure-custom-domain-gateway.md)。
