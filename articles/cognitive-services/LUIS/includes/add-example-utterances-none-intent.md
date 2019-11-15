---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648124"
---
用戶端應用程式必須知道語句是否對應用程式沒有意義或不適當。 **None** 意圖會在建立過程中新增至每個應用程式，以判斷用戶端應用程式是否無法回答語句。

如果 LUIS 針對語句傳回 [None]  意圖時，用戶端應用程式可以詢問使用者是否想要結束交談，或提供更多有關繼續交談的指示。 

如果您將 [無]  意圖保留空白，則會在其中一個現有的主體網域意圖中預測應在主體網域外部預測的表達。 結果就是用戶端應用程式 (例如聊天機器人) 將根據不正確的預測來執行錯誤的作業。 

1. 選取左面板中的 [意圖]  。

1. 選取 [無]  意圖。 新增您的使用者可能輸入但與您的應用程式無關的三個語句：

    |`None` 範例表達|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
