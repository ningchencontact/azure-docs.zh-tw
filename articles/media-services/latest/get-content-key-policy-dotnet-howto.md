---
title: 使用媒體服務 v3 .NET SDK 從現有原則取得簽署金鑰 - Azure | Microsoft Docs
description: 本主題示範如何使用媒體服務 v3 .NET SDK，從現有原則取得簽署金鑰。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322481"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>從現有原則取得簽署金鑰

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 API 不會從**取得**或**列出**作業傳回秘密或認證。 回應中的金鑰一律為 Null、空白或處理過的。 您必須呼叫個別的動作方法，以取得秘密或認證。 在有些 API 會擷取/顯示秘密，而有些 API 不會的情況下，個別的動作可讓您設定不同的 RBAC 安全性權限。 如需如何使用 RBAC 管理存取權的相關資訊，請參閱[使用 RBAC 管理存取權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)。

其範例包括 

* 在取得 StreamingLocator 時不傳回 ContentKey 值、 
* 在取得 ContentKeyPolicy 時不傳回限制金鑰、 
* 不傳回作業的 HTTP 輸入 URL 的查詢字串部分 (以移除簽章)。

本文中的範例示範如何使用 .NET 從現有原則取得簽署金鑰。 
 
## <a name="download"></a>下載 

使用以下命令，將包含完整 .NET 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
具有祕密的 ContentKeyPolicy 範例位於 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) 資料夾中。

## <a name="get-contentkeypolicy-with-secrets"></a>取得具有祕密的 ContentKeyPolicy 

若要取得金鑰，請使用 **GetPolicyPropertiesWithSecretsAsync**，如下面範例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>後續步驟

[設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md) 
