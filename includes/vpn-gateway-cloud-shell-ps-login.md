---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320038"
---
以較高的權限開啟 PowerShell 主控台。



如果您是以本機執行 Azure PowerShell，請連線到您的 Azure 帳戶。 *Connect-AzAccount* Cmdlet 會提示您輸入認證。 驗證後即會下載您的帳戶設定供 Azure PowerShell 使用。 如果您未在本機執行 PowerShell，而改為在瀏覽器中使用 Azure Cloud Shell 的 [試用]，則可略過這個第一步驟。 您會自動連線至您的 Azure 帳戶。

```azurepowershell
Connect-AzAccount
```

如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。

```azurepowershell-interactive
Get-AzSubscription
```

指定您要使用的訂用帳戶。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```