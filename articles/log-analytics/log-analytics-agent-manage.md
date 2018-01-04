---
title: "管理 Azure 記錄分析代理程式 |Microsoft 文件"
description: "本文將告訴您通常會在生命週期的 Microsoft Monitoring Agent (MMA) 部署在機器上執行的其他管理工作。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: e917b64c32c2f99c9a8b0397e70a32b2dbb2e3cf
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>管理和維護適用於 Windows 和 Linux 的記錄分析代理程式

初始部署後的記錄分析的 Windows 或 Linux 代理程式，您可能需要重新設定視情況，代理程式，或如果已達到淘汰階段，在其生命週期中的從電腦移除它。  您可以輕鬆地管理這些例行的維護工作，以手動方式或透過自動化，以降低操作的錯誤和費用。

## <a name="adding-or-removing-a-workspace"></a>新增或移除工作區 

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="update-settings-from-control-panel"></a>從 [控制台] 的更新設定

1. 電腦具有系統管理權限的帳戶登入。
2. 開啟 [ **控制台**]。
3. 選取**Microsoft Monitoring Agent** ，然後按一下 [ **Azure 記錄分析 (OMS)** ] 索引標籤。
4. 如果移除工作區，請選取它，然後按一下**移除**。 您想要停止回報到的代理程式的其他工作區的重複此步驟。
5. 如果加入工作區中，按一下**新增**以及**新增記錄分析工作區** 對話方塊中，貼上的工作區識別碼和工作區金鑰 （主索引鍵）。 如果電腦應該報告為 Azure 政府雲端中的記錄分析工作區，請從 Azure 雲端下拉式清單中選取 Azure 美國政府。 
6. 按一下 [確定]  以儲存變更。

#### <a name="remove-a-workspace-using-powershell"></a>移除工作區中使用 PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>加入商業使用 PowerShell 的 Azure 中的工作區 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>美國政府使用 PowerShell 在 Azure 中加入工作區 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>如果您已在命令列或指令碼之前用來安裝或設定代理程式，`EnableAzureOperationalInsights`取代為`AddCloudWorkspace`和`RemoveCloudWorkspace`。
>

## <a name="update-proxy-settings"></a>更新 proxy 設定 
若要設定代理程式和服務透過 proxy 伺服器通訊或[OMS 閘道](log-analytics-oms-gateway.md)部署之後，請使用下列方法之一來完成這項工作。

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="update-settings-using-control-panel"></a>使用控制台中的更新設定

1. 電腦具有系統管理權限的帳戶登入。
2. 開啟 [ **控制台**]。
3. 選取**Microsoft Monitoring Agent** ，然後按一下 [ **Proxy 設定**] 索引標籤。
4. 按一下**使用 proxy 伺服器**和提供的 URL 和連接埠號碼的 proxy 伺服器或閘道。 如果您的 OMS 閘道或 proxy 伺服器需要驗證，請輸入使用者名稱和密碼來進行驗證，然後按一下 **確定**。 

#### <a name="update-settings-using-powershell"></a>使用 PowerShell 的更新設定 

複製下列範例 PowerShell 程式碼、 更新您的環境，以特定資訊並以 PS1 副檔名儲存。  直接連接到記錄分析服務每一部電腦上執行指令碼。

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Linux 代理程式
如果您的 Linux 電腦需要透過 OMS 記錄分析閘道或 proxy 伺服器進行通訊，請執行下列步驟。  Proxy 組態值的語法如下：`[protocol://][user:password@]proxyhost[:port]`。

1. 藉由執行下列命令來編輯檔案 `/etc/opt/microsoft/omsagent/proxy.conf`，並將值變更為您的特定設定。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 執行下列命令來重新啟動代理程式： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>解除安裝代理程式
使用下列程序解除安裝 Windows 或 Linux 代理程式命令列或安裝精靈。

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="uninstall-from-control-panel"></a>從控制台解除安裝
1. 電腦具有系統管理權限的帳戶登入。  
2. 在**控制台**，按一下 **程式和功能**。
3. 在**程式和功能**，按一下  **Microsoft Monitoring Agent**，按一下 **解除安裝**，然後按一下 **是**。

>[!NOTE]
>代理程式安裝精靈也可以按兩下執行**Mmasetup-amd64.exe-<platform>.exe**，這是可從 Azure 入口網站中的工作區下載。

#### <a name="uninstall-from-the-command-line"></a>從命令列解除安裝
代理程式下載的檔案是使用 IExpress 建立獨立的安裝套件。  代理程式和支援檔案的安裝程式包含封裝中，而且需要以適當地解除安裝使用下列範例所示的命令列擷取。 

1. 電腦具有系統管理權限的帳戶登入。  
2. 若要擷取代理程式安裝檔案中，從提升權限的命令提示字元執行`extract MMASetup-<platform>.exe`它就會提示您將檔案解壓縮至的路徑。  或者，您可以指定路徑所傳遞的引數`extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`。  如需有關命令列的 swtiches IExpress 所支援的詳細資訊，請參閱[IExpress 的命令列參數](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)然後更新以符合您需求的範例。
3. 在提示中輸入`%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`。  

### <a name="linux-agent"></a>Linux 代理程式
若要移除代理程式，請執行下列步驟。

1. 將 Linux 代理程式[通用指令碼](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)下載到電腦。
2. 在電腦上使用 *--purge* 引數執行搭售的 .sh 檔案，如此可將代理程式及其組態完全移除。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>向 Operations Manager 管理群組回報的代理程式設定

### <a name="windows-agent"></a>Windows 代理程式
執行下列步驟來設定 OMS Agent for Windows 回報至 System Center Operations Manager 管理群組。 

1. 電腦具有系統管理權限的帳戶登入。
2. 開啟 [ **控制台**]。 
3. 按一下**Microsoft Monitoring Agent** ，然後按一下 [ **Operations Manager** ] 索引標籤。
4. 如果 Operations Manager 伺服器已與 Active Directory 整合，請按一下 [自動更新來自 AD DS 的管理群組指派] 。
5. 按一下 [新增] 以開啟 [新增管理群組] 對話方塊。
6. 在**管理群組名稱**欄位中，輸入您的管理群組的名稱。
7. 在**主要管理伺服器**欄位中，輸入主要管理伺服器的電腦名稱。
8. 在**管理伺服器連接埠**欄位中，輸入 TCP 連接埠號碼。
9. 在 [代理程式動作帳戶] 頁面下，選擇本機系統帳戶或本機網域帳戶。
10. 按一下 [確定] 關閉 [新增管理群組] 對話方塊，然後按一下 [確定] 關閉 [Microsoft 監視代理程式內容] 對話方塊。

### <a name="linux-agent"></a>Linux 代理程式
執行下列步驟設定 OMS Agent for Linux 向 System Center Operations Manager 管理群組報告。 

1. 編輯 `/etc/opt/omi/conf/omiserver.conf`
2. 請確定行首`httpsport=`定義連接埠 1270年。 例如：`httpsport=1270`
3. 重新啟動 OMI 伺服器：`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>後續步驟

檢閱[疑難排解 Linux 代理程式](log-analytics-agent-linux-support.md)如果您安裝或管理代理程式時所遇到的問題。  