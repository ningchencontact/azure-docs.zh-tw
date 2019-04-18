---
title: 連線到 Azure 媒體服務 v3 API-Node.js
description: 了解如何連接到媒體服務 v3 API 與 Node.js。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495083"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>連線到媒體服務 v3 API-Node.js

這篇文章會示範如何連接到 Azure 媒體服務 v3 node.js SDK 使用服務主體的登入方法。

## <a name="prerequisites"></a>必要條件

- 安裝 [Node.js](https://nodejs.org/en/download/)。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住的資源群組名稱和媒體服務帳戶名稱。

## <a name="create-packagejson"></a>建立 package.json

1. 建立使用您最愛的編輯器的 package.json 檔案。
1. 開啟檔案，並貼上下列程式碼：

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

必須指定下列套件：

|Package|描述|
|---|---|
|`azure-arm-mediaservices`|Azure 媒體服務 SDK。 <br/>若要確定您使用最新的 Azure 媒體服務封裝，請檢查[NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)。|
|`azure-storage`|儲存體 SDK。 檔案上傳到資產時，會使用它。|
|`ms-rest-azure`| 用來登入。|

您可以執行下列命令，以確定您使用最新的套件：

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>連線至 Node.js 用戶端

1. 建立使用您最愛的編輯器的.js 檔案。
1. 開啟該檔案，並貼上下列程式碼。
1. 所得的值 「 結束點組態 」 一節中設定的值[存取 Api](access-api-cli-how-to.md)。

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>執行您的應用程式

開啟命令提示字元。 瀏覽至範例的目錄，然後執行下列命令：

```
npm install 
node index.js
```

## <a name="see-also"></a>請參閱

- [媒體服務概念](concepts-overview.md)
- [NPM 安裝 azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>後續步驟

探索媒體服務 [Node.js 參考](https://aka.ms/ams-v3-nodejs-ref)文件，並查看[範例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)示範如何搭配使用媒體服務 API 與 Node.js。

