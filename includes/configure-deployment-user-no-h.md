---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344681"
---
在 Cloud Shell 中，使用 [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令來設定部署認證。 需要這個部署使用者，才能將 FTP 和本機 Git 部署至 Web 應用程式。 使用者名稱和密碼是帳戶等級。 _它們與您的 Azure 訂用帳戶認證不同。_

在下列範例中，以新的使用者名稱和密碼取代 *\<username>* 和 *\<password>* (包括括弧)。 使用者名稱必須是 Azure 中唯一的。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字、符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

您應取得 JSON 輸出，其中密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 ` 'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。

您只需設定此部署使用者一次，即可將它用於所有 Azure 部署。

> [!NOTE]
> 記下使用者名稱和密碼。 您稍後需要它們來部署 Web 應用程式。
>
>
