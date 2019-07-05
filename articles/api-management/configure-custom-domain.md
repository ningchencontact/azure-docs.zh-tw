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
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509304"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱

當您建立 「 Azure API 管理服務執行個體時，Azure 將它指派 azure-api.net 的子網域 (例如`apim-service-name.azure-api.net`)。 不過，您可以公開使用自訂網域名稱，例如您 API 管理端點**contoso.com**。 本教學課程會示範如何將現有的自訂 DNS 名稱對應至 API 管理執行個體所公開的端點。

> [!WARNING]
> 想要使用憑證釘選以改善應用程式安全性的客戶，必須使用自訂網域名稱 > 與其所管理的憑證，而非預設憑證。 改為釘選預設憑證的客戶 > 會對其所未控制的憑證屬性採用硬式相依性，這不是建議做法。

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
-   您所擁有的自訂網域名稱。 您要使用的自訂網域名稱，必須另外購買並由 DNS 伺服器代管。 本主題不含如何代管自訂網域名稱的指示。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主體別名 (SAN) 必須符合網域名稱 （這可讓 API 管理執行個體，以安全地透過 SSL 公開 Url）。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 入口網站來設定自訂網域名稱

1. 瀏覽至您的 API 管理執行個體中[Azure 入口網站](https://portal.azure.com/)。
1. 選取 [自訂網域和 SSL]  。

    有許多您可以對其指派的自訂網域名稱的端點。 目前有下列端點可用：

    - **Proxy** (預設值為：`<apim-service-name>.azure-api.net`)、
    - **入口網站** (預設值為：`<apim-service-name>.portal.azure-api.net`)、
    - **管理** (預設值為：`<apim-service-name>.management.azure-api.net`)、
    - **SCM** (預設值為：`<apim-service-name>.scm.azure-api.net`)。

    > [!NOTE]
    > 您可以更新所有端點或一部分端點。 客戶通常會更新 **Proxy** (此 URL 用來呼叫透過 API 管理公開的 API) 和**入口網站** (開發人員入口網站 URL)。 **管理**並**SCM**端點的 API 管理執行個體擁有者只會在內部使用，因此較不常指派的自訂網域名稱。 在大部分情況下，可以指定端點設定只能在單一的自訂網域名稱。 不過， **Premium**層支援設定多個主機名稱**Proxy**端點。

1. 選取您想要更新的端點。
1. 在右側視窗中，按一下 [自訂]  。

    - 在 [自訂網域名稱]  中，指定您想要使用的名稱。 例如： `api.contoso.com` 。 萬用字元網域名稱 (例如\*。 domain.com) 也支援。
    - 在 **憑證**，從金鑰保存庫中選取憑證。 您也可以上傳有效的。PFX 檔案，並提供其**密碼**，如果憑證受密碼保護。

    > [!TIP]
    > 我們建議使用 Azure 金鑰保存庫的管理憑證，並將它們設定為 autorotate。
    > 如果您使用 Azure Key Vault 來管理自訂網域 SSL 憑證時，請確定憑證插入 Key Vault[作為_憑證_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)，而非_祕密_。
    >
    > 若要擷取的 SSL 憑證，API 管理必須清單取得祕密權限在 Azure 金鑰保存庫，包含憑證上。 使用 Azure 入口網站時就會自動完成所有必要的設定步驟。 使用命令列工具或管理 API 時，必須以手動方式授與這些權限。 此作業有兩個階段。 首先，使用受控身分識別的頁面在您的 API 管理執行個體上，確定已啟用受控身分識別，並記下該頁面上顯示的主體識別碼。 其次，授與權限清單，並取得 Azure 金鑰保存庫，包含憑證的祕密權限，此主體的識別碼。
    >
    > 如果憑證設定為 autorotate，API 管理將會挑選最新版本自動而不需要停機的服務 （如果您的 API 管理層有 SLA-i.e.除了開發人員層的所有層中）。

1. 按一下 [套用]。

    > [!NOTE]
    > 指派憑證的流程可能需要 15 分鐘以上，視部署大小而定。 開發人員 SKU 會停機，而基本和更高版本的 SKU 則不會停機。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
