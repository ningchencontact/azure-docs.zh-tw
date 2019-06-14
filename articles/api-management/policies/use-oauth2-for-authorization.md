---
title: Azure API 管理原則範例 - 使用 OAuth2 在閘道與後端之間進行授權 | Microsoft Docs
description: Azure API 管理原則範例 - 示範如何在閘道與後端之間使用 OAuth2 進行授權。 此範例示範如何從 AAD 取得存取權杖並將它轉送到後端。
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
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859085"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>使用 OAuth2 在閘道與後端之間進行授權

本文會說明 Azure API 管理原則範例，示範如何在閘道與後端之間使用 OAuth2 進行授權。 此範例示範如何從 AAD 取得存取權杖並將它轉送到後端。 

若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

下列指令碼會使用 {{屬性}} 中出現的屬性。 若要了解屬性以及如何在 API 管理原則中使用它們，請參閱[這個](../api-management-howto-properties.md)主題。
 
## <a name="policy"></a>原則

將程式碼貼至 [輸入]  區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

