---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148022"
---
## <a name="test"></a>測試函式

在 Mac 或 Linux 電腦上使用 cURL 來測試已部署的函式，在 Windows 上則請使用 Powershell。 執行下列 cURL 命令時，但請將其中的 `<app_name>` 預留位置改為函式應用程式的名稱。 將查詢字串 `&name=<yourname>` 附加至 URL。

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![瀏覽器顯示的函式回應。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

如果您的命令列無法使用 `cURL` 或 `Invoke-WebRequest`，在網頁瀏覽器的位址中輸入相同 URL 即可。 同樣地，請將 `<app_name>` 預留位置改為函式應用程式的名稱，然後對 URL 附加查詢字串 `&name=<yourname>` 並執行要求。

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![瀏覽器顯示的函式回應。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
