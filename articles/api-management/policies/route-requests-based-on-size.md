---
title: Azure API 管理原則範例 - 根據要求主體的大小進行要求的路由傳送 | Microsoft Docs
description: Azure API 管理原則範例 - 示範如何根據要求主體的大小進行要求的路由傳送。
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
ms.openlocfilehash: da21a7c83a61b618646c4c4d2f422756b04dc300
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934143"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>根據要求主體的大小進行要求的路由傳送

本文會說明 Azure API 管理原則範例，示範如何根據要求主體的大小進行要求的路由傳送。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

