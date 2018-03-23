---
title: 使用 Azure Automation DSC 來部署 Site Recovery 行動服務 | Microsoft Docs
description: 說明如何使用 Azure Automation DSC，將 Azure Site Recovery 行動服務和 VMware VM 及實體伺服器複寫的 Azure 代理程式自動部署到 Azure
services: site-recovery
author: krnese
manager: lorenr
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: krnese
ms.openlocfilehash: 0bf1b551ba578cd152201c131bd60470bdc9d86a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc"></a>使用 Azure 自動化 DSC 部署行動服務

您使用 [Azure Site Recovery](site-recovery-overview.md)，在您想要複寫到 Azure 的 VMware VM 和實體伺服器上部署行動服務。

本文說明如何使用 Azure 自動化 Desired State Configuration (DSC)，在 Windows 機器上部署服務，以確保：


## <a name="prerequisites"></a>先決條件

* 用來儲存必要安裝程式的儲存機制
* 用來儲存必要複雜密碼以向管理伺服器註冊的存放庫。 系統會針對特定組態伺服器產生唯一複雜密碼。 
* Windows Management Framework (WMF) 5.0 應該安裝在您要啟用保護功能的機器上。 這是自動化 DSC 的需求。
如果您想要針對已安裝 WMF 4.0 的 Windows 機器使用 DSC，請參閱[在中斷連線的環境中使用 DSC](## Use DSC in disconnected environments)。
 

## <a name="extract-binaries"></a>擷取二進位檔

行動服務可以透過命令列來安裝並接受數個引數。 您需要取得二進位檔 (在從您的設定擷取它們之後)，並將它們儲存在您能夠使用 DSC 組態進行擷取的地方。

1. 若要擷取此設定所需的檔案，請瀏覽至管理伺服器上的下列目錄︰**\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**
2. 在這個資料夾中，確認有一個名稱如下的 MSI 檔案：**Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**。
3. 使用下列命令來擷取安裝程式：**.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. 選取所有檔案並將它們傳送到壓縮的資料夾。

您現在已擁有使用 Automation DSC 來自動執行行動服務設定所需的二進位檔。

## <a name="store-the-passphrase"></a>儲存複雜密碼

您必須決定要放置此壓縮資料夾的位置。 您可以使用 Azure 儲存體帳戶 (如稍後所示) 來儲存設定所需的複雜密碼。 接著，代理程式會在此過程中向管理伺服器註冊。

1. 將您在部署組態伺服器時取得的複雜密碼儲存在文字檔中 - passphrase.txt。
2. 請將壓縮的資料夾和複雜密碼放在 Azure 儲存體帳戶裡的專用容器中。


如果您偏好將這些檔案保留在您網路中的共用上，您也可以這樣做。 您只需要確保稍後將會使用的 DSC 資源具有存取權，而且可以取得設定和複雜密碼。

## <a name="create-the-dsc-configuration"></a>建立 DSC 組態

此設定需倚賴 WMF 5.0。 為了讓機器能夠順利透過 Automation DSC 套用組態，必須要有 WMF 5.0。

此環境使用下列範例 DSC 組態︰

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
此組態將會執行下列各項操作：

* 變數會告訴組態可在哪裡取得行動服務和 Azure VM 代理程式的二進位檔、可在哪裡取得複雜密碼，以及可在哪裡儲存輸出。
* 組態會匯入 xPSDesiredStateConfiguration DSC 資源，以便讓您可以使用 `xRemoteFile` 從儲存機制下載檔案。
* 組態會建立您想要用來儲存二進位檔的目錄。
* 封存資源會將從壓縮的資料夾中擷取檔案。
* 套件 Install 資源會使用特定的引數從 UNIFIEDAGENT.EXE 安裝程式安裝行動服務。 (您必須變更建構引數的變數以反映您的環境)。
* 套件 AzureAgent 資源會安裝 Azure VM 代理程式，這是針對在 Azure 中執行的每部 VM 建議安裝的代理程式。 Azure VM 代理程式也可讓您在容錯移轉之後，在 VM 上新增擴充功能。
* 服務資源將會確保相關的行動服務與 Azure 服務一律都處於執行狀態。

將組態儲存為 **ASRMobilityService**。

> [!NOTE]
> 請記得取代組態中的 CSIP 以反映實際的管理伺服器，以便代理程式能夠正確連線並使用正確的複雜密碼。
>
>

## <a name="upload-to-automation-dsc"></a>上傳至 Automation DSC
由於您所產生的 DSC 組態會匯入必要的 DSC 資源模組 (xPSDesiredStateConfiguration)，因此在上傳 DSC 組態之前，您必須先在 Automation 中匯入該模組。

1. 登入您的「自動化」帳戶、瀏覽至 [資產]  >  [模組]，然後按一下 [瀏覽資源庫]。
2. 搜尋模組並將它匯入您的帳戶中。
3. 完成此動作之後，請前往已安裝 Azure Resource Manager 模組的機器，然後繼續匯入新建立的 DSC 組態。

## <a name="import-cmdlets"></a>匯入 Cmdlet

1. 在 PowerShell 中，登入您的 Azure 訂用帳戶。
2. 修改 Cmdlet 以反映您的環境，並擷取變數中的「自動化」帳戶資訊：

    ```powershell
    $AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
    ```

3. 使用下列 Cmdlet 將組態上傳至 Automation DSC︰

    ```powershell
    $ImportArgs = @{
        SourcePath = 'C:\ASR\ASRMobilityService.ps1'
        Published = $true
        Description = 'DSC Config for Mobility Service'
    }
    $AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
    ```

## <a name="compile-the-configuration-in-automation-dsc"></a>在 Automation DSC 中編譯組態

1. 在 Automation DSC 中編譯組態，以便開始向它註冊節點。 您將透過執行下列 Cmdlet 來達成目的：

    ```    powershell
    $AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
    ```

這可能需要幾分鐘的時間，因為您基本上是將組態部署到裝載的 DSC 提取服務。

2. 編譯組態之後，您可以使用 PowerShell (Get-AzureRmAutomationDscCompilationJob) 或使用 [Azure 入口網站](https://portal.azure.com/)來擷取作業資訊。

    ![擷取作業](./media/vmware-azure-mobility-deploy-automation-dsc/retrieve-job.png)

您現在已成功發佈 DSC 組態並將它上傳到 Automation DSC。

## <a name="onboard-machines-to-automation-dsc"></a>讓機器在 Automation DSC 上線


1. 請確定您的 Windows 機器會以最新版的 WMF 進行更新。 您可以從 [下載中心](https://www.microsoft.com/download/details.aspx?id=50395)下載並安裝適用於您平台的正確版本。
2. 為 DSC 建立將套用至節點的 metaconfig。 若要順利完成此作業，您必須針對在 Azure 中選取的自動化帳戶擷取端點 URL 和主要金鑰。 您可以在「自動化」帳戶 [所有設定] 刀鋒視窗上的 [金鑰] 底下找到這些值。

    ![金鑰值](./media/vmware-azure-mobility-deploy-automation-dsc/key-values.png)

在此範例中，您有一部想要使用 Site Recovery 來保護的 Windows Server 2012 R2 實體伺服器。

## <a name="check-for-any-pending-file-rename-operations"></a>檢查是否有任何擱置的檔案重新命名作業

在您開始將伺服器與 Automation DSC 端點建立關聯之前，建議您檢查登錄中是否有任何擱置的檔案重新命名作業。 因為若有擱置的重新開機，它們可能會讓設定無法完成。

1. 執行下列 Cmdlet 以確認伺服器上沒有擱置的重新開機︰

    ```powershell
    Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
    ```
2. 如果呈現空白，您就可以繼續作業。 若非如此，您必須在維護期間重新啟動伺服器以解決此問題。
3. 若要在伺服器上套用組態，請啟動「Windows PowerShell 整合式指令碼環境」(ISE) 並執行下列指令碼。 這基本上是 DSC 本機組態，此組態會指示「本機組態管理員」引擎向 Automation DSC 服務註冊並擷取特定的組態 (ASRMobilityService.localhost)。

    ```powershell
    [DSCLocalConfigurationManager()]
    configuration metaconfig {
        param (
            $URL,
            $Key
        )
        node localhost {
            Settings {
                RefreshFrequencyMins = '30'
                RebootNodeIfNeeded = $true
                RefreshMode = 'PULL'
                ActionAfterReboot = 'ContinueConfiguration'
                ConfigurationMode = 'ApplyAndMonitor'
                AllowModuleOverwrite = $true
            }

            ResourceRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }

            ConfigurationRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
                ConfigurationNames = 'ASRMobilityService.localhost'
            }

            ReportServerWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }
        }
    }
    metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'
    
    Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
    ```

此組態會導致「本機組態管理員」引擎向 Automation DSC 註冊它自己。 它也會決定引擎應有的運作方式、如果有組態漂移 (ApplyAndAutoCorrect) 的情況應該如何處理，以及如果必須重新開機，應該如何繼續進行組態設定。

1. 執行此指令碼之後，節點應該就會開始向 Automation DSC 註冊。

    ![進行中的節點註冊](./media/vmware-azure-mobility-deploy-automation-dsc/register-node.png)

2. 如果您返回 Azure 入口網站，便可以看到新註冊的節點現已出現在入口網站中。

    ![入口網站中的已註冊節點](./media/vmware-azure-mobility-deploy-automation-dsc/registered-node.png)

3. 在伺服器上，您可以執行下列 PowerShell Cmdlet 來確認是否已正確註冊節點︰

    ```powershell
    Get-DscLocalConfigurationManager
    ```

4. 提取此組態並套用到伺服器之後，您可以透過執行下列 Cmdlet 來進行確認︰

    ```powershell
    Get-DscConfigurationStatus
    ```

輸出會顯示伺服器已成功提取其組態︰

![輸出](./media/vmware-azure-mobility-deploy-automation-dsc/successful-config.png)

此外，行動服務設定有自己的記錄檔，您可在 SystemDrive\ProgramData\ASRSetupLogs 找到該記錄檔。

就這麼簡單！ 您現在已在想要使用 Site Recovery 來保護的機器上成功部署和註冊行動服務。 DSC 將會確保所需的服務一律都處於執行狀態。

![部署成功](./media/vmware-azure-mobility-deploy-automation-dsc/successful-install.png)

在管理伺服器偵測到部署成功之後，您可以使用 Site Recovery 在機器上設定保護及啟用複寫。

## <a name="use-dsc-in-disconnected-environments"></a>在中斷連線的環境中使用 DSC

如果您的機器未連線到網際網路，您仍然可依賴 DSC 在您想要保護的工作負載上部署和設定行動服務。

您可以在您的環境中將自己的 DSC 具現化，以基本上提供與從 Automation DSC 取得之功能相同的功能。 亦即，用戶端會將組態 (在註冊後) 提取到 DSC 端點。 不過，另一個選項是將 DSC 組態手動推送到您的機器 (不論是在本機還是遠端)。

請注意，在此範例中，為電腦名稱新增了一個參數。 遠端檔案現在位於您想要保護之機器應可存取的遠端共用上。 指令碼的最後會頒布組態，然後開始將 DSC 組態套用到目標電腦。

### <a name="prerequisites"></a>先決條件
確定已安裝 xPSDesiredStateConfiguration PowerShell 模組。 針對已安裝 WMF 5.0 的 Windows 電腦，您可以在目標機器上執行下列 Cmdlet 來安裝 xPSDesiredStateConfiguration 模組：

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

如果您需要將此模組散佈到具有 WMF 4.0 的 Windows 機器，您也可以下載並儲存此模組。 請在具有 PowerShellGet (WMF 5.0) 的機器上執行下列 Cmdlet︰

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

此外，針對 WMF 4.0，請確定機器上已安裝 [Windows 8.1 更新 KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) 。

下列組態可以推送到具有 WMF 5.0 和 WMF 4.0 的 Windows 機器：

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

如果您想要在公司網路上將自己的 DSC 提取伺服器具現化，以模擬您可以從 Automation DSC 取得的相同功能，請參閱 [設定 DSC Web 提取伺服器](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396)。

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>選擇性：使用 Azure Resource Manager 範本來部署 DSC 組態
本文的重點在於如何建立您自己的 DSC 組態，以自動部署行動服務和 Azure VM 代理程式，並確保它們在您想要保護的電腦上執行。 我們也有會將此 DSC 組態部署到新的或現有 Automation DSC 帳戶的 Azure Resource Manager 範本。 此範本會使用輸入參數來建立將包含您環境之變數的「自動化」資產。

部署範本之後，您可以直接參考本指南中的步驟 4 來讓您的機器上線。

此範本會執行下列作業︰

1. 使用現有的「自動化」帳戶或建立一個新帳戶
2. 採用下列各項的輸入參數︰
   * ASRRemoteFile -- 您儲存行動服務設定的位置
   * ASRPassphrase -- 您儲存 passphrase.txt 檔案的位置
   * ASRCSEndpoint -- 您管理伺服器的 IP 位址
3. 匯入 xPSDesiredStateConfiguration PowerShell 模組
4. 建立及編譯 DSC 組態

所有上述步驟都會依正確順序進行，以便您能夠開始將機器上線來進行保護。

包含部署指示的範本位於 [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC)。

使用 PowerShell 來部署範本：

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>後續步驟
部署行動服務代理程式之後，您可以為虛擬機器 [啟用複寫](vmware-azure-tutorial.md) 。
