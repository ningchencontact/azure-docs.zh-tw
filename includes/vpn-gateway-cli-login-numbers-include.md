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
ms.openlocfilehash: cf3bbb6c45061d6f4885839fbfb7f069264fe986
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444011"
---
1. 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 如需關於登入的詳細資訊，請參閱[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。

   ```azurecli
   az login
   ```
2. 如果您有多個 Azure 訂用帳戶，請列出帳戶的所有訂用帳戶。

   ```azurecli
   az account list --all
   ```
3. 指定您要使用的訂用帳戶。

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
