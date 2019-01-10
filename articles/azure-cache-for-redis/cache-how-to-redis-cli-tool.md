---
title: 如何搭配使用 redis-cli 與 Azure Redis 快取 | Microsoft Docs
description: 了解如何搭配使用 redis-cli 與「Azure Redis 快取」。
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: 68c533235480ec49e3c561d99095d5722f6f69a4
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106319"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>如何搭配使用 Redis 命令列工具與 Azure Redis 快取

*redis-cli.exe* 是一個能以用戶端形式與「Azure Redis 快取」進行互動的常用命令列工具。 此工具也可供與「Azure Redis 快取」搭配使用。

如果使用 Windows 平台，可下載[適用於 Windows 的 Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)使用此工具。 

如果您想要在另一個平台上執行此命令列工具，請從 [https://redis.io/download](https://redis.io/download) 下載「Azure Redis 快取」。

## <a name="gather-cache-access-information"></a>收集快取存取資訊

您可以使用三種方法收集存取快取所需的資訊：

1. 在 Azure CLI 使用[az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. 在 Azure PowerShell 使用[Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. 使用 Azure 入口網站。

在本節中，您將從 Azure 入口網站來擷取金鑰。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>啟用 redis-cli.exe 的存取

使用「Azure Redis 快取」時，預設只會啟用 SSL 連接埠 (6380)。 `redis-cli.exe` 命令列工具不支援 SSL。 您有兩個設定選項可以使用它：

1. [啟用非 SSL 連接埠 (6379)](cache-configure.md#access-ports) - **不建議使用此設定**，因為在此設定中，存取金鑰會透過 TCP 以純文字傳送。 此變更可能會危害您的快取存取。 只有當您只是要存取測試快取時，才會考慮此設定。

2. 下載並安裝 [stunnel](https://www.stunnel.org/downloads.html)。

    執行 **stunnel GUI Start** 啟動伺服器。

    以滑鼠右鍵按一下 stunnel 伺服器的工作列圖示，然後按一下 [Show Log Window \(顯示記錄視窗\)]。

    在 stunnel 的 [Log Windows \(記錄視窗\)] 功能表上按一下 [Configuration \(設定\)]  >  [Edit Configuration \(編輯設定\)] 開啟目前的設定檔。

    在 [Service definitions \(服務定義\)] 區段下加入 *redis-cli.exe* 的下列項目。 取代 `yourcachename`，插入您實際的快取名稱。 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    儲存並關閉設定檔。 
  
    在 stunnel 的 [Log Window \(記錄視窗\)] 功能表上按一下 [Configuration \(設定\)]  >  [Reload Configuration \(重新載入設定\)]。


## <a name="connect-using-the-redis-command-line-tool"></a>使用 Redis 命令列工具連線。

使用 stunnel 時，執行 *redis-cli.exe*，只傳遞您的「連接埠」和「存取金鑰」(主要或次要) 以連線到快取。

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![搭配使用 stunnel 與 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

如果您使用的測試快取有**不安全**的非 SSL 連接埠，請執行 `redis-cli.exe` 並傳遞您的「主機名稱」、「連接埠」和「存取金鑰」(主要或次要) 以連線到測試快取。

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![搭配使用 stunnel 與 redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>後續步驟

深入了解如何使用 [Redis 主控台](cache-configure.md#redis-console)發出命令。

