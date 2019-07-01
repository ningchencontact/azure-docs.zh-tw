---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ebea8bfd69a4df605142ab82f3efbc7d97d34529
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143952"
---
在 Azure Cloud Shell 中，使用 [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令來設定部署認證。 您可以使用這個部署使用者，將 FTP 和本機 Git 部署至 Web 應用程式。 使用者名稱和密碼是帳戶等級。 _它們與您的 Azure 訂用帳戶認證不同。_

在下列範例中，請以新的使用者名稱和密碼取代 *\<username>* 和 *\<password>* (包括括弧)。 使用者名稱在 Azure 內必須是唯一的。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

您會獲得一個密碼顯示為 `null` 的 JSON 輸出。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 本機 Git 推送的部署使用者名稱不能包含 '@' 符號。

您只需設定此部署使用者一次。 您可以將它用於所有 Azure 部署。

> [!NOTE]
> 請記錄使用者名稱和密碼。 您稍後需要它們來部署 Web 應用程式。
>
>
