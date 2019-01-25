---
title: 使用 Active Directory 同盟服務 (AD FS) 將 Kubernetes 部署至 Azure Stack | Microsoft Docs
description: 了解如何使用 Active Directory 同盟服務 (AD FS) 將 Kubernetes 部署至 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: a161d2223dacb618f1e2eeb6443fffae18cc7259
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354854"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>使用 Active Directory 同盟服務將 Kubernetes 部署至 Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Kubernetes 處於預覽狀態。

您可以遵循本文中的步驟部署及設定 Kubernetes 的資源。 當 Active Directory 同盟服務 (AD FS) 是您的身分識別管理服務時，請使用下列步驟。

## <a name="prerequisites"></a>必要條件 

若要開始使用，請確定您具有適當權限，並且已備妥 Azure Stack。

1. 產生在 Azure Stack 上用來登入 Linux VM 的 SSH 公開和私密金鑰組。 您在建立叢集時需要用到公開金鑰。

    如需產生金鑰的指示，請參閱 [SSH 金鑰產生](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 確認您在 Azure Stack 租用戶入口網站中具有有效的訂用帳戶，且您有足夠的公用 IP 位址可用來新增應用程式。

    叢集無法部署至 Azure Stack 的**系統管理員**訂用帳戶。 您必須使用「**使用者**」訂用帳戶。 

1. 如果您的市集中沒有 Kubernetes 叢集，請連絡您的 Azure Stack 系統管理員。

## <a name="create-a-service-principal"></a>建立服務主體

使用 AD FS 作為您的身分識別解決方案時，需要與您的 Azure Stack 系統管理員一起設定服務主體。 服務主體可讓您的應用程式存取 Azure Stack 資源。

1. 您的 Azure Stack 系統管理員為您提供憑證和服務主體的資訊。 這項資訊應如下所示：

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

2. 將您的新服務主體指派為您訂用帳戶的參與者角色。 如需指示，請參閱[指派角色](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal#assign-role-to-service-principal)。

3. 建立金鑰保存庫以儲存憑證進行部署。

    - 您需要下列幾項資訊：

        | 值 | 說明 |
        | ---   | ---         |
        | Azure Resource Manager 端點 | Microsoft Azure Resource Manager 是可讓系統管理員進行部署、管理及監視 Azure 資源的管理架構。 Azure Resource Manager 能夠以群組方式處理這些工作，而非個別單獨作業的方式。<br>Azure Stack 開發套件 (ASDK) 中的端點為：`https://management.local.azurestack.external/`<br>整合系統中的端點為：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | 您的訂用帳戶識別碼 | [訂用帳戶識別碼](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions)是您存取 Azure Stack 中供應項目的方式。 |
        | 您的使用者名稱 | 您的使用者名稱。 |
        | 資源群組名稱  | 新資源群組的名稱，或選取現有的資源群組。 資源名稱必須是小寫的英數字元。 |
        | KeyVault 名稱 | 保存庫名稱。<br> Regex 模式：`^[a-zA-Z0-9-]{3,24}$` |
        | 資源群組位置 | 資源群組的位置。 這是您選擇用來安裝 Azure Stack 的區域。 |

    - 使用已提升權限的提示字元開啟 PowerShell。 執行下列程式碼，並將參數更新為您的值：

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location local -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. 上傳憑證至金鑰保存庫。

    - 您需要下列幾項資訊：

        | 值 | 說明 |
        | ---   | ---         |
        | 憑證路徑 | 憑證的 FQDN 或檔案路徑。 |
        | 憑證密碼 | 憑證密碼。 |
        | 祕密名稱 | 在上一個步驟中產生的秘密。 |
        | KeyVault 名稱 | 在上一個步驟中建立的金鑰保存庫的名稱。 |
        | Azure Resource Manager 端點 | Azure Stack 開發套件 (ASDK) 中的端點為：`https://management.local.azurestack.external/`<br>整合系統中的端點為：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | 您的訂用帳戶識別碼 | [訂用帳戶識別碼](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions)是您存取 Azure Stack 中供應項目的方式。 |

    - 使用已提升權限的提示字元開啟 PowerShell。 執行下列程式碼，並將參數更新為您的值：

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<keyvault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 開啟 [Azure Stack 入口網站](https://portal.local.azurestack.external)。

1. 選取 [+ 建立資源] > [計算] > [Kubernetes 叢集]。 按一下頁面底部的 [新增] 。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基本概念

1. 在 [建立 Kubernetes 叢集] 中選取 [基本資料]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 選取**訂用帳戶**識別碼。

1. 輸入新資源群組的名稱，或選取現有的資源群組。 資源名稱必須是小寫的英數字元。

1. 選取資源群組的 [位置]。 這是您選擇用來安裝 Azure Stack 的區域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 叢集設定

1. 在 [建立 Kubernetes 叢集] 中選取 [Kubernetes 叢集設定]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 輸入 **Linux VM 系統管理員使用者名稱**。 這是屬於 Kubernetes 叢集和 DVM 的 Linux 虛擬機器的使用者名稱。

1. 輸入用來對建立為 Kubernetes 叢集和 DVM 一部分的所有 Linux 機器進行授權的 **SSH 公開金鑰**。

1. 輸入在區域中具有唯一性的**主要設定檔 DNS 前置詞**。 這必須是區域中唯一的名稱，例如 `k8s-12345`。 根據最佳做法，請試著選擇與資源群組名稱相同的名稱。

    > [!Note]  
    > 對於每個叢集，都應使用全新且唯一的主要設定檔 DNS 前置詞。

1. 選取 [Kubernetes 主要集區設定檔計數]。 此計數包含主要集區中的節點數目。 可能的值介於 1 到 7 之間。 此值應該是奇數。

1. 選取 [Kubernetes 主要 VM 的 VM 大小]。

1. 選取 [Kubernetes 節點集區設定檔計數]。 此計數包含叢集中的代理程式數目。 

1. 選取 [儲存體設定檔]。 您可以選擇 **Blob 磁碟**或**受控磁碟**。 這會指定 Kubernetes 節點 VM 的 VM 大小。 

1. 針對 Azure Stack 安裝的 **Azure Stack 身分識別系統**選取 [ADFS]。

1. 輸入**服務主體 ClientId**。Kubernetes Azure 雲端提供者會使用此識別碼。 當 Azure Stack 系統管理員建立服務主體時，用戶端識別碼已識別為應用程式識別碼。

1. 輸入**金鑰保存庫資源群組**。 

1. 輸入**金鑰保存庫名稱**。

1. 輸入**金鑰保存庫密碼**。

1. 輸入 **Kubernetes Azure 雲端提供者版本**。 這是 Kubernetes Azure 提供者的版本。 Azure Stack 會為每個 Azure Stack 版本發行一個自訂 Kubernetes 組建。

### <a name="3-summary"></a>3.總結

1. 選取總結。 刀鋒視窗會顯示您 Kubernetes 叢集組態設定的驗證訊息。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 檢閱您的設定。

3. 選取 [確定] 以部署叢集。

> [!TIP]  
>  如果您有任何關於部署的問題，可以張貼您的問題，或查看是否有人已在 [Azure Stack 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中回答過此問題。 

## <a name="next-steps"></a>後續步驟

[連線至您的叢集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)