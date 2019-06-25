---
title: 包含檔案
description: 包含檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305258"
---
1. 請確定您已登入您的 Azure 帳戶，並使用您想要上架此預覽的訂用帳戶。 請使用下列範例進行註冊：

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  再次重新註冊您的訂用帳戶，具有*Microsoft.Network*提供者命名空間。

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. 使用下列命令來確認*AllowBastionHost*與您的訂用帳戶註冊功能：

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    可能需要幾分鐘的時間才能完成註冊。
