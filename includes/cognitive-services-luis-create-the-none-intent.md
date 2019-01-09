---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0579b1e21e714e25e197cb5abf46793a38978d06
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2018
ms.locfileid: "53755594"
---
用戶端應用程式必須知道語句是否對應用程式沒有意義或不適當。 **None** 意圖會在建立過程中新增至每個應用程式，以判斷用戶端應用程式是否無法回答語句。

如果 LUIS 針對語句傳回 [None] 意圖時，用戶端應用程式可以詢問使用者是否想要結束交談，或提供更多有關繼續交談的指示。 

> [!CAUTION] 
> 請勿將 **None** 意圖留空。 

1. 選取左面板中的 [意圖]。

2. 選取 [無] 意圖。 新增您的使用者可能輸入但與您的人力資源應用程式無關的三個語句：

    | 範例語句|
    |--|
    |狗一直叫很煩人|
    |幫我訂披薩|
    |海裡面的企鵝|