---
title: 設定要使用遠端桌面閘道在 Azure DevTest Labs 中實驗室 |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中設定實驗室，以確保安全存取實驗室的 Vm，而不需要公開的 RDP 連接埠的遠端桌面閘道。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078998"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>若要使用遠端桌面閘道的 Azure DevTest Labs 中設定您的實驗室
在 Azure DevTest Labs 中，您可以設定您的實驗室，以確保能安全存取實驗室的虛擬機器 (Vm) 的遠端桌面閘道，而不需要公開的 RDP 連接埠。 實驗室提供一個集中位置以您的實驗室使用者檢視並連線至他們擁有存取權的所有虛擬機器。 **Connect**按鈕**虛擬機器**頁面會建立您可以開啟以連接至電腦的特定機器的 RDP 檔案。 您可以進一步自訂，並保護您的實驗室連接至遠端桌面閘道的 RDP 連線。 

這種方法是更安全的因為實驗室使用者直接向閘道機器，或可以使用已加入網域的閘道機器上的公司認證來連接到他們的電腦。 實驗室也支援使用權杖型驗證可讓使用者連線到自己的實驗室虛擬機器不需要 RDP 連接埠公開至網際網路的閘道機器。 這篇文章會逐步如何使用權帳驗證來連接到實驗室電腦的實驗室設定的範例。

## <a name="architecture-of-the-solution"></a>解決方案的架構

![解決方案的架構](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. [取得 RDP 檔案內容](/rest/api/dtl/virtualmachines/getrdpfilecontents)當您選取時，會呼叫動作**Connect** button.1。 
1. 取得 RDP 檔案內容動作會叫用`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`來要求驗證權杖。
    1. `{gateway-hostname}` 是閘道主機名稱上指定**實驗室設定**您的實驗室，在 Azure 入口網站中的頁面。 
    1. `{lab-machine-name}` 是您想要連線之電腦的名稱。
    1. `{port-number}` 是設為連線所需要的連接埠。 通常此連接埠是 3389。 如果 VM 使用實驗室[共用 IP](devtest-lab-shared-ip.md) DevTest Labs 中，連接埠中的功能將會不同。
1. 遠端桌面閘道會延後從呼叫`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`Azure 函式來產生驗證權杖。 DevTest Labs 服務會自動包含在要求標頭中的函式金鑰。 函式金鑰是儲存在實驗室的金鑰保存庫。 要顯示該密碼的名稱**閘道語彙基元秘**上**實驗室設定**實驗室的頁面。
1. 預期 Azure 函式會傳回針對至閘道機器憑證式權杖驗證的權杖。  
1. 取得 RDP 檔案內容動作，則會傳回完整的 RDP 檔案，包括驗證資訊。
1. 您開啟 RDP 檔案，使用您慣用的 RDP 連線程式。 請記住，並非所有的 RDP 連線程式支援權杖驗證。 驗證權杖沒有到期日，由函式應用程式設定。 權杖到期之前，請以實驗室 VM 的連接。
1. 一旦遠端桌面閘道機器進行驗證的 RDP 檔案中的權杖時，就會將連線轉寄到您的實驗室電腦中。

### <a name="solution-requirements"></a>解決方案需求
若要使用 DevTest Labs 權杖驗證功能，有幾項組態要求閘道機器、 網域名稱服務 (DNS) 和函式。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>遠端桌面閘道機器的需求
- 必須處理 HTTPS 流量在閘道電腦上安裝 SSL 憑證。 憑證必須符合完整格式的網域名稱 (FQDN)，閘道伺服器陣列或機器本身的 FQDN 的負載平衡器，如果只有一部機器。 萬用字元 SSL 憑證沒有作用。  
- 安裝在閘道電腦上的簽章憑證。 使用建立的簽署憑證[建立 SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)指令碼。
- 安裝[插入式驗證](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)支援權杖驗證的遠端桌面閘道的模組。 這類模組的其中一個範例是`RDGatewayFedAuth.msi`所附[System Center Virtual Machine Manager (VMM) 映像](/system-center/vmm/install-console?view=sc-vmm-1807)。 如需有關 System Center 的詳細資訊，請參閱 < [System Center 文件](https://docs.microsoft.com/system-center/)並[定價詳細資料](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 閘道伺服器可處理對要求`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`。

    閘道主機名稱會是閘道伺服器陣列的負載平衡器的 FQDN 或機器本身的 FQDN，如果只有一部機器。 `{lab-machine-name}`是您想要連線，實驗室機器的名稱和`{port-number}`是會在其進行連線的連接埠。  根據預設，此連接埠是 3389。  不過，如果使用虛擬機器[共用 IP](devtest-lab-shared-ip.md) DevTest Labs 中，連接埠中的功能將會不同。
- [應用程式路由要求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模組的 Internet Information Server (IIS) 可用來重新導向`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`要求至 azure 的函式，用來處理要求，以取得權杖進行驗證。


## <a name="requirements-for-azure-function"></a>Azure 函式的需求
Azure 函式會處理要求，格式為`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}`和傳回驗證權杖根據相同簽署憑證安裝在閘道機器上。 `{function-app-uri}`是用來存取該函式的 uri。 要求的標頭中時，會自動傳遞的函式索引鍵。 函式的範例，請參閱[ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>網路需求

- 在閘道機器上安裝之 SSL 憑證與相關聯的 FQDN 的 DNS 必須將流量導向至閘道機器 」 或 「 閘道機器伺服陣列的負載平衡器。
- 如果實驗室機器使用私人 Ip，必須是從閘道機器的網路路徑到實驗室電腦上，透過共用相同的虛擬網路，或使用對等互連的虛擬網路。

## <a name="configure-the-lab-to-use-token-authentication"></a>設定實驗室，以便使用權杖驗證 
本節說明如何設定要使用支援權杖驗證的遠端桌面閘道器電腦的實驗室。 本章節並未涵蓋如何設定遠端桌面閘道伺服陣列本身。 如需該資訊，請參閱[範例，以建立遠端桌面閘道](#sample-to-create-a-remote-desktop-gateway)這篇文章的最後一節。 

更新實驗室設定之前，請儲存成功地執行函式傳回實驗室的金鑰保存庫中的驗證權杖所需的金鑰。 您可以取得的函式索引鍵值**管理**函式在 Azure 入口網站中的頁面。 如需有關如何將密碼儲存在金鑰保存庫的詳細資訊，請參閱 <<c0> [ 新增至金鑰保存庫的祕密](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)。 將儲存之密碼的名稱，以供稍後使用。

若要尋找實驗室的金鑰保存庫識別碼，請執行下列 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

設定實驗室，以便使用權杖驗證，使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]  ，然後從清單中選取 [DevTest Labs]  。
1. 從實驗室清單中，選取您**實驗室**。
1. 在實驗室的頁面上，選取**組態和原則**。
1. 在左側功能表中，在**設定**區段中，選取**實驗室設定**。
1. 在 **遠端桌面**區段中，輸入完整的網域名稱 (FQDN) 或遠端桌面服務的閘道器電腦的 IP 位址或的伺服器陣列**閘道的主機名稱**欄位。 此值必須符合在閘道機器上使用的 SSL 憑證的 FQDN。

    ![在實驗室設定中的遠端桌面選項](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在**遠端桌面**區段中，如**閘道的語彙基元**祕密，輸入稍早建立之密碼的名稱。 此值不是函式金鑰本身，但在實驗室的保存函式金鑰的 key vault 中祕密的名稱。

    ![在實驗室設定中的閘道語彙基元密碼](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **儲存**變更。

    > [!NOTE] 
    > 依序按一下**儲存**，即表示您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 如需有關遠端閘道的詳細資訊，請參閱 <<c0> [ 歡迎使用 Remote Desktop Services](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果慣用設定透過自動化實驗室，請參閱[組 DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)若要設定的範例 PowerShell 指令碼**閘道的主機名稱**和**閘道語彙基元秘**設定。 [Azure DevTest Labs GitHub 儲存機制](https://github.com/Azure/azure-devtestlab)也提供 Azure Resource Manager 範本，建立或更新的實驗室**閘道的主機名稱**和**閘道語彙基元秘**設定。

## <a name="configure-network-security-group"></a>設定網路安全性群組
若要進一步保護實驗室，網路安全性群組 (NSG) 可以加入實驗室虛擬機器所使用的虛擬網路。 如需相關指示如何設定 NSG，請參閱[建立、 變更或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。

以下是範例只允許先通過閘道傳至連到實驗室機器的流量的 NSG。 此規則中的來源是單一閘道電腦的 IP 位址或閘道機器前方負載平衡器的 IP 位址。

![網路安全性群組-規則](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>若要建立遠端桌面閘道的範例

> [!NOTE] 
> 使用範例範本，即表示您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 如需有關遠端閘道的詳細資訊，請參閱 <<c0> [ 歡迎使用 Remote Desktop Services](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab)提供有助於讓安裝程式的幾個範例使用 DevTest Labs 中的權杖驗證與遠端桌面閘道所需的資源。 這些範例包含閘道機器 」、 「 實驗室設定和 「 函式應用程式的 Azure Resource Manager 範本。

請遵循下列步驟來設定遠端桌面閘道伺服陣列的範例解決方案。

1. 建立簽署的憑證。  執行[建立 SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 儲存憑證指紋、 密碼和 Base64 編碼建立的憑證。
2. 取得 SSL 憑證。 SSL 憑證與相關聯的 FQDN 必須是您所控制的網域。 儲存憑證指紋、 密碼和 Base64 編碼此憑證。 若要取得憑證指紋，使用 PowerShell，請使用下列命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    若要取得使用 PowerShell 將 Base64 編碼，請使用下列命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 從下載的檔案[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)。

    範本需要存取幾個其他 Resource Manager 範本和相關的資源位於相同的基底 URI。 從所有檔案都複製[ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth.msi 至儲存體帳戶中的 blob 容器。  
4. 部署**azuredeploy.json**從[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)。 範本會使用下列參數：
    - adminUsername – 所需。  閘道機器的系統管理員使用者名稱。
    - adminPassword – 所需。 閘道機器的系統管理員帳戶的密碼。
    - instanceCount – 若要建立的閘道機器數目。  
    - alwaysOn – 指出是否要保留建立的 Azure Functions 應用程式處於暖的狀態，或不。 當使用者第一次嘗試連線到其實驗室 VM，但它的確有成本影響因素時，讓 Azure Functions 應用程式可避免延遲。  
    - tokenLifetime – 建立語彙基元就會是有效的時間長度。 格式為 hh: mm:。
    - sslCertificate – Base64 編碼的閘道器電腦的 SSL 憑證。
    - sslCertificatePassword – 在閘道電腦的 SSL 憑證的密碼。
    - sslCertificateThumbprint-SSL 憑證的本機憑證存放區中識別的憑證指紋。
    - signCertificate – Base64 編碼的簽章閘道器電腦的憑證。
    - signCertificatePassword – 簽署憑證，閘道器電腦的密碼。
    - signCertificateThumbprint-簽章憑證的本機憑證存放區中識別的憑證指紋。
    - _artifactsLocation – 可在其中找到所有支援的資源的 URI 位置。 此值必須是完整的 UIR，而非相對路徑。
    - _artifactsLocationSasToken – 用來存取支援的資源，如果位置是 Azure 儲存體帳戶的共用存取簽章 (SAS) 權杖。

    您可以使用下列命令中使用 Azure CLI 部署範本：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    以下是參數的描述：

    - 可以透過執行取得 {儲存體帳戶-端點} `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`。  {儲存體-acct-名稱} 是保留您上傳的檔案儲存體帳戶的名稱。  
    - {容器-名稱} 是 {儲存體-acct-名稱} 容器，顯示包含您上傳的檔案名稱。  
    - 可以透過執行取得 {sas 權杖} `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`。 
        - {儲存體-acct-名稱} 是保留您上傳的檔案儲存體帳戶的名稱。  
        - {容器-名稱} 是 {儲存體-acct-名稱} 容器，顯示包含您上傳的檔案名稱。  
        - {Utc-到期日} 是日期，以 utc 表示的 SAS 權杖即將到期，SAS 權杖不會再用於存取儲存體帳戶。

    記錄 gatewayFQDN 和 gatewayIP 從範本部署輸出的值。 您也需要儲存為新建立的函式，可在函式索引鍵值[函式應用程式設定](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 索引標籤。
5. 設定 DNS，讓該 FQDN 的 SSL 憑證會將導向至 IP 位址的 gatewayIP 從上一個步驟。

    遠端桌面閘道伺服器陣列建立並進行適當的 DNS 更新之後，它已準備好可供在 DevTest Labs 中對實驗室。 **閘道主機名稱**並**語彙基元秘項閘道**設定必須設定為使用您所部署的閘道電腦。 

    > [!NOTE]
    > 如果實驗室機器使用私人 Ip，必須是從閘道機器的網路路徑到實驗室電腦上，透過共用相同的虛擬網路，或使用對等互連的虛擬網路。

    實驗室使用者按一下時，閘道和實驗室設定之後，建立連接檔案**Connect**就會自動包含連線使用權杖驗證所需的資訊。     

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以深入了解遠端桌面服務：[遠端桌面服務文件](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


