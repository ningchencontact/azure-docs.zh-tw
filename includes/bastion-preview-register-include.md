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
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191742"
---
1. 請確定您已登入您的 Azure 帳戶，並使用您想要上架此預覽的訂用帳戶。 請使用下列範例進行註冊：

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  再次重新註冊您的訂用帳戶，具有*Microsoft.Network*提供者命名空間。

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. 使用下列命令來確認*AllowBastionHost*與您的訂用帳戶註冊功能：

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    可能需要幾分鐘的時間才能完成註冊。