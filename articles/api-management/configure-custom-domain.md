---
title: 設定 Azure API 管理執行個體的自訂網域名稱 | Microsoft Docs
description: 本主題描述如何設定 Azure API 管理執行個體的自訂網域名稱。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: a771b437258046f937b97a9e37ffedbe0a17c1c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693584"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱 

當您建立 API 管理 (APIM) 執行個體時，Azure 會將它指派至 azure-api.net 的子網域 (例如，`apim-service-name.azure-api.net`)。 不過，您可以使用自己的網域名稱 (例如 **contoso.com**) 來公開您的 APIM 端點。 本教學課程會示範如何將現有的自訂 DNS 名稱對應至 Azure API 管理執行個體所公開的端點。

> [!WARNING]
> 想要使用憑證釘選以改善應用程式安全性的客戶，必須使用自訂網域名稱 > 與其所管理的憑證，而非預設憑證。 改為釘選預設憑證的客戶 > 會對其所未控制的憑證屬性採用硬式相依性，這不是建議做法。

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 您所擁有的自訂網域名稱。 您要使用的自訂網域名稱，必須另外購買並由 DNS 伺服器代管。 本主題不含如何代管自訂網域名稱的指示。
+ 您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主體或主體別名 (SAN) 必須符合網域名稱 (這可讓 APIM 安全地透過 SSL 公開 URL)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 入口網站來設定自訂網域名稱

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您的 APIM 執行個體。
1. 選取 [自訂網域和 SSL]。
    
    可以为许多终结点分配自定义域名。 目前有下列端點可用： 
   + **Proxy** (預設值為：`<apim-service-name>.azure-api.net`)、 
   + **入口網站** (預設值為：`<apim-service-name>.portal.azure-api.net`)、     
   + **管理** (預設值為：`<apim-service-name>.management.azure-api.net`)、 
   + **SCM** (預設值為：`<apim-service-name>.scm.azure-api.net`)。

     >[!NOTE]
     > 您可以更新所有端點或一部分端點。 客戶通常會更新 **Proxy** (此 URL 用來呼叫透過 API 管理公開的 API) 和**入口網站** (開發人員入口網站 URL)。 **管理**和 **SCM** 端點供 APIM 客戶在內部使用，因此很少會指派自訂網域名稱。

1. 選取您想要更新的端點。 
1. 在右側視窗中，按一下 [自訂]。

   + 在 [自訂網域名稱] 中，指定您想要使用的名稱。 例如： `api.contoso.com`。 也支援萬用字元網域名稱 (例如，*.domain.com)。
   + 在**证书**中，从密钥保管库中选择证书。 如果证书受密码保护，你还可以上传有效的 .PFX 文件并提供其**密码**。

     > [!TIP]
     > 如果使用 Azure 密钥保管库来管理自定义域 SSL 证书，请确保该证书[作为证书](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)而不是机密插入到密钥保管库中。 如果证书设置为“自动轮换”，API 管理会自动选取最新版本。

1. 按一下 [套用]。

    >[!NOTE]
    >指派憑證的流程可能需要 15 分鐘以上，視部署大小而定。 開發人員 SKU 會停機，而基本和更高版本的 SKU 則不會停機。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
