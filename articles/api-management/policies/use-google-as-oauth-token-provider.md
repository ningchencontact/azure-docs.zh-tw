---
title: Azure API 管理原則範例 - 使用 Google OAuth 權杖授權存取 | Microsoft Docs
description: Azure API 管理原則範例 - 示範如何使用 Google 作為 OAuth 權杖提供者來授權存取您的端點。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 46ffe21b31597f889b411a40a9f3e1f2dd76f1ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933457"
---
# <a name="authorize-access-using-google-oauth-token"></a>使用 Google OAuth 權杖授權存取

本文會說明 Azure API 管理原則範例，示範如何使用 Google 作為 OAuth 權杖提供者來授權存取您的端點。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

