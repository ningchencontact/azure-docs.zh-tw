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
ms.openlocfilehash: da682e3e947c3f8643610e42ad22fb9d742796f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
您可以使用 'Get-AzureRmVirtualNetworkGatewayConnection' Cmdlet，並在搭配或不搭配 '-Debug' 的情況下驗證連線是否成功。 

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 出現提示時，請選取 [A] 以執行 [全部]。 在範例中，'-Name' 是指您想要測試之連線的名稱。

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```