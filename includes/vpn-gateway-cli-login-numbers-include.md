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
ms.openlocfilehash: a72f5f5a1d8c64b347185827906f15d3cb9f55ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
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