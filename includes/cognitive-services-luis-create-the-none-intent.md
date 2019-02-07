---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 08328b3c872ce9e8a25db6075b00e0cee2fdf4fd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480628"
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
