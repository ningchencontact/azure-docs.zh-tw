---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 5ff41442d32739763332fa9bab59543ea3c7804a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129241"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>準備程式碼以進行 Docker 和 Kubernetes 開發
目前為止，您具有可以在本機執行的基本 Web 應用程式。 您現在會藉由建立資產 (定義應用程式的容器) 並將其部署到 Kubernetes 的方式，將應用程式容器化。 使用 Azure Dev Spaces 可以輕鬆完成此工作： 

1. 啟動 VS Code 並開啟 `webfrontend` 資料夾。 (您可以略過要新增偵錯資產或還原專案的任何預設提示。)
1. 在 VS Code 中開啟整合式終端機 (使用 [檢視 > 整合式終端機] 功能表)。
1. 執行這個命令 (請確定您目前的資料夾是 **webfrontend**)：

    ```cmd
    azds prep --public
    ```

Azure CLI 的 `azds prep` 命令會產生具有預設設定的 Docker 和 Kubernetes 資產：
* `./Dockerfile` 描述了應用程式的容器映像，以及如何建置原始程式碼和在容器內執行。
* `./charts/webfrontend` 底下的 [Helm 圖表](https://docs.helm.sh)會說明如何將容器部署至 Kubernetes。

現在不需要了解這些檔案的完整內容。 但是依然很值得指出，**從開發環境到生產環境都可以使用相同的 Kubernetes 和 Docker 組態即程式碼資產，而為不同的環境之間提供更佳的一致性。**
 
`prep` 命令也會產生名為 `./azds.yaml` 的檔案，該檔案是 Azure Dev Spaces 的組態檔。 它可以透過在 Azure 中實現反覆式開發法體驗的其他組態，來補足 Docker 和 Kubernetes 成品。
