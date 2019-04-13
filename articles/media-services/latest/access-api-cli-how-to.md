---
title: 存取 Azure 媒體服務 API - Azure CLI | Microsoft Docs
description: 請遵循此方法的步驟存取 Azure 媒體服務 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 1b872c5c2ff0f581300a843650d7434c7c526c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545614"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 存取 Azure 媒體服務 API
 
您應該使用 Azure AD 服務主體驗證來連線至 Azure 媒體服務 API。 您的應用程式必須要求擁有下列參數的 Azure AD 權杖：

* Azure AD 租用戶端點
* 媒体服务资源 URI
* REST 媒體服務的資源 URI
* Azure AD 應用程式值：用戶端識別碼和用戶端祕密

如需詳細資訊，請參閱 <<c0> [ 使用媒體服務 v3 Api 進行開發](media-services-apis-overview.md)。

本主題說明如何使用 Azure CLI 建立 Azure AD 應用程式和服務主體，以及取得必要的值來存取 Azure 媒體服務資源。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>請參閱

- [調整媒體保留單元 - CLI](media-reserved-units-cli-how-to.md)
- [建立媒體服務帳戶 - CLI](./scripts/cli-create-account.md) 
- [重設帳戶認證 - CLI](./scripts/cli-reset-account-credentials.md)
- [建立資產 - CLI](./scripts/cli-create-asset.md)
- [上傳檔案 - CLI](./scripts/cli-upload-file-asset.md)
- [建立轉換 - CLI](./scripts/cli-create-transform.md)
- [建立作業 - CLI](./scripts/cli-create-jobs.md)
- [建立 EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [發佈資產 - CLI](./scripts/cli-publish-asset.md)
- [篩選 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>後續步驟

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
