---
title: Azure Stack 驗證即服務中的工作流程通用參數 | Microsoft Docs
description: Azure Stack 驗證即服務的工作流程通用參數
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f66f57799e1e6b6d0e27624e3dc08b4de5d09cac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766194"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Azure Stack 驗證即服務的工作流程通用參數

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

通用參數包括「驗證即服務」(VaaS) 中所有測試所需的值，例如環境變數和使用者認證。 當您建立或變更工作流程時，會在工作流程層級定義這些值。 排定測試時，會將這些值以參數方式傳遞給該工作流程底下的每個測試。

> [!NOTE]
> 每個測試都會定義一組自己的參數。 在排程階段，測試可能會要求您輸入一個不受通用參數影響的值，或可能允許您覆寫通用參數值。

## <a name="environment-parameters"></a>環境參數

環境參數會描述測試中的 Azure Stack 環境。 提供這些值時，必須藉由為您要測試的特定執行個體產生並上傳 Azure Stack 戳記資訊檔案來提供。

> [!NOTE]
> 在正式的驗證工作流程中，建立工作流程之後，即無法修改環境參數。

### <a name="generate-the-stamp-information-file"></a>產生戳記資訊檔案

1. 登入 DVM 或任何能夠存取 Azure Stack 環境的機器。
2. 在提高權限的 PowerShell 視窗中執行下列命令：

    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>在 ECE 設定檔中找出值

您可以手動在位於 DVM 上 `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` 的 **ECE 設定檔**中找出環境參數值。

## <a name="test-parameters"></a>測試參數

通用測試參數包括不能儲存在設定檔中的機密資訊。 這些資訊必須以手動方式提供。

參數    | 說明
-------------|-----------------
租用戶系統管理員使用者                            | 由 AAD 目錄中的服務管理員所佈建的「Azure Active Directory 租用戶系統管理員」。 此使用者會執行租用戶層級的動作，例如部署範本來設定資源 (VM、儲存體帳戶等) 及執行工作負載。 如需有關佈建租用戶帳戶的詳細資料，請參閱[新增 Azure Stack 租用戶](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)。
服務管理員使用者             | 在 Azure Stack 部署期間所指定「AAD 目錄租用戶」的「Azure Active Directory 系統管理員」。 請在 ECE 設定檔中搜尋 `AADTenant`，然後選取 `UniqueName` 元素中的值。
雲端管理員使用者               | Azure Stack 網域管理員帳戶 (例如 `contoso\cloudadmin`)。 請在 ECE 設定檔中搜尋 `User Role="CloudAdmin"`，然後選取 `UserName` 元素中的值。
診斷連接字串          | 「Azure 儲存體帳戶」的 SAS URL，在測試執行期間將會把診斷記錄複製到該處。 如需有關產生 SAS URL 的指示，請參閱[產生診斷連接字串](#generate-the-diagnostics-connection-string)。 |

> [!IMPORTANT]
> **診斷連接字串**必須有效，才能繼續進行操作。

### <a name="generate-the-diagnostics-connection-string"></a>產生診斷連接字串

必須有診斷連接字串，才能在測試執行期間儲存診斷記錄。 請使用在設定期間建立的「Azure 儲存體帳戶」(請參閱[設定驗證即服務資源](azure-stack-vaas-set-up-resources.md)) 來建立共用存取簽章 (SAS) URL，以賦予 VaaS 存取權來將記錄上傳到您的儲存體帳戶。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 從 [允許的服務] 選項中選取 [Blob]。 請將所有其餘選項取消選取。

1. 從 [允許的資源類型] 中，選取 [服務]、[容器]及 [物件]。

1. 從 [允許的權限] 中，選取 [讀取]、[寫入]、[列出]、[新增]、[建立]。 請將所有其餘選項取消選取。

1. 將 [開始時間] 設為目前時間，以及將 [結束時間] 設為目前時間起的三個月。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> SAS URL 會在產生 URL 時所指定的結束時間到期。  
排定測試時，請確定此 URL 的有效期至少為 30 天再加上執行測試所需的時間 (建議值為三個月)。

## <a name="next-steps"></a>後續步驟

- 了解[驗證即服務重要概念](azure-stack-vaas-key-concepts.md)