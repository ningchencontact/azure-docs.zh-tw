---
title: API HTTP 回應碼 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解 QnA Maker API 會傳回哪些 HTTP 回應碼。 這將協助您解決任何錯誤。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446528"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP 回應碼
[管理](https://go.microsoft.com/fwlink/?linkid=2092179)和預測 API 傳回 HTTP 回應碼。 當回應訊息包含專屬於要求的資訊時，HTTP 回應狀態碼為一般。 

### <a name="management"></a>管理性

|代碼|說明|
|:--|--|
|2xx|成功|
|400|要求的參數不正確，表示必要參數遺失、格式不正確或太大|
|400|要求的本文不正確，表示 JSON 遺失、格式不正確或太大|
|401|金鑰無效|
|403|禁止 - 您沒有正確的權限|
|404|KB 不存在|
|410|此 API 已被淘汰且不再提供|
