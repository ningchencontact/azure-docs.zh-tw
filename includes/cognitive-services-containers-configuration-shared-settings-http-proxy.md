---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151105"
---
如果您需要設定 HTTP Proxy 以進行輸出要求，請使用以下兩個引數：

| Name | 資料類型 | 說明 |
|--|--|--|
|HTTP_PROXY|字串|要使用的 Proxy，例如 `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|字串|對 Proxy 進行驗證時所需的任何認證，例如 username:password。|
|`<proxy-user>`|字串|Proxy 的使用者。|
|`proxy-password`|字串|對於 Proxy 與 `<proxy-user>` 相關聯的密碼。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```