---
title: 跨租用戶驗證 - Azure Resource Manager
description: 描述 Azure Resource Manager 處理跨租用戶驗證要求的方式。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109473"
---
# <a name="authenticate-requests-across-tenants"></a>跨租用戶驗證要求

在建立多租用戶應用程式時，您可能需要處理不同租用戶中資源的驗證要求。 常見的案例是某個租用戶中的虛擬機器必須加入其他租用戶中的虛擬網路。 Azure Resource Manager 提供儲存輔助權杖的標頭值，以驗證對不同租用戶的要求。

## <a name="header-values-for-authentication"></a>驗證標頭值

要求具有下列驗證標頭值：

| 標頭名稱 | 說明 | 範例值 |
| ----------- | ----------- | ------------ |
| Authorization | 主要權杖 | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 輔助權杖 | Bearer &lt;auxiliary-token1&gt;; EncryptedBearer &lt;auxiliary-token2&gt;; Bearer &lt;auxiliary-token3&gt; |

輔助標頭最多可以持有三個輔助權杖。 

在您的多租用戶應用程式的程式碼中，取得其他租用戶的驗證權杖，並將它們儲存在輔助標頭中。 所有權杖都必須來自相同的使用者或應用程式。 使用者或應用程式必須已經受邀請為其他租用戶的來賓。

## <a name="processing-the-request"></a>處理要求

當您的應用程式傳送要求至 Resource Manager 時，該要求會在主要權杖的身分識別下執行。 主要權杖必須是有效且未到期的。 此權杖必須是來自可以管理訂用帳戶的租用戶。

當要求參考來自其他租用戶的資源時，Resource Manager 會檢查輔助權杖，以判斷是否能處理該要求。 標頭中的所有輔助權杖必須都是有效且未到期的。 如果任何權杖是過期的，則 Resource Manager 會傳回 401 回應碼。 回應包含的用戶端識別碼和租用戶識別碼不是來自有效權杖。 如果輔助標頭包含租用戶的有效權杖，則會處理跨租用戶的要求。

## <a name="next-steps"></a>後續步驟
* 若要了解如何使用 Azure Resource Manager API 傳送驗證要求，請參閱[使用 Resource Manager 驗證 API 來存取訂用帳戶](resource-manager-api-authentication.md)。
* 如需權杖的詳細資訊，請參閱 [Azure Active Directory 存取權杖](/azure/active-directory/develop/access-tokens)。
