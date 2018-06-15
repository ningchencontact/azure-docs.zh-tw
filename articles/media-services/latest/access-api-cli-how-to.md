---
title: 存取 Azure 媒體服務 API - CLI 2.0 | Microsoft Docs
description: 請遵循此方法的步驟存取 Azure 媒體服務 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 9295c3f9dfabc8ef7749758e926df443843720a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639770"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>使用 CLI 2.0 存取 Azure 媒體服務 API
 
您應該使用 Azure AD 服務主體驗證來連線至 Azure 媒體服務 API。 您的應用程式必須要求擁有下列參數的 Azure AD 權杖：

* Azure AD 租用戶端點
* 媒體服務資源 URI
* REST 媒體服務的資源 URI
* Azure AD 應用程式值：用戶端識別碼和用戶端祕密

本主題說明如何使用 CLI 2.0 建立 Azure AD 應用程式和服務主體，以及取得必要的值來存取 Azure 媒體服務資源。

## <a name="prerequisites"></a>先決條件 

建立新的 Azure 媒體服務帳戶，如[此快速入門](create-account-cli-quickstart.md)所述。

## <a name="log-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](http://portal.azure.com)，然後啟動 **CloudShell** 來執行 CLI 命令，如後續步驟所示。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 CLI，本主題需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [串流處理檔案](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>另請參閱

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
