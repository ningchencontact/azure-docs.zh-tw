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
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208299"
---
## <a name="roles-versus-hierarchical-entities"></a>角色與階層式實體

您應該使用階層式實體，或包含角色的簡單實體模式？ 

視情況而定。 模式和範例語句相似，因為這兩者均代表使用者在特定意圖下使用的語句。  

若語句沒有清楚的模式，請使用階層式實體。 

|階層式實體|包含角色的簡單實體|
|--|--|
|可在意圖的範例語句和模式中取得|只能在模式中取得|
|在意圖中必須有範例語句且子實體已標記|不能在意圖的範例語句中標記角色|
|可能需要意圖中**更多**的範例語句才能擷取實體|應該需要意圖中**較少**的範例語句|
