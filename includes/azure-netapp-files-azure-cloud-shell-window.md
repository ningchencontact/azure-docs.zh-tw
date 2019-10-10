---
title: 包含檔案
description: 包含檔案
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: 3a63fd96b09910b0cd7ee8c3ab9947b034173c8f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836299"
---
1. 指定已列入 Azure NetApp Files 白名單的訂用帳戶：
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. 註冊 Azure 資源提供者： 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```