---
title: "針對 Azure Cloud Shell 進行疑難排解 | Microsoft Docs"
description: "針對 Azure Cloud Shell 進行疑難排解"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 52ee832b643af573d8236b266df17d36e485ead2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的疑難排解和限制

針對 Azure Cloud Shell 問題進行疑難排的已知解決方式包括：

## <a name="general-troubleshooting"></a>一般疑難排解

### <a name="early-timeouts-in-firefox"></a>在 FireFox 中提前逾時
- **詳細資料**：Cloud Shell 會利用開放式 Websocket 將輸入/輸出傳遞至瀏覽器。 FireFox 預設的原則會提早關閉 Websocket，而造成在 Cloud Shell 中提前逾時。
- **解決辦法**：開啟 FireFox，然後在 URL 方塊中瀏覽至 "about:config"。 搜尋 "network.websocket.timeout.ping.request"，然後將值從 0 變更為 10。

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>儲存體對話方塊 - 錯誤：403 RequestDisallowedByPolicy
- **詳細資料**：因為您的系統管理員設置的 Azure 原則，透過 Cloud Shell 建立儲存體帳戶失敗。錯誤訊息包括：`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解決辦法**：連絡您的 Azure 系統管理員，請他移除或更新拒絕儲存體建立的 Azure 原則。

### <a name="storage-dialog---error-400-disallowedoperation"></a>儲存體對話方塊 - 錯誤：400 DisallowedOperation
 - **詳細資料**：使用 Azure Active Directory 訂用帳戶時，您無法建立儲存體。
 - **解決方式**：使用能夠建立儲存體資源的 Azure 訂用帳戶。 Azure AD 訂用帳戶無法建立 Azure 資源。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>終端機輸出 - 錯誤：無法與終端機連線: 無法建立 websocket。 按 `Enter` 重新連線。
 - **詳細資料**：Cloud Shell 必須能夠與 Cloud Shell 基礎結構建立 websocket 連線。
 - **解決辦法**：確認您已將您的網路設定設定為啟用傳送 https 要求和 websocket 要求至 *.console.azure.com 中的網域。

## <a name="bash-troubleshooting"></a>Bash 疑難排解

### <a name="cannot-run-az-login"></a>無法執行 az login

- **詳細資料**：執行 `az login` 將無法運作，因為您已經在用來登入 Cloud Shell 或 Azure 入口網站的帳戶下驗證過。
- **解決方式**：運用您使用的帳戶來登入或登出，然後使用您預期的 Azure 帳戶來重新進行驗證。

### <a name="cannot-run-the-docker-daemon"></a>無法執行 Docker 精靈

- **詳細資料**：Cloud Shell 會運用容器來裝載您的殼層環境，因此系統會不允許執行精靈。
- **解決方式**：運用預設安裝的 [docker-machine](https://docs.docker.com/machine/overview/) 以從遠端 Docker 主機管理 Docker 容器。

## <a name="powershell-troubleshooting"></a>PowerShell 疑難排解

### <a name="no-home-directory-persistence"></a>沒有 $Home 目錄持續性

- **詳細資料**：應用程式 (例如：git、vim 和其他項目) 寫入 `$Home` 的任何資料將不會跨 PowerShell 工作階段保存。
- **解決方式**：在 PowerShell 設定檔中，以在 $Home 的 `clouddrive` 中建立應用程式特定資料夾的符號連結。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C 不會結束 Cmdlet 提示字元

- **詳細資料**：嘗試結束 Cmdlet 提示字元時，`Ctrl+C` 不會結束提示字元。
- **解決方式**：若要結束提示，請依序按 `Ctrl+C` 和 `Enter`。

### <a name="gui-applications-are-not-supported"></a>不支援 GUI 應用程式

- **詳細資料**：如果使用者啟動 GUI 應用程式，則不會傳回提示。 例如，當使用者複製已啟用雙因素驗證的私人 GitHub 存放庫時，會顯示一個對話方塊，用以完成雙因素驗證。  
- **解決方式**：關閉並重新開啟殼層。

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online 不會開啟說明頁面

- **詳細資料**：如果使用者鍵入 `Get-Help Find-Module -online`，則會看到一則錯誤訊息，例如：`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **解決方式**：複製 URL，並手動在瀏覽器上予以開啟。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>針對 Azure VM 遠端管理進行疑難排解

- **詳細資料**：基於 WinRM 的預設 Windows 防火牆設定，使用者可能會看到下列錯誤：`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **解決方式**：確定 VM 正在執行。 您可以執行 `Get-AzureRmVM -Status` 來找出 VM 狀態。  接下來，在遠端 VM 上新增防火牆規則，以允許任何子網路中的 WinRM 連線 (舉例來說)。

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 您可以使用 [Azure 自訂指令碼擴充功能](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)，避免登入遠端 VM 來新增防火牆規則。
 您可以將前述指令碼儲存至檔案 (即 `addfirerule.ps1`)，並將其上傳至 Azure 儲存體容器。
 然後，嘗試下列命令：

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` 會快取 Azure 磁碟機中的結果

- **詳細資料**：會在 Azure 磁碟機中快取 `dir` 的結果。
- **解析**：您在 Azure 磁碟機檢視中建立或移除資源之後，請執行 `dir -force` 來進行更新。

## <a name="general-limitations"></a>一般限制
Azure Cloud Shell 具有下列已知限制：

### <a name="system-state-and-persistence"></a>系統狀態和持續性

提供 Cloud Shell 工作階段的電腦只是暫時性，在工作階段閒置 20 分鐘後就會回收。 Cloud Shell 需要掛接 Azure 檔案共用。 因此，您的訂用帳戶必須能夠設定儲存體資源，才可存取 Cloud Shell。 其他考量包括：

* 在掛接的儲存體中，只會保存 `clouddrive` 目錄內的修改。 在 Bash 中，也會保存 `$Home` 目錄。
* 只能從您的[已指派區域](persisting-shell-storage.md#mount-a-new-clouddrive)內掛接 Azure 檔案共用。
  * 在 Bash 中，會執行 `env` 來尋找設定為 `ACC_LOCATION` 的區域。
* Azure 檔案只支援本機備援儲存體和異地備援儲存體帳戶。

### <a name="browser-support"></a>瀏覽器支援

Cloud Shell 支援最新版的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 及 Apple Safari。 不支援 Safari 私密瀏覽模式。

### <a name="copy-and-paste"></a>複製和貼上

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>針對指定的使用者，只能有一個殼層作用中

使用者一次只能啟動一種類型的 Shell：**Bash** 或 **PowerShell**。 不過，您可能一次會執行多個 Bash 或 PowerShell 執行個體。 在 Bash 或 PowerShell 之間交換會讓 Cloud Shell 重新啟動，這樣會終止現有工作階段。

### <a name="usage-limits"></a>使用限制

Cloud Shell 主要用於互動式的使用案例。 因此，任何長時間執行而沒有互動的工作階段會在不發出警告的情況下結束。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>使用者權限

權限設定為沒有 sudo 存取權的一般使用者。 不會保存 `$Home` 目錄之外的任何安裝。

### <a name="editing-bashrc"></a>編輯 .bashrc

編輯 .bashrc 時請小心，這麼做可能會導致 Cloud Shell 發生未預期的錯誤。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="slow-startup-time"></a>緩慢的啟動時間

在預覽期間，Azure Cloud Shell 中的 PowerShell (Preview) 最多需要 60 秒才能初始化。

### <a name="default-file-location-when-created-from-azure-drive"></a>從 Azure 磁碟機建立時的預設檔案位置：

使用 PowerShell Cmdlet 時，使用者無法在 Azure 磁碟機底下建立檔案。 當使用者使用其他工具 (例如 vim 或 nano) 來建立新檔案時，檔案預設會儲存至 C:\Users 資料夾。 

### <a name="gui-applications-are-not-supported"></a>不支援 GUI 應用程式

如果使用者執行的命令會建立 Windows 對話方塊，例如 `Connect-AzureAD` 或 `Login-AzureRMAccount`，其將會看到如下錯誤訊息：`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`。
