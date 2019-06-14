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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322629"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>從現有原則取得簽署金鑰

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會傳回密碼或認證上**取得**或是**清單**作業。 請參閱詳細的說明：如需詳細資訊，請參閱[RBAC 和媒體服務帳戶](rbac-overview.md)

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
