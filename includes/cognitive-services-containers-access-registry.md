---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704245"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>使用 Docker CLI 來驗證私人容器登錄

您可以向私人容器登錄的認知服務容器中任一種，但建議的方法，從命令列是使用[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

使用[`docker login`命令](https://docs.docker.com/engine/reference/commandline/login/)所示，在下列範例中，登入`containerpreview.azurecr.io`，認知服務容器的私用容器登錄。 取代 *\<使用者名稱\>* 的使用者名稱與 *\<密碼\>* 提供認證，您已收到的密碼Azure 認知服務小組。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果您已受保護您的認證在文字檔中，您可以藉由串連該文字檔的內容`cat`命令，以`docker login`命令，如下列範例所示。 取代 *\<passwordFile\>* 使用的路徑和名稱的文字檔案，其中包含密碼和 *\<username\>* 的使用者名稱提供您的認證。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
