---
title: 傳送至 Azure 資訊安全中心的安全性訊息，IoT preview |Microsoft Docs
description: 了解如何將傳送您 iot 使用 Azure 資訊安全中心的安全性訊息。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a91a3538a9c176e3c76e351eb53eb84decc85938
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200542"
---
# <a name="send-security-messages-sdk"></a>傳送安全性訊息 SDK

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本作法指南說明的 IoT 服務功能的 Azure 資訊安全中心 (ASC)，當您選擇要收集並傳送您的裝置安全性訊息，而不需使用 ASC IoT 代理程式，並說明如何執行這項操作。  

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 使用 C# 的傳送安全性訊息 API
> * 使用 C 的傳送安全性訊息 API

## <a name="asc-for-iot-capabilities"></a>針對 IoT 功能 ASC

處理和分析任何種類的安全性訊息資料，只要傳送的資料符合 iot ASC [IoT 結構描述的 ASC](https://aka.ms/iot-security-schemas)和訊息已設定為安全性訊息。

## <a name="security-message"></a>安全性訊息

Iot 的 ASC 定義安全性訊息，使用下列準則：
- 如果訊息已傳送 Azure IoT C 與 /C# SDK
- 如果訊息符合[安全性訊息結構描述](https://aka.ms/iot-security-schemas)
- 如果訊息已設定為在傳送前的安全性訊息

每個安全性訊息包含寄件者的中繼資料，例如`AgentId`， `AgentVersion`，`MessageSchemaVersion`和安全性事件的清單。
結構描述定義的有效和必要屬性包括的事件類型的安全性訊息。

[!NOTE]
> 所傳送的訊息若不符合結構描述，則會遭到忽略。 請務必先確認結構描述再起始資料的傳送，因為系統目前並不會儲存遭到忽略的訊息。 
> 未設定為使用 Azure IoT C 安全性訊息傳送的訊息 /C# SDK 將不會路由至 IoT 管線 ASC

## <a name="valid-message-example"></a>有效的訊息範例

下列範例會顯示有效的安全性訊息物件。 此範例包含的訊息中繼資料，另一個`ProcessCreate`安全性事件。

一次設定為安全性訊息並傳送此訊息會被處理 asc iot。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>傳送安全性訊息 

傳送安全訊息而不使用 ASC IoT 代理程式，使用[Azure IoTC#裝置 SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)或是[Azure IoT C 裝置 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)。

若要從裝置傳送裝置資料供適用於 IoT 的 ASC 處理，請使用下列其中一個 API 來標記訊息，以便訊息能夠正確路由至適用於 IoT 的 ASC 處理管線。 以這種方式傳送的訊息在經過處理後，會在 IoT 中樞或 Azure 資訊安全中心內顯示為適用於 IoT 的 ASC 裡面的安全性深入解析。 

傳送的所有資料 (即使已標上正確的標頭)，仍必須符合[適用於 IoT 的 ASC 訊息結構描述](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>傳送安全性訊息 API

**傳送安全性訊息** API 目前適用於 C 和C#。  

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

## <a name="next-steps"></a>後續步驟
- 閱讀適用於 IoT 的 ASC 服務[概觀](overview.md)
- 深入了解適用於 IoT 的 ASC [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題集](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 了解[警示](concept-security-alerts.md)
