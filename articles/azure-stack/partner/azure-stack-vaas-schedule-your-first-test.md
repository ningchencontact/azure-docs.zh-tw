---
title: 使用 Azure Stack 入口網站的「驗證即服務」來排程第一個測試 | Microsoft Docs
description: 使用 Azure Stack 入口網站的「驗證即服務」來排程第一個測試。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8ff78f46283d3ee4baa7310aa56bcdabde85f2cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159617"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>快速入門：使用「驗證即服務」入口網站來排程第一個測試

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用驗證即服務 (VaaS) 入口網站排程第一個測試，以檢查您的硬體。 必須先將本機代理程式部署在要驗證的 Azure Stack 解決方案上，才能執行驗證測試。

在本快速入門中，您將新增解決方案並執行測試。

## <a name="prerequisites"></a>必要條件

在您依照本快速入門操作之前，您必須要有：
 - 驗證即服務帳戶。 如需指示，請參閱[設定您的驗證即服務帳戶](azure-stack-vaas-set-up-account.md)。  
- 在系統上安裝本機代理程式。 如需指示，請參閱[部署本機代理程式並測試虛擬機器](azure-stack-vaas-test-vm.md)。

## <a name="add-a-new-solution"></a>新增解決方案

1. 登入[驗證入口網站](https://azurestackvalidation.com)。

    ![登入驗證入口網站](media/vaas_portalsignin.png)  

2. 選取 [新增解決方案]。
3. 輸入解決方案的名稱，然後選取 [儲存]。

## <a name="create-a-solution-validation-workflow"></a>建立解決方案驗證工作流程

1. 選取解決方案名稱。
2. 選取 [解決方案驗證] 圖格上的 [管理]。

    ![解決方案驗證](media/image2.png)

## <a name="create-a-solution-workflow"></a>建立解決方案工作流程

1. 選取 [新增解決方案驗證]。
2. 輸入驗證的名稱。
3. 選取 [最小值] 或 [最大值]。  
    - **最低**  
    以支援的節點數目下限設定解決方案。  
    - **最大值**  
    以支援的節點數目上限設定解決方案。
4. 新增您的環境參數。 如需詳細資訊，請參閱[新增環境參數](#add-environmental-parameters)。
5. 新增您的一般測試參數。 如需詳細資訊，請參閱[新增一般測試參數](#add-common-test-parameters)。

    根據測試定義，在測試時您可能需要輸入一般參數以外的值，或者，您可以覆寫一般參數值。
6. 按一下 [提交] 以排程測試。

## <a name="add-environmental-parameters"></a>新增環境參數

新增下列環境參數：

| 測試進行資訊 | 必要 | 說明 |
| --- | --- | --- | --- |
| Azure Stack 組建 | 必要 | Azure Stack 組建編號 (例如 20170501.1) 值必須是有效的 Azure Stack 組建編號或版本，例如 1.0.170330.9 |
| 租用戶識別碼 | 必要 | Active Directory 租用戶識別碼。 這必須是 GUID (例如 ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| 區域 | 必要 | Azure Stack 部署區域 |
| 租用戶 Resource Manager 端點 | 必要 | 租用戶 Azure Resource Manager 作業的端點 (例如 https://management.loc-ext.domain.com) |
| 管理員 Resource Manager 端點 | 不需要 | 租用戶 Azure Resource Manager 作業的端點 (例如 https://management.loc-ext.domain.com) |
| 外部 FQDN | 不需要 | 用來作為端點尾碼的外部完整網域名稱 (FQDN)。 (例如 local.azurestack.external 或 redmond.contoso.com) |
| 節點數目 | 必要 | 解決方案中的節點數目。 |

## <a name="add-common-test-parameters"></a>新增一般測試參數

新增下列一般測試參數：

| 測試進行資訊 | 必要 | 說明 |
| --- | --- | --- |
| 租用戶使用者名稱 | 必要 | 租用戶使用者名稱 (例如 tenant@contoso.onmicrosoft.com) |
| 租用戶密碼 | 必要 | 租用戶的密碼。 |
| 服務管理員使用者名稱 | 不需要 | 租用戶使用者名稱 (例如 tenant@contoso.onmicrosoft.com) |
| 服務管理員密碼 | 不需要 | 服務管理員使用者名稱 (例如 serviceadmin@contoso.onmicrosoft.com) |
| 雲端管理員使用者名稱 | 不需要 | Azure Stack 網域管理員帳戶 (例如 contoso\cloudadmin) |
| 雲端管理員密碼 | 不需要 | |
|  診斷連接字串 | 不需要 | Azure 儲存體帳戶的 SAS URI，在測試執行期間將會把診斷記錄複製到該處。 請參閱[建立用來儲存記錄的 Azure 儲存體 Blob](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)。 <br><br>**診斷連接字串**一般參數的值將會由服務儲存，並在排程時提供給工作流程中所有使用此參數的測試。 當 SAS URL 即將在 30 天內到期時，系統會提示您在一般參數頁面上提供新的 SAS URL。 |
| 標記 - 名稱 | 不需要 |  可以輸入描述性標記以標示工作流程。 這是標記的名稱。 |
| 標記 - 值 | 不需要 | 可以輸入描述性標記以標示工作流程。 這是標記的值。 |

## <a name="next-steps"></a>後續步驟

- [驗證新的 Azure Stack 解決方案](azure-stack-vaas-validate-solution-new.md)  
- 深入了解 [Azure Stack 驗證即服務](https://docs.microsoft.com/azure/azure-stack/partner)。
