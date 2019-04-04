---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2019
ms.openlocfilehash: 2d3d7b37721ca1b19f5d73133352cabdbffe6d68
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886826"
---
如果您需要設定 HTTP Proxy 以進行輸出要求，請使用以下兩個引數：

| 名稱 | 資料類型 | 描述 |
|--|--|--|
|HTTP_PROXY|字串|若要使用，比方說，proxy `http://proxy:8888`|
|HTTP_PROXY_CREDS|字串|對 Proxy 進行驗證時所需的任何認證，例如 username:password。|

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
```