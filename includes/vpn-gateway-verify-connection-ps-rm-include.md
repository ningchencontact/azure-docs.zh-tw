---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 10/17/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456327"
---
您可以使用 'Get-AzVirtualNetworkGatewayConnection' Cmdlet，並在搭配或不搭配 '-Debug' 的情況下驗證連線是否成功。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 [A] 以執行 [全部]。 在範例中，'-Name' 是指您想要測試之連線的名稱。

   ```azurepowershell
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```