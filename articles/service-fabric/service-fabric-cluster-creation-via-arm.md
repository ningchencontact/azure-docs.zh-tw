---
title: 從範本建立 Azure Service Fabric 叢集 | Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager、Azure Key Vault 及 Azure Active Directory (Azure AD) 來進行用戶端驗證，在 Azure 中設定安全的 Service Fabric 叢集。
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: ebf4ebd563bab1395f32654bc71955a9416c7a5a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 來建立 Service Fabric 叢集 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
>
>

此逐步指南可逐步引導您使用 Azure Resource Manager 在 Azure 中設定安全的 Azure Service Fabric 叢集。 我們承認本文很長。 不過，除非您已經徹底熟悉內容，否則請務必仔細遵循每個步驟。

本指南涵蓋下列程序：

* 部署 Service Fabric 叢集之前必須注意的重要概念。
* 使用 Service Fabric 資源管理員模組在 Azure 中建立叢集。
* 設定 Azure Active Directory (Azure AD) 以驗證在叢集上執行管理作業的使用者。
* 為您的叢集撰寫自訂 Azure Resource Manager 範本，並進行部署。

## <a name="key-concepts-to-be-aware-of"></a>要注意的重要概念
在 Azure 中，Service Fabric 會授權您使用 x509 憑證來保護您的叢集和其端點。 憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。 針對叢集上的用戶端存取/執行管理作業，包括部署、升級和刪除應用程式、服務和其內含的資料，您可以使用憑證或 Azure Active Directory 認證。 強烈建議您使用 Azure Active Directory，因為這是避免用戶端憑證共享的唯一方式。  如需如何在 Service Fabric 中使用憑證的詳細資訊，請參閱 [Service Fabric 叢集安全性案例][service-fabric-cluster-security]。

Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。 您可以使用 [Key Vault][key-vault-get-started] 來管理 Azure 中 Service Fabric 叢集的憑證。 


### <a name="cluster-and-server-certificate-required"></a>叢集和伺服器憑證 (必要)
需有這些憑證 (一個主要憑證和選擇性次要憑證) 以便保護叢集，並避免未經授權的存取。 它會透過兩種方式提供叢集安全性：

* **叢集驗證：** 驗證叢集同盟的節點對節點通訊。 只有可使用此憑證提供其身分識別的節點可以加入叢集。
* **伺服器驗證：**向管理用戶端驗證叢集管理端點，管理用戶端就能知道它正在交談的對象是真正的叢集，而非「中間人 (man in the middle)」。 此憑證也會為 HTTPS 管理 API 及透過 HTTPS 使用的 Service Fabric Explorer 提供 SSL。

為用於這些用途，憑證必須符合下列要求：

* 憑證必須包含私密金鑰。 通常這些憑證的副檔名為 .pfx 或 .pem  
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換 (.pfx) 檔案。
* **憑證的主體名稱必須與您用來存取 Service Fabric 叢集的網域相符**。 必須如此相符，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法從憑證授權單位 (CA) 取得 *.cloudapp.azure.com 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>設定用戶端驗證用的 Azure Active Directory (選擇性，但建議使用)

Azure AD 可讓組織 (稱為租用戶) 管理使用者對應用程式的存取。 應用程式分成具有 Web 型登入 UI 的應用程式，以及具有原生用戶端體驗的應用程式。 在本文中，我們假設您已經建立租用戶。 如果您尚未建立租用戶，請從閱讀[如何取得 Azure Active Directory 租用戶][active-directory-howto-tenant]開始進行。

Service Fabric 叢集提供其管理功能的各種進入點 (包括 Web 型 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio])。 因此，您將建立兩個 Azure AD 應用程式來控制對叢集的存取：一個 Web 應用程式和一個原生應用程式。

本文件稍後會有設定方式的詳細資訊。

### <a name="application-certificates-optional"></a>應用程式憑證 (選用)
您可以針對應用程式安全性目的，在叢集上安裝任何數目的其他憑證。 在建立您的叢集之前，請考量需要在節點上安裝憑證的應用程式安全性案例，例如：

* 將應用程式組態值加密和解密。
* 在複寫期間將資料跨節點加密。

不論是 Linux 叢集還是 Windows 叢集，建立安全叢集的概念都是相同的。 

### <a name="client-authentication-certificates-optional"></a>用戶端驗證憑證 (選擇性)
您可以針對管理員或使用者用戶端作業指定任意數目的其他憑證。 根據預設，叢集憑證具有管理員用戶端權限。 這些額外的用戶端憑證不應安裝到叢集中，只需要在叢集設定中指定為允許，不過，其必須安裝在用戶端機器上，以連線到叢集並執行任何管理作業。


## <a name="prerequisites"></a>先決條件 
不論是 Linux 叢集還是 Windows 叢集，建立安全叢集的概念都是相同的。 本指南涵蓋使用 azure powershell 或 azure CLI 建立新叢集的方式。 必要條件如下 

-  [Azure PowerShell 4.1 和更新版本][azure-powershell]或 [Azure CLI 2.0 和更新版本][azure-CLI]。
-  您可以在這裡找到 Service Fabric 模組的詳細資料 - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) 和 [az SF CLI 模組](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>使用 Service Fabric RM 模組部署叢集

在本文件中，我們會使用 Service Fabric RM powershell 和 CLI 模組來部署叢集，powershell 或 CLI 模組命令可允許用於多種案例。 我們會一一瀏覽案例。 挑選最符合您的需求的案例。 

- 建立新叢集 - 使用系統產生的自我簽署憑證
    - 使用預設叢集範本
    - 使用既有範本
- 建立新叢集 - 使用您已擁有的憑證
    - 使用預設叢集範本
    - 使用既有範本

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>建立新叢集 - 使用系統產生的自我簽署憑證

如果要系統產生自我簽署的憑證，並使用此憑證來保護您的叢集，請使用下列命令建立叢集。 此命令會設定主要叢集憑證，該憑證可用於叢集安全性，以及用來設定管理員存取權，以便使用該憑證來執行管理作業。

### <a name="login-in-to-azure"></a>登入 Azure。

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>使用模組中隨附的預設 5 Node 1 nodetype 範本來設定叢集

使用下列命令，以最少的參數數目快速建立叢集

所用範本可於 [Azure Service Fabric 範本範例：Windows 範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)上取得

以下命令適用於建立 Windows 和 Linux 叢集，您只需要隨之指定作業系統。 Powershell/ CLI 命令也會在指定的 theCertificateOutputFolder 中輸出憑證，但是請確定已建立憑證資料夾。 此命令也需要其他參數如 VM SKU。

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>使用既有的自訂範本 

如果您需要撰寫自訂範本以符合需求，強烈建議您使用 [Azure Service Fabric 範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)上提供的其中一個範本。 請依照指示和說明在下方章節中[自訂叢集範本][customize-your-cluster-template]。

如果您已有自訂範本，則請再次確認範本及參數檔案中三個與憑證相關的參數，其命名如下所示，且值也如下所示為 Null。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

以下是對等的 CLI 命令，可執行相同動作。 將宣告陳述式中的值變更為適當的值。 CLI 可支援上述 PowerShell 命令所支援的所有其他參數。

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>建立新叢集 - 使用您從 CA 購買或既有的憑證。

如果您有想要用來保護叢集的憑證，請使用下列命令建立叢集。

如果這是 CA 簽署的憑證，而您最後會將其用於其他用途，建議您特別針對金鑰保存庫提供不同的資源群組。 建議您將 Key Vault 放入其自己的資源群組中。 此動作可讓您移除計算和儲存體資源群組 (包括含有 Service Fabric 叢集的資源群組)，而不會遺失您的金鑰和密碼。 **含有您金鑰保存庫的資源群組_必須與正在使用它的叢集位於相同區域_。**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>使用模組中隨附的預設 5 Node 1 nodetype 範本
所用範本可於 [Azure 範例：Windows 範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)上取得

```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>使用既有的自訂範本 
如果您需要撰寫自訂範本以符合需求，強烈建議您使用 [Azure Service Fabric 範本範例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)上提供的其中一個範本。 請依照指示和說明在下方章節中[自訂叢集範本][customize-your-cluster-template]。

如果您已有自訂範本，則請再次確認範本及參數檔案中三個與憑證相關的參數，其命名如下所示，且值也如下所示為 Null。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```PowerShell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword

```

以下是對等的 CLI 命令，可執行相同動作。 將宣告陳述式中的值變更為適當的值。

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>使用指標來指向您已上傳到金鑰保存庫的祕密

若要使用現有的 Key Vault，您「必須將它啟用為可供部署使用」，才能讓計算資源提供者從它取得憑證，然後將它安裝在叢集節點上：

```PowerShell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```
以下是對等的 CLI 命令，可執行相同動作。 將宣告陳述式中的值變更為適當的值。

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>設定用戶端驗用的 Azure Active Directory

Azure AD 可讓組織 (稱為租用戶) 管理使用者對應用程式的存取。 應用程式分成具有 Web 型登入 UI 的應用程式，以及具有原生用戶端體驗的應用程式。 在本文中，我們假設您已經建立租用戶。 如果您尚未建立租用戶，請從閱讀[如何取得 Azure Active Directory 租用戶][active-directory-howto-tenant]開始進行。

Service Fabric 叢集提供其管理功能的各種進入點 (包括 Web 型 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio])。 因此，您將建立兩個 Azure AD 應用程式來控制對叢集的存取：一個 Web 應用程式和一個原生應用程式。

為了簡化與設定 Azure AD 搭配 Service Fabric 叢集相關的一些步驟，我們建立了一組 Windows PowerShell 指令碼。

> [!NOTE]
> 建立叢集之前，您必須先完成下列步驟。 由於指令碼會預期叢集名稱和端點，因此這些值應該是計劃的值，而不是您已經建立的值。

1. [下載指令碼][sf-aad-ps-script-download]到您的電腦。
2. 在 zip 檔案上按一下滑鼠右鍵，選取 [屬性]、選取 [解除封鎖] 核取方塊，然後按一下 [套用]。
3. 解壓縮 zip 檔案。
4. 執行 `SetupApplications.ps1`，並且提供 TenantId、ClusterName 和 WebApplicationReplyUrl 作為參數。 例如︰

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

您可以執行 PowerShell 命令 `Get-AzureSubscription` 來找出您的 TenantId。 執行此命令會顯示每個訂用帳戶的 TenantId。

ClusterName 是用來加在指令碼所建立 Azure AD 應用程式的前面。 它不需要與實際叢集名稱完全相符。 其用意只是要讓您更容易將 Azure AD 構件對應到與之搭配使用的 Service Fabric 叢集。

WebApplicationReplyUrl 是在您的使用者完成登入之後，Azure AD 傳回給他們的預設端點。 請將此端點設定為您叢集的 Service Fabric Explorer 端點，預設為︰

https://&lt;cluster_domain&gt;:19080/Explorer

系統會提示您登入具有 Azure AD 租用戶系統管理權限的帳戶。 在您登入之後，指令碼會建立 Web 和原生應用程式來代表 Service Fabric 叢集。 如果您在 [Azure 入口網站][azure-portal]中查看租用戶的應用程式，則應該會看到兩個新項目︰

   * *ClusterName*\_叢集
   * *ClusterName*\_用戶端

此指令碼會列印您在下一節建立叢集時 Azure Resource Manager 範本所需的 JSON，因此建議讓 PowerShell 視窗保持開啟。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>建立 Service Fabric 叢集 Resource Manager 範本
本節適用於想要撰寫自訂 Service Fabric 叢集 Resource Manager 範本的使用者。 擁有範本之後，您仍可使用 powershell 或 CLI 模組進行部署。 

您可以在 [GitHub 上的 Azure 範例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中取得 Resource Manager 範本範例。 這些範本可以用作叢集範本的起點。

### <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本
本指南使用[五節點安全叢集][service-fabric-secure-cluster-5-node-1-nodetype]範例範本和範本參數。 下載 `azuredeploy.json` 和 `azuredeploy.parameters.json` 到您的電腦並在您最愛的文字編輯器中開啟這兩個檔案。

### <a name="add-certificates"></a>新增憑證
您可以藉由參考包含憑證金鑰的 Key Vault，將憑證新增到叢集 Resource Manager 範本。 在 Resource Manager 範本參數檔案 (azuredeploy.parameters.json) 中新增金鑰保存庫參數和值。 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>將所有憑證都新增到虛擬機器擴展集 osProfile
安裝在叢集中的每個憑證都必須在擴展集資源 (Microsoft.Compute/virtualMachineScaleSets) 的 osProfile 區段中設定妥當。 此動作會指示資源提供者在 VM 上安裝憑證。 此安裝既包含叢集憑證，也包含任何您打算用於應用程式的應用程式安全性憑證︰

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>設定 Service Fabric 叢集憑證
不論是在 Service Fabric 叢集資源 (Microsoft.ServiceFabric/clusters) 中，還是在虛擬機器擴展集資源中虛擬機器擴展集的 Service Fabric 延伸模組中，都必須設定叢集驗證憑證。 這個安排可讓 Service Fabric 資源提供者設定它，以用於管理端點的叢集驗證和伺服器驗證。

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>將憑證資訊新增至虛擬機器擴展集資源：
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>將憑證資訊新增至 Service Fabric 叢集資源：
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>新增 Azure AD 設定以針對用戶端存取使用 Azure AD

您可以藉由參考包含憑證金鑰的金鑰保存庫，將 Azure AD 設定新增到叢集 Resource Manager 範本。 在 Resource Manager 範本參數檔案 (azuredeploy.parameters.json) 中新增 Azure AD 參數和值。

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>將值填入參數檔案中。
最後，請使用 Key Vault 和 Azure AD PowerShell 命令的輸出值來填入參數檔案︰

如果您打算使用 Azure Service Fabric RM PowerShell 模組，且希望系統針對叢集安全性產生自我簽署憑證，則您不需要填入叢集憑證資訊，只需將其保持為 Null。 

> [!NOTE]
> 若要 RM 模組拾取及填入這些空白參數值，參數名稱必須符合下列名稱
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

如果您使用的是應用程式憑證，或已上傳至金鑰保存庫的現有叢集，您需要取得這項資訊並加以填寫 

RM 模組無法為您產生 Azure AD 組態。 因此如果您打算針對用戶端存取使用 Azure AD，則需要加以填寫。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>測試您的範本  
使用下列 PowerShell 命令，以參數檔案來測試 Resource Manager 範本︰

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

為避免遇到問題和收到難解的訊息，您可選擇使用 "-Debug"。

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

下圖說明 Key Vault 和 Azure AD 組態在 Resource Manager 範本中發生作用的位置。

![Resource Manager 相依性對應][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>使用 Azure 資源範本建立叢集 

您現在可以使用本文件先前所述的步驟來部署叢集，或者如果您有參數檔案中的值且已填入，則可準備直接使用 [Azure 資源範本部署][resource-group-template-deploy]來建立叢集。

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

為避免遇到問題和收到難解的訊息，您可選擇使用 "-Debug"。


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>將使用者指派給角色
建立應用程式來代表您的叢集之後，請將使用者指派給 Service Fabric 所支援的角色︰唯讀和系統管理員。您可以使用 [Azure 入口網站][azure-portal]來指派角色。

1. 在 Azure 入口網站右上角，選取您的租用戶。

    ![選取 [租用戶] 按鈕][select-tenant-button]
2. 在左側索引標籤上選取 [Azure Active Directory]，然後選取 [企業應用程式]。
3. 選取 [所有應用程式]，然後尋找並選取 Web 應用程式，其名稱類似 `myTestCluster_Cluster`。
4. 按一下 [使用者和群組] 索引標籤。

    ![使用者和群組索引標籤][users-and-groups-tab]
5. 在新頁面上按一下 [新增使用者] 按鈕，選取使用者和要指派的角色，然後按一下頁面底部的 [選取] 按鈕。

    ![將使用者指派給角色頁面][assign-users-to-roles-page]
6. 按一下頁面底部的 [指派] 按鈕。

    ![新增指派確認][assign-users-to-roles-confirm]

> [!NOTE]
> 如需 Service Fabric 中角色的詳細資訊，請參閱 [角色型存取控制 (適用於 Service Fabric 用戶端)](service-fabric-cluster-security-roles.md)。
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>設定 Azure Active Directory 的疑難排解說明
設定 Azure AD 並加以使用並不容易，因此以下提供一些指標，供您對問題進行偵錯。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer 會提示您選取憑證
#### <a name="problem"></a>問題
在 Service Fabric Explorer 中順利登入 Azure AD 之後，瀏覽器會返回首頁，但是會出現提示您選取憑證的訊息。

![SFX 憑證對話方塊][sfx-select-certificate-dialog]

#### <a name="reason"></a>原因
使用者未獲指派 Azure AD 叢集應用程式中的角色。 因此，Azure AD 驗證在 Service Fabric 叢集上發生失敗。 Service Fabric Explorer 會回復到憑證驗證。

#### <a name="solution"></a>解決方法
請依照設定 Azure AD 的指示進行操作，然後指派使用者角色。 另外，建議您如 `SetupApplications.ps1` 所做的一樣，開啟 [存取應用程式需要使用者指派]。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>使用 PowerShell 進行連線時失敗，發生錯誤：「指定的認證無效」
#### <a name="problem"></a>問題
在您順利登入 Azure AD 之後，於使用 PowerShell 以 “AzureActiveDirectory” 安全性模式連接到叢集時連線失敗，發生錯誤：「指定的認證無效」。

#### <a name="solution"></a>解決方法
此解決方案與前一個相同。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer 在您登入時傳回失敗："AADSTS50011"
#### <a name="problem"></a>問題
當您嘗試在 Service Fabric Explorer 中登入 Azure AD 時，頁面傳回失敗：「AADSTS50011：回覆地址 &lt;url&gt; 與針對應用程式設定的回覆地址不符：&lt;guid&gt;」。

![SFX 回覆地址不相符][sfx-reply-address-not-match]

#### <a name="reason"></a>原因
代表 Service Fabric Explorer 的叢集 (Web) 應用程式嘗試對照 Azure AD 來進行驗證，而它在要求中提供重新導向傳回 URL。 但該 URL 並未列在 Azure AD 應用程式 [回覆 URL] 清單中。

#### <a name="solution"></a>解決方法
在 AAD 頁面中選取 [應用程式註冊]，選取您的叢集應用程式，然後選取 [回覆 URL] 按鈕。 在 [回覆 URL] 頁面上，將 Service Fabric Explorer 的 URL 新增到清單中，或取代清單內的其中一個項目。 完成時，請儲存變更。

![Web 應用程式回覆 url][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>透過 PowerShell 使用 Azure AD 驗證來連接叢集
若要連接 Service Fabric 叢集，請使用下列 PowerShell 命令範例︰

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

若要了解 Connect-ServiceFabricCluster Cmdlet，請參閱 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>我是否可以在多個叢集中重複使用相同的 Azure AD 租用戶？
是。 但是請務必將 Service Fabric Explorer 的 URL 新增到叢集 (Web) 應用程式。 否則 Service Fabric Explorer 無法運作。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>為什麼在已啟用 Azure AD 的情況下仍然需要伺服器憑證？
FabricClient 和 FabricGateway 會執行相互驗證。 在 Azure AD 驗證期間，Azure AD 整合會將用戶端身分識別提供給伺服器，而伺服器憑證則用來驗證伺服器身分識別。 如需有關 Service Fabric 憑證的詳細資訊，請參閱 [X.509 憑證和 Service Fabric][x509-certificates-and-service-fabric]。

## <a name="next-steps"></a>後續步驟
此時，您已具有以 Azure Active Directory 提供管理驗證的安全叢集。 接下來，請[連線到您的叢集](service-fabric-connect-to-secure-cluster.md)並了解如何[管理應用程式密碼](service-fabric-application-secret-management.md)。


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

