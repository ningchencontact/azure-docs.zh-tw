---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201477"
---
以較高的權限開啟 PowerShell 主控台。



如果您是以本機執行 Azure PowerShell，請連線到您的 Azure 帳戶。 *Connect-AzureRmAccount* Cmdlet 會提示您輸入認證。 驗證後即會下載您的帳戶設定供 Azure PowerShell 使用。 如果您未在本機執行 PowerShell，而改為在瀏覽器中使用 Azure Cloud Shell 的 [試用]，則可略過這個第一步驟。 您會自動連線至您的 Azure 帳戶。

```azurepowershell
Connect-AzureRmAccount
```

如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。

```azurepowershell-interactive
Get-AzureRmSubscription
```

指定您要使用的訂用帳戶。

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```