---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945176"
---
## <a name="create-a-project-zip-file"></a>建立專案 ZIP 檔案

>[!NOTE]
> 如果您在 ZIP 檔案中下載了檔案，請先將檔案解壓縮。 例如，如果您從 GitHub 下載了 ZIP 檔案，則無法以原狀部署該檔案。 GitHub 會新增其他巢狀目錄，其不適用於 App Service。 
>

在本機終端機視窗中，瀏覽至應用程式專案的根目錄。 

此目錄應該包含您的 web 應用程式的項目檔案，例如 index.html、index.php 和 app.js。 也可以包含套件管理檔案，像是 project.json、composer.json、package.json、bower.json 和 requirements.txt。

除非您想要 App Service 為您執行部署自動化，否則請執行所有的組建工作（例如，`npm`、`bower`、`gulp`、`composer`和 `pip`），並確定您擁有執行應用程式所需的所有檔案。 如果您想要[直接執行封裝](../articles/app-service/deploy-run-package.md)，則需要此步驟。

在專案中建立所有項目的 ZIP 封存。 下列命令會使用您終端機中的預設工具：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

