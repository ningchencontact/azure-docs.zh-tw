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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612894"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 存取 Azure 媒體服務 API
 
您應該使用 Azure AD 服務主體驗證來連線至 Azure 媒體服務 API。 您的應用程式必須要求擁有下列參數的 Azure AD 權杖：

* Azure AD 租用戶端點
* 媒體服務資源 URI
* REST 媒體服務的資源 URI
* Azure AD 應用程式值：用戶端識別碼和用戶端祕密

本主題說明如何使用 Azure CLI 建立 Azure AD 應用程式和服務主體，以及取得必要的值來存取 Azure 媒體服務資源。

## <a name="prerequisites"></a>必要條件 

- 在本機安裝和使用 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，並非所有[媒體服務 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中運作。 建議在本機使用 CLI。

- [建立媒體服務帳戶](create-account-cli-how-to.md)。

    請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
