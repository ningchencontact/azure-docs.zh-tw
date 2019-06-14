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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779659"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 存取 Azure 媒體服務 API
 
若要使用 Azure AD 服務主體驗證來連接到 Azure 媒體服務 API，您的應用程式必須要求具有下列參數的 Azure AD 權杖：

* Azure AD 租用戶端點
* 媒體服務資源 URI
* REST 媒體服務的資源 URI
* Azure AD 應用程式值：用戶端識別碼和用戶端祕密

如需詳細說明，請參閱 <<c0> [ 存取媒體服務 v3 Api](media-services-apis-overview.md#accessing-the-azure-media-services-api)。

本主題說明如何使用 Azure CLI 建立 Azure AD 應用程式和服務主體，以及取得必要的值來存取 Azure 媒體服務資源。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>請參閱

- [調整媒體保留單元 - CLI](media-reserved-units-cli-how-to.md)
- [建立媒體服務帳戶 - CLI](create-account-cli-how-to.md) 
- [重設帳戶認證 - CLI](cli-reset-account-credentials.md)
- [建立資產 - CLI](cli-create-asset.md)
- [上傳檔案 - CLI](cli-upload-file-asset.md)
- [建立轉換 - CLI](cli-create-transform.md)
- [使用自訂的轉換-CLI 進行編碼](custom-preset-cli-howto.md)
- [建立作業 - CLI](cli-create-jobs.md)
- [建立 EventGrid - CLI](job-state-events-cli-how-to.md)
- [發佈資產 - CLI](cli-publish-asset.md)
- [篩選 - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

您想要串流內容的串流端點必須處於執行中狀態。 下列 CLI 命令會啟動您的預設資料流端點：

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

