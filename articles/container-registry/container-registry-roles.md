---
title: Azure Container Registry - 角色和權限
description: Azure 角色型存取控制 (RBAC) 以及身分識別和存取管理 (IAM) 可對 Azure Container Registry 中的資源提供精確的權限。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 793dbf056201a3315a9b77dfebbb9331a8ed7db1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310591"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry 角色和權限

Azure Container Registry 服務可支援一組 Azure 角色，對於 Azure Container Registry 提供不同程度的權限。 Azure[角色型存取控制](../role-based-access-control/index.yml) (RBAC) 可用來將特定權限指派給必須與登錄互動的使用者或服務主體。

| 角色/權限       | [存取 Resource Manager](#access-resource-manager) | [建立/刪除登錄](#create-and-delete-registry) | [推送映像](#push-image) | [提取映像](#pull-image) | [刪除影像資料](#delete-image-data) | [變更原則](#change-policies) |   [簽署映像](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 擁有者 | X | X | X | X | X | X |  |  
| 參與者 | X | X | X |  X | X | X |  |  
| 讀取者 | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>區分使用者和服務

套用權限時，最佳的做法是提供人員或服務完成工作的最低權限。 下列權限集合代表人員或無周邊服務可使用的一組功能。

### <a name="cicd-solutions"></a>CI/CD 解決方案

自動執行 CI/CD 解決方案的 `docker build` 命令時，您需要 `docker push` 功能。 對於這些無周邊服務案例，建議指派 **AcrPush** 角色。 此角色不同於更廣泛**參與者**角色，可防止帳戶執行其他登錄作業或存取 Azure Resource Manager。

### <a name="container-host-nodes"></a>容器主機節點

同樣地，執行容器的節點需要 **AcrPull** 角色，但是應該不需要**讀者**功能。

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 擴充功能

對於 Visual Studio Code [Docker 擴充功能組](https://code.visualstudio.com/docs/azure/docker)之類的工具，需要額外的資源提供者存取權，才能列出可用的 Azure 容器登錄。 在此情況下，請對於使用者提供**讀者**或**參與者**角色的存取權。 這些角色允許 `docker pull`、`docker push`、`az acr list`、`az acr build` 和其他功能。 

## <a name="access-resource-manager"></a>存取 Resource Manager

必須具備 Azure Resource Manager 存取權，才能使用 [Azure CLI](/cli/azure/) 來進行 Azure 入口網站和登錄管理。 例如，若要使用 `az acr list` 命令取得登錄的清單，您需要此權限集合。 

## <a name="create-and-delete-registry"></a>建立和刪除登錄

建立和刪除 Azure 容器登錄的能力。

## <a name="push-image"></a>推送映像

透過 `docker push` 推送映像或將另一個[支援的成品](container-registry-image-formats.md) (例如 Helm 圖表) 推送到登錄的能力。 需要使用授權的身分識別對於登錄進行[驗證](container-registry-authentication.md)。 

## <a name="pull-image"></a>提取映像

透過 `docker pull` 提取非隔離映像或從登錄提取另一個[支援的成品](container-registry-image-formats.md) (例如 Helm 圖表) 的能力。 需要使用授權的身分識別對於登錄進行[驗證](container-registry-authentication.md)。

## <a name="delete-image-data"></a>刪除映像資料

從登錄中[刪除容器映射](container-registry-delete.md), 或刪除其他[支援](container-registry-image-formats.md)的成品 (例如 Helm 圖表) 的功能。

## <a name="change-policies"></a>變更原則

對於登錄設定原則的能力。 原則包括映像清除、啟用隔離和映像簽署。

## <a name="sign-images"></a>簽署映像

簽署映像 (通常指派給自動化程序，以便使用服務主體) 的能力。 此權限通常會結合[推送映像](#push-image)，以便將受信任的映像推送至登錄。 如需詳細資訊，請參閱[Azure Container Registry 中的內容信任](container-registry-content-trust.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解使用[Azure 入口網站](../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具將 RBAC 角色指派至 Azure 的身分識別。

* 深入了解 Azure Container Registry 的[驗證選項](container-registry-authentication.md)。
