---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551213"
---
您可以存取容器內的 從產生的主控台記錄檔。 首先，開啟容器記錄在 Cloud Shell 中執行下列命令：

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

開啟容器記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

> [!NOTE]
> 您也可以檢查記錄檔，從在瀏覽器`https://<app-name>.scm.azurewebsites.net/api/logs/docker`。

若要停止記錄資料流在任何時間，請輸入`Ctrl` + `C`。
