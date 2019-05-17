---
title: Azure CLI 指令碼範例 - 建立轉換 | Microsoft Docs
description: 您可以使用 Azure CLI 指令碼來建立轉換。
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 86f455f25bb41833774bb7ad7d81688aad812b5a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236811"
---
# <a name="cli-example-create-a-transform"></a>CLI 範例：建立轉換

本文中的 Azure CLI 指令碼會示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。 請一律檢查是否已有所需名稱和「配方」的轉換。 若是如此，您應該重複使用該轉換。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>後續步驟

[媒體服務概觀](media-services-overview.md)
