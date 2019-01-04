---
title: 重設失敗的線路 - ExpressRoute：PowerShell：Azure | Microsoft Docs
description: 本文可協助您重設處於失敗狀態的 ExpressRoute 電路。
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 7b88ba6e00cbec05263fe5bc8e795cda95beee04
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093659"
---
# <a name="reset-a-failed-expressroute-circuit"></a>重設失敗的 ExpressRoute 電路

當 ExpressRoute 電路上的作業未成功完成時，電路可能會進入 [失敗] 狀態。 本文可協助您重設失敗的 Azure ExpressRoute 電路。

## <a name="reset-a-circuit"></a>重設線路

1. 安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。

2. 以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連接：

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. 如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. 指定您要使用的訂用帳戶。

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. 執行下列命令來重設處於失敗狀態的電路：

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

電路現在應該狀況良好。 如果電路仍處於失敗狀態，請使用 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。

## <a name="next-steps"></a>後續步驟

如果仍會發生問題，請向 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 開啟支援票證。
