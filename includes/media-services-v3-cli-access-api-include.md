---
title: 包含檔案
description: 包含檔案
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309004"
---
## <a name="access-the-media-services-api"></a>存取媒體服務 API

若要連線到 Azure Media Services API，您可使用 Azure AD 服務主體驗證。 下列命令會建立 Azure AD 應用程式，並將服務主體連結到帳戶。 您應使用傳回值來設定您的應用程式。

執行指令碼之前，您可以用建立資源時所選擇的名稱取代 `amsaccount` 和 `amsResourceGroup`。 `amsaccount` 是連結服務主體的 Azure 媒體服務名稱。

此命令會傳回 `json` 輸出︰

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

此命令會產生如下的回應：

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

如果您想要在回應中取得 `xml`，請使用下列命令：

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
