---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 875dd9d768b5f40f2d4ed3fad5b14a6cd6f6f6ba
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129247"
---
### <a name="sign-in-to-azure-cli"></a>登入 Azure CLI
登入 Azure。 在終端機視窗中輸入下列命令：

```cmd
az login
```

> [!Note]
> 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。

#### <a name="if-you-have-multiple-azure-subscriptions"></a>如果您有多個 Azure 訂用帳戶...
您可以執行下列命令以檢視訂用帳戶： 

```cmd
az account list
```
在 JSON 輸出中找出含有 `isDefault: true` 的訂用帳戶。
如果這不是您要使用的訂用帳戶，您可以變更預設訂用帳戶：

```cmd
az account set --subscription <subscription ID>
```
