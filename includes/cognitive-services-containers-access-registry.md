---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125140"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>使用 Docker CLI 驗證私人容器登錄

有幾個方式可以向認知服務容器的私人容器登錄驗證，但從命令列進行的建議方法是使用 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) \(英文\)。

使用下列範例中所示的 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令來登入 `containerpreview.azurecr.io` (認知服務容器的私人容器登錄)。 將 *\<username\>* 取代為使用者名稱，並將 *\<password\>* 取代為您從 Azure 認知服務小組收到的認證所提供的密碼。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已經將您的認證保護在文字檔中，您可以將該文字檔的內容串連 (使用 `cat` 命令) 至 `docker login` 命令，如下列範例所示。 將 *\<passwordFile\>* 取代為包含密碼之文字檔的路徑和名稱，並將 *\<username\>* 取代為您認證中提供的使用者名稱。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
