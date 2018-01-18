 ---
title: 匯入 Postman 環境以進行 Azure 媒體服務 REST 呼叫 description: 本主題提供用以進行 Azure 媒體服務 REST 呼叫之 Postman 環境的定義。
services: media-services documentationcenter: '' author: Juliako manager: cfowler editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 01/04/2017 ms.author: juliako

---

# <a name="import-the-postman-environment"></a>匯入 Postman 環境 

本文包含用於 [Postman 集合](postman-collection.md)的 **Postman** 環境變數定義，其中包含呼叫媒體服務 REST API 的分組 HTTP 要求。 此環境與集合檔案適用於[設定 Postman 以進行媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)教學課程。

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
