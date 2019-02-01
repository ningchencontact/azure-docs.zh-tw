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
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098900"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI 範例：建立 Azure 事件方格訂用帳戶 

本文中的 Azure CLI 指令碼會示範如何建立作業狀態變更的帳戶層級 Event Grid 訂用帳戶。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](../create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>後續步驟

如需其他範例，請參閱 [Azure CLI 範例](../cli-samples.md)。
