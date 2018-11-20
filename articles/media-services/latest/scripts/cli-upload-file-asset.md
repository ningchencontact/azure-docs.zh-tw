---
title: Azure CLI 指令碼範例 - 將檔案上傳至容器 | Microsoft Docs
description: 您可以使用 Azure CLI 指令碼，將本機檔案上傳至儲存體容器。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 5120d938d137efef77eeb0b69a5bf571bd4c509b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614492"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI 範例：將本機檔案上傳至容器 

本文中的 Azure CLI 指令碼會示範如何將本機檔案上傳至儲存體容器。

## <a name="prerequisites"></a>必要條件 

- 在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，並非所有[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中運作。 建議在本機使用 CLI。

- [建立媒體服務帳戶](../create-account-cli-how-to.md)。

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>後續步驟

如需其他範例，請參閱 [Azure CLI 範例](../cli-samples.md)。
