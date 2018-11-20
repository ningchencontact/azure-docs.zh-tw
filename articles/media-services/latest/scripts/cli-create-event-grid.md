---
title: Azure CLI 指令碼範例 - 建立 Azure Event Grid 訂用帳戶 | Microsoft Docs
description: 本主題中的 Azure CLI 指令碼會示範如何建立作業狀態變更的帳戶層級 Event Grid 訂用帳戶。
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
ms.openlocfilehash: a3cff649001adf569f1454d16a2a97b32972ef00
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612609"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI 範例：建立 Azure Event Grid 訂用帳戶 

本文中的 Azure CLI 指令碼會示範如何建立作業狀態變更的帳戶層級 Event Grid 訂用帳戶。

## <a name="prerequisites"></a>必要條件 

- 在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，並非所有[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中運作。 建議在本機使用 CLI。

- [建立媒體服務帳戶](../create-account-cli-how-to.md)。

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>後續步驟

如需其他範例，請參閱 [Azure CLI 範例](../cli-samples.md)。
