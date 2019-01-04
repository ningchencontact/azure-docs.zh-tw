---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444007"
---
您可以使用 'Get-AzureRmVirtualNetworkGatewayConnection' Cmdlet，並在搭配或不搭配 '-Debug' 的情況下驗證連線是否成功。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 [A] 以執行 [全部]。 在範例中，'-Name' 是指您想要測試之連線的名稱。

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```