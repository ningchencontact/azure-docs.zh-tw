---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886825"
---
`Logging` 設定可管理適用於容器的 ASP.NET Core 記錄支援。 對於您用於 ASP.NET Core 應用程式的容器，您可以使用相同的組態設定和值。 

容器支援下列記錄提供者：

|提供者|目的|
|--|--|
|[主控台](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[偵錯](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 記錄提供者。 支援此記錄提供者的所有 ASP.NET Core 組態設定和預設值。|
|[磁碟](#disk-logging)|JSON 記錄提供者。 此記錄提供者會將記錄資料寫入至輸出裝載。|

此容器 命令會採用 JSON 格式輸出掛接儲存記錄資訊：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

此容器 命令會顯示偵錯資訊，請在前面加上`dbug`，而容器正在執行：

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>磁碟記錄

`Disk` 記錄提供者支援下列組態設定：  

| 名稱 | 資料類型 | 描述 |
|------|-----------|-------------|
| `Format` | 字串 | 適用於記錄檔的輸出格式。<br/> **附註：** 此值必須設定為 `json` 以啟用記錄提供者。 如果在具現化期間指定此值，但沒有同時指定輸出裝載，便會發生錯誤。 |
| `MaxFileSize` | 整數  | 記錄檔的大小上限，以 MB 為單位。 當目前記錄檔的大小符合或超過此值時，記錄提供者便會建立新的記錄檔。 如果指定 -1，記錄檔的大小便只會受限於輸出裝載的檔案大小上限 (若有的話)。 預設值為 1。 |

如需設定 ASP.NET Core 記錄支援的詳細資訊，請參閱[設定檔案組態](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)。

