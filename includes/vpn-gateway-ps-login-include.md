---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173848"
---
開始這項設定之前，您必須登入 Azure 帳戶。 此 Cmdlet 會提示您輸入 Azure 帳戶的登入認證。 登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。 如需詳細資訊，請參閱 [搭配使用 Windows PowerShell 與 Resource Manager](../articles/powershell-azure-resource-manager.md)。

若要登入，請以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連接：

```powershell
Connect-AzAccount
```

如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

```powershell
Get-AzSubscription
```

指定您要使用的訂用帳戶。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
