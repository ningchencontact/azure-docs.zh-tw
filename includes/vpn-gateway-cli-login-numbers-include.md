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
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313603"
---
1. 使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。 如需有關登入的詳細資訊，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)。

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