---
title: Azure CLI 指令碼範例 - 建立應用程式並從 Azure Repos 持續部署 | Microsoft Docs
description: Azure CLI 指令碼範例 - 建立可從 Azure Repos 持續部署的應用程式
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: jeconnoc
ms.custom: seodec18
ms.openlocfilehash: 90eaf7f57f524cb6e47d9d0f4e160855acd6daf5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633652"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>使用 Azure CLI 建立可持續部署的 App Service 應用程式

此範例指令碼會在 App Service 中建立應用程式及其相關資源，然後設定從 Azure DevOps 存放庫持續部署。 針對此範例，您需要：

* 具有應用程式程式碼的 Azure DevOps 存放庫 (您必須有此存放庫的系統管理權限)。
* 您 Azure DevOps 組織的[個人存取權杖 (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，需要 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 建立 App Service 方案。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 建立 App Service 應用程式。 |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | 將 App Service 應用程式與 Git 或 Mercurial 存放庫建立關聯。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。
