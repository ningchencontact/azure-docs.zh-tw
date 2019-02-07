---
title: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 的詳細資料 | Microsoft Docs
description: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 的詳細資料
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749154"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>取得 Azure Cache for Redis 的詳細資料

在此案例中，您將了解關於如何擷取 Azure Cache for Redis 執行個體 (包括其佈建狀態) 的詳細資料。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，擷取 Azure Cache for Redis 執行個體的詳細資料。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 取得 Azure Cache for Redis 執行個體的詳細資料。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。
