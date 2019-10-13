---
title: 包含檔案
description: 包含檔案
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285605"
---
| 網域名稱                  | 輸出連接埠 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 中的資料移動服務。 |
| `*.frontend.clouddatahub.net` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 |
| `download.microsoft.com`    | 443            | 自我裝載整合執行階段所需，以用於下載更新。 如果您已停用自動更新，則可以省略此步驟。 |
| `*.core.windows.net`          | 443            | 當您使用[分段複製](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能時，可供自我裝載整合執行階段用來連線到 Azure 儲存體帳戶。 |
| `*.database.windows.net`      | 1433           | (選擇性) 當您在 Azure SQL Database 或 Azure SQL 資料倉儲來回複製時，需要提供此資訊。 若要在不開啟連接埠 1433 的情況下，將資料複製到 Azure SQL Database 或 Azure SQL 資料倉儲，請使用分段複製功能。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (選擇性) 當您在 Azure Data Lake Store 來回複製時，需要提供此資訊。 |
