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
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: b3513ab2583939943ff188b582f57f49530e5ded
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736250"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱

當您建立 Azure API 管理服務實例時, Azure 會為其指派 azure-api.net 的子域 ( `apim-service-name.azure-api.net`例如)。 不過, 您可以使用自己的自訂功能變數名稱 (例如**contoso.com**) 公開您的 API 管理端點。 本教學課程說明如何將現有的自訂 DNS 名稱對應至 API 管理實例所公開的端點。

> [!WARNING]
> 想要使用憑證釘選以改善應用程式安全性的客戶，必須使用自訂網域名稱 > 與其所管理的憑證，而非預設憑證。 改為釘選預設憑證的客戶 > 會對其所未控制的憑證屬性採用硬式相依性，這不是建議做法。

## <a name="prerequisites"></a>先決條件

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理實例。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
-   您所擁有的自訂網域名稱。 您要使用的自訂網域名稱，必須另外購買並由 DNS 伺服器代管。 本主題不含如何代管自訂網域名稱的指示。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主體或主體別名 (SAN) 必須符合功能變數名稱 (這可讓 API 管理實例透過 SSL 安全地公開 Url)。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 入口網站來設定自訂網域名稱

1. 在[Azure 入口網站](https://portal.azure.com/)中, 流覽至您的 API 管理實例。
1. 選取 [**自訂網域**]。

    有幾個端點可供您指派自訂功能變數名稱。 目前有下列端點可用：

    - **閘道**(預設值為`<apim-service-name>.azure-api.net`:)、
    - **入口網站** (預設值為：`<apim-service-name>.portal.azure-api.net`)、
    - **管理** (預設值為：`<apim-service-name>.management.azure-api.net`)、
    - **SCM** (預設值為：`<apim-service-name>.scm.azure-api.net`)。

    > [!NOTE]
    > 在取用層中, 只有中的**閘道**端點可供設定。
    > 您可以更新所有端點或一部分端點。 客戶通常會更新**閘道**(此 URL 是用來呼叫透過 api 管理公開的 api) 和**入口網站**(開發人員入口網站 URL)。
    > **管理**和**SCM**端點僅供「API 管理」實例擁有者在內部使用, 因此較不常指派自訂功能變數名稱。 進階層支援為**閘道**端點設定多個主機名稱。

1. 選取您想要更新的端點。
1. 在右側視窗中，按一下 [自訂]。

    - 在 [自訂網域名稱] 中，指定您想要使用的名稱。 例如： `api.contoso.com` 。 也支援萬用字元功能變數名稱 (例如, \*. domain.com)。
    - 在 [**憑證**] 中, 選取 Key Vault 的憑證。 您也可以上傳有效的。PFX 檔案, 並提供**密碼**(如果使用密碼保護憑證)。

    > [!TIP]
    > 我們建議使用 Azure Key Vault 來管理憑證, 並將其設定為 autorotate。
    > 如果您使用 Azure Key Vault 來管理自訂網域 SSL 憑證, 請確定憑證是以[_憑證_的形式](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)插入 Key Vault, 而不是_秘密_。
    >
    > 若要提取 SSL 憑證, API 管理必須在包含憑證的 Azure Key Vault 上具有 [取得秘密] 許可權的清單。 使用 Azure 入口網站時, 所有必要的設定步驟都會自動完成。 使用命令列工具或管理 API 時, 必須以手動方式授與這些許可權。 此作業有兩個階段。 首先, 使用 API 管理實例上的 [受控識別] 頁面, 以確定受控識別已啟用, 並記下該頁面上顯示的主體識別碼。 第二, 在包含憑證的 Azure Key Vault 上, 授與許可權清單並取得此主體識別碼的秘密許可權。
    >
    > 如果憑證設定為 autorotate, 則 API 管理會自動取得最新版本, 而不會對服務造成任何停機 (如果您的 API 管理層有 SLA-----在所有層級中, 而開發人員層除外)。

1. 按一下 [套用]。

    > [!NOTE]
    > 指派憑證的流程可能需要 15 分鐘以上，視部署大小而定。 開發人員 SKU 會停機，而基本和更高版本的 SKU 則不會停機。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 組態

設定自訂功能變數名稱的 DNS 時, 您有兩個選項:

- 設定指向已設定自訂功能變數名稱之端點的 CNAME 記錄。
- 設定指向您 API 管理閘道 IP 位址的 A 記錄。

> [!NOTE]
> 雖然 API 管理實例的 IP 位址是靜態的, 但在少數情況下可能會變更。 基於此原因, 建議您在設定自訂網域時使用 CNAME。 選擇 DNS 設定方法時, 請將這一點納入考慮。 如需詳細資訊, 請參閱[API MANANAGEMENT 常見問題](https://docs.microsoft.com/azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)。

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
