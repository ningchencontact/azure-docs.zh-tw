---
title: 匯出 Azure 訂用帳戶最上層資訊 | Microsoft Docs
description: 描述如何檢視與帳戶相關聯的所有 Azure 訂用帳戶識別碼。
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: 09231ab69276f3b4763f07c51230921d15333f63
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339428"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>匯出並檢視最上層的訂用帳戶資訊
如果您需要檢視與使用者認證相關聯的訂用帳戶識別碼集，[請從 Azure 帳戶中心下載含有訂用帳戶資訊的 .json 檔案](http://account.azure.com/subscriptions/download)。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

下載的 .json 檔案會提供下列資訊：
- 電子郵件：與帳戶相關聯的電子郵件地址。
- Puid：與計費帳戶相關聯的唯一識別碼。
- SubscriptionIds：屬於您帳戶的訂用帳戶清單 (依照訂用帳戶識別碼所列舉)。

### <a name="subscriptionsjson-sample"></a>subscriptions.json 範例

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
