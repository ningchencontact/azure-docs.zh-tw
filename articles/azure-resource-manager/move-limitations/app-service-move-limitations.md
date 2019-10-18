---
title: 移動 Azure App Service 資源
description: 使用 Azure Resource Manager 將 App Service 資源移到新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 04ddf644c58434531dde708ee3b6432b1fce8f91
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533570"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 資源的移動指引

根據您要移動訂用帳戶內的資源還是新的訂用帳戶，移動 App Service 資源的步驟會有所不同。

## <a name="move-in-same-subscription"></a>在相同的訂用帳戶中移動

在_相同訂用帳戶內_移動 Web 應用程式時，您無法移動第三方 SSL 憑證。 不過，您可以只將 Web 應用程式移至新的資源群組，而不移動第三方 SSL 憑證，而且您應用程式的 SSL 功能仍可正常運作。

如果您想在移動 Web 應用程式時一併移動 SSL 憑證，請遵循下列步驟：

1. 從 Web 應用程式中，刪除第三方憑證，但保留一份您的憑證
2. 移動 Web 應用程式。
3. 將第三方憑證上傳至移動的 Web 應用程式。

## <a name="move-across-subscriptions"></a>跨訂用帳戶移動

在_訂用帳戶之間_移動 Web 應用程式時，適用下列限制：

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或已匯的入 SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果 App Service 資源不再位於其原始資源群組中，請將其移回其原始資源群組。 然後，在訂用帳戶之間移動資源。

如果您不記得原始的資源群組，您可以透過診斷來尋找它。 針對您的 web 應用程式，選取 [**診斷並解決問題**]。 然後，選取 [設定**和管理**]。

![選取診斷](./media/app-service-move-limitations/select-diagnostics.png)

選取 [**遷移選項**]。

![選取遷移選項](./media/app-service-move-limitations/select-migration.png)

針對 [建議的步驟] 選取選項，以移動 web 應用程式。

![選取建議的步驟](./media/app-service-move-limitations/recommended-steps.png)

您會在移動資源之前，看到建議採取的動作。 此資訊包括 web 應用程式的原始資源群組。

![建議](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>移動 App Service 憑證

您可以將 App Service 憑證移至新資源群組或訂用帳戶。 如果您的 App Service 憑證是繫結至 Web 應用程式，在將資源移動到新的訂用帳戶之前，還必須多採取幾個步驟。 請在移動資源前，先從 Web 應用程式刪除 SSL 繫結和私用憑證。 不需要刪除 App Service 憑證，只需要刪除 Web 應用程式中的私用憑證即可。

## <a name="move-support"></a>移動支援

若要判斷可以移動的 App Service 資源，請參閱的移動支援狀態：

- [AppService](../move-support-resources.md#microsoftappservice)
- [CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
