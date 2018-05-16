---
title: Azure API 管理原則範例 - 產生共用存取簽章 | Microsoft Docs
description: Azure API 管理原則範例 - 示範如何使用運算式來產生共用存取簽章，並使用 rewrite-uri 原則來將要求轉送到 Azure 儲存體。
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
ms.openlocfilehash: fadad6f83f1a855f3ca1509a6a5e02c822db512d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="generate-shared-access-signature"></a>產生共用存取簽章

本文會說明 Azure API 管理原則範例，示範如何使用運算式來產生[共用存取簽章](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)，並使用 rewrite-uri 原則來將要求轉送到 Azure 儲存體。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入] 區塊。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

