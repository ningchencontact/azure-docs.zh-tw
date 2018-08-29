---
title: 驗證即服務 Azure Stack 的測試參數 | Microsoft Docs
description: 有關驗證即服務 Azure Stack 之組態檔和測試進行參數的參考主題。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234517"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>驗證即服務 Azure Stack 的測試參數

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

從驗證即服務 (VaaS) 執行任何「測試套件」之前，請先選取解決方案並建立測試進行。

- 使用已註冊的 VaaS 租用戶認證進行登入。
- 建立新的解決方案 (透過選取 [新增解決方案]) 或選取任何現有的解決方案。
- 從 [測試進行] 工作流程圖格中選取 [開始] 按鈕。

> [!Note]  
> 為您要驗證的每個唯一機器組/硬體設定建立一個新的解決方案項目，並為該機器組上的每個組建部署建立一個新的測試進行。

您將需要輸入在 [測試進行資訊] 頁面中執行測試時所需的參數。 請在開始新的測試進行或驗證執行時，提供這些參數。 大多數參數的值與您部署 Azure Stack 時提供的值相同。

您可以手動輸入[測試參數表格](#test-parameters)中所列的值，或上傳部署組態檔，此檔案包含完整的 Azure Stack 戳記資訊。 上傳之後，入口網站就會從該檔案載入值。

> [!Note]  
> 您可以尋找組態檔並將其載入至入口網站，來搜尋及輸入測試參數值。

## <a name="export-the-test-parameters-using-powershell"></a>使用 PowerShell 來匯出測試參數

1. 登入 DVM 機器或任何能夠存取 Azure Stack 環境的機器。
2. 開啟已提升權限的 PowerShell 視窗，然後執行：

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. 將 **stampinfoproperties.json** 上傳至 VaaS 入口網站。

## <a name="find-the-test-parameters-in-the-configuration-file"></a>在組態檔中尋找測試參數

您也可以開啟 ECE **組態檔**來尋找測試參數值。 您可以在 DVM 機器上的以下路徑中找到該檔案：  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>測試參數 

| 參數    | 說明 |
|-------------|-----------------|
| Azure Stack 組建                      | 已部署的 Azure Stack 組建或版本號碼，例如 1.0.170330.0 | 
| 租用戶識別碼                              | 在 Azure Stack 部署期間所指定的「Azure Active Directory 租用戶」。 請在組態檔中搜尋 **AADTenant**，然後選取 `Id` 標記中的 **GUID** 值。 | 
| 區域                                 | Azure Stack 部署區域。 在組態檔中搜尋 **Region**。 | 
| 租用戶 Resource Manager                | 租用戶 Azure Resource Manager 端點，例如 `https://management.<ExternalFqdn>` | 
| 管理員 Resource Manager                 | 管理員 Azure Resource Manager 端點。 例如，`https://adminmanagement.<ExternalFqdn>` | 
| 外部 FQDN                          | 環境的外部 FQDN。 在組態檔中搜尋 **ExternalFqdn**。 | 
| 租用戶使用者                            | 已經佈建或必須由「Azure AD 目錄」中的「服務管理員」佈建的「Azure Active Directory 租用戶管理員」。 如需有關佈建租用戶帳戶的詳細資料，請參閱[在 Azure Active Directory 中新增 Azure Stack 租用戶帳戶](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad)。 測試會使用此值來執行租用戶層級的作業，例如部署範本來佈建資源 (VM、儲存體帳戶等) 及執行工作負載。 | 
| 服務管理員使用者             | 在 Azure Stack 部署期間所指定「Azure AD 目錄租用戶」的「Azure Active Directory 管理員」。 請在組態檔中搜尋 **AADTenant**，然後選取組態檔之 `UniqueName` 標記中的值。 | 

## <a name="checks-before-starting-the-tests"></a>開始測試前的檢查

測試會執行遠端作業。 執行測試的機器必須能夠存取 Azure Stack 端點。 否則，測試將無法運作。 如果您使用 VaaS 內部部署代理程式，請使用將作為代理程式執行位置的機器。 您可以執行下列檢查來確認您的機器是否能夠存取 Azure Stack 點。

1. 檢查是否能夠連線至「基底 URI」。 開啟 CMD 提示字元或 Bash 殼層，然後執行下列命令：

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 開啟網頁瀏覽器，然後瀏覽至 `https://adminportal.<EXTERNALFQDN>` 以檢查是否能夠連線至「MAS 入口網站」。

3. 使用建立測試進行時所提供的 Azure AD 服務管理員名稱和密碼值來進行登入。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Stack 驗證即服務](https://docs.microsoft.com/azure/azure-stack/partner)。
