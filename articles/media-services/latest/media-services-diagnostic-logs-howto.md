---
title: 監視媒體服務透過 Azure 監視器的診斷記錄 |Microsoft Docs
description: 本文說明如何將路由和檢視診斷記錄檔，透過 Azure 監視器。
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795845"
---
# <a name="monitor-media-services-diagnostic-logs"></a>監視媒體服務的診斷記錄檔

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷的記錄檔，可協助您了解如何執行您的應用程式。 詳細的說明，這個功能以及您要使用 Azure 媒體服務的計量和診斷記錄的原因，請參閱[監視媒體服務的計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

這篇文章會示範如何將資料路由傳送至儲存體帳戶，然後再檢視資料。 

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](create-account-cli-how-to.md)。
- 檢閱[監視媒體服務的計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

## <a name="route-data-to-the-storage-account-using-the-portal"></a>將資料路由至使用入口網站的儲存體帳戶

1. 在 https://portal.azure.com 上登入 Azure 入口網站。
1. 瀏覽至您的媒體服務帳戶中，按一下**Diagnostic-settings**下方**監視器**。 在這裡，您會看到訂用帳戶中所有透過 Azure 監視器而產生監視資料的資源清單。 

    ![診斷設定區段](media/media-services-diagnostic-logs/logs01.png)

1. 按一下 **新增診斷設定**。

   資源診斷設定可定義應該從特定資源路由傳送「什麼」  監視資料，以及監視資料應該傳送至「何處」  。

1. 在出現的區段中，為您的設定指定**名稱**，並勾選 [封存至儲存體帳戶]  方塊。

    選取您要傳送記錄檔和按下的儲存體帳戶**確定**。
1. 勾選 [記錄]  和 [計量]  下的所有方塊。 根據資源類型而定，您可能只有其中一個選項可用。 這些核取方塊可控制要將該資源類型的哪幾種記錄和計量資料傳送至您選取的目的地，在此案例中是儲存體帳戶。

   ![診斷設定區段](media/media-services-diagnostic-logs/logs02.png)
1. 將 [保留期 (天數)]  滑桿設為 30。 此滑桿可設定監視資料在儲存體帳戶中的保留天數。 Azure 監視器會自動刪除比指定天數更舊的資料。 保留天數為 0 會無限期地儲存資料。
1. 按一下 [儲存]  。

來自資源的監視資料現在開始流入儲存體帳戶。

## <a name="route-data-to-the-storage-account-using-the-cli"></a>將資料路由至使用 CLI 的儲存體帳戶

若要啟用儲存體帳戶中診斷記錄的儲存體，您可以執行下列`az monitor diagnostic-settings`CLI 命令： 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例如:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>使用入口網站的儲存體帳戶中的檢視資料

如果您遵循上述步驟操作，資料已經開始流向儲存體帳戶。

您可能需要等候長達五分鐘，事件才會出現在儲存體帳戶中。

1. 在入口網站中，在左側導覽列尋找並瀏覽至 [儲存體帳戶]  區段。
1. 識別您在上一節所建立的儲存體帳戶並按一下。
1. 按一下  **Blob**，然後在標示為容器**insights-記錄-keydeliveryrequests**。 這是具有您的記錄檔中的容器。 監視資料是屬於容器的資源識別碼，然後依日期和時間。
1. 依資源識別碼、日期和時間按一下容器，以瀏覽至 PT1H.json 檔案。 按一下 PT1H.json 檔案，然後按一下 [下載]  。

 您現在可以檢視已儲存在儲存體帳戶中的 JSON 事件。

### <a name="examples-of-pt1hjson"></a>PT1H.json 的範例

#### <a name="clear-key-delivery-log"></a>清除金鑰傳遞記錄

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine 加密的金鑰傳遞記錄

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>另請參閱

* [Azure 監視器計量](../../azure-monitor/platform/data-platform.md)
* [Azure 監視器診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>後續步驟

[監視計量](media-services-metrics-howto.md)
