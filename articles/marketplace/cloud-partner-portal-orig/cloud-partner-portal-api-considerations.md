---
title: API 考量 | Microsoft Docs
description: 使用 Marketplace API 時的版本設定、錯誤處理與授權問題。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806439"
---
<a name="api-considerations"></a>API 考量
=================

<a name="api-versioning"></a>API 版本控制
--------------

同時可能會提供多個版本的 API。 用戶端必須透過提供 `api-version` 參數做為查詢字串的一部分，以指出希望叫用的版本。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

對於具有未知或無效 API 版本之要求的回應是 HTTP 代碼 400。 此錯誤會在回應主體中傳回已知 API 版本集合。

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>錯誤
------

API 會以對應的 HTTP 狀態碼與選擇性的額外資訊 (在回應中序列化為 JSON) 回應錯誤。
當您收到錯誤時 (特別是 400 類別的錯誤)，解決根本原因之前請勿重試要求。 例如，在上面相同的範例回應中，請在重新傳送要求之前修正 API 版本參數。

<a name="authorization-header"></a>驗證標頭
--------------------

針對此參考中的所有 API，您必須隨著從 Azure Active Directory (Azure AD) 取得的持有人權杖傳遞授權標頭。 需要此標頭才能接收有效的回應；若此標頭不存在，將會傳回 `401 Unauthorized` 錯誤。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
