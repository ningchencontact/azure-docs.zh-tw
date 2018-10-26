---
title: API HTTP 回應碼 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解哪些 HTTP 回應碼會從 QnA Maker API 傳回
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079691"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP 回應碼
[管理](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)和預測 API 傳回 HTTP 回應碼。 當回應訊息包含專屬於要求的資訊時，HTTP 回應狀態碼為一般。 

### <a name="management"></a>管理性

|代碼|說明|
|:--|--|
|2xx|成功|
|400|要求的參數不正確，表示必要參數遺失、格式不正確或太大|
|400|要求的本文不正確，表示 JSON 遺失、格式不正確或太大|
|401|金鑰無效|
|403|禁止 - 您沒有正確的權限|
|404|KB 不存在|