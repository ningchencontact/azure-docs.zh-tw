---
title: 設定-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的設定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992427"
---
# <a name="event-grid-configuration"></a>事件方格設定

事件方格提供許多可在每個環境中修改的設定。 下一節是所有可用選項及其預設值的參考。

## <a name="tls-configuration"></a>TLS 設定

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-api-protocol.md)中找到其使用方式的範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| 事件方格模組的 TLS 原則。 預設值為 [僅限 HTTPS]。
|`inbound:serverAuth:serverCert:source`| 事件方格模組用來進行 TLS 設定的伺服器憑證來源。 預設值為 IoT Edge。

## <a name="incoming-client-authentication"></a>傳入用戶端驗證

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-client-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| 開啟/關閉以憑證為基礎的用戶端驗證。 預設值為 true。
|`inbound:clientAuth:clientCert:source`| 驗證用戶端憑證的來源。 預設值為 IoT Edge。
|`inbound:clientAuth:clientCert:allowUnknownCA`| 允許自我簽署用戶端憑證的原則。 預設值為 true。
|`inbound:clientAuth:sasKeys:enabled`| 若要開啟/關閉以 SAS 金鑰為基礎的用戶端驗證。 預設值為 off。
|`inbound:clientAuth:sasKeys:key1`| 其中一個值，用來驗證傳入的要求。
|`inbound:clientAuth:sasKeys:key2`| 選擇性的第二個值，用來驗證傳入的要求。

## <a name="outgoing-client-authentication"></a>外寄用戶端驗證
若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-identity-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| 若要開啟/關閉附加連出要求的身分識別憑證。 預設值為 true。
|`outbound:clientAuth:clientCert:source`| 用來抓取事件方格模組外寄憑證的來源。 預設值為 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件處理常式

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-webhook-subscriber-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| 控制是否只允許 HTTPS 訂閱者的原則。 預設值為 true （僅限 HTTPS）。
|`outbound:webhook:skipServerCertValidation`| 用來控制是否要驗證訂閱者憑證的旗標。 預設值為 true。
|`outbound:webhook:allowUnknownCA`| 控制訂閱者是否可以出示自我簽署憑證的原則。 預設值為 true。 

## <a name="delivery-and-retry"></a>傳遞和重試

若要深入瞭解這項功能的一般資訊，請參閱[傳遞和重試](delivery-retry.md)。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | 傳遞事件的嘗試次數上限。 預設值為 30。
| `broker:defaultEventTimeToLiveInSeconds` | 存留時間（TTL），以秒為單位，如果未傳遞，則會捨棄事件。 預設值為**7200**秒

## <a name="output-batching"></a>輸出批次處理

若要深入瞭解這項功能的一般資訊，請參閱[傳遞和輸出批次處理](delivery-output-batching.md)。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 旋鈕所允許的最大值。 預設值為 `1_058_576`。
| `api:deliveryPolicyLimits:maxEventsPerBatch` | `MaxEventsPerBatch` 旋鈕所允許的最大值。 預設值為 `50`。
| `broker:defaultMaxBatchSizeInBytes` | 只有在指定 `MaxEventsPerBatch` 時，傳遞要求大小上限。 預設值為 `1_058_576`。
| `broker:defaultMaxEventsPerBatch` | 只有在指定 `MaxBatchSizeInBytes` 時，要新增至批次的最大事件數目。 預設值為 `10`。
