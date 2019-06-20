---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174441"
---
若要從遠端連線至裝置的程序會視用戶端作業系統，而不同。

### <a name="remotely-connect-from-a-windows-client"></a>Windows 用戶端從遠端連線

在開始之前，請確定您的 Windows 用戶端正在執行 Windows PowerShell 5.0 或更新版本。

遵循下列步驟從 Windows 用戶端，從遠端連線。

1. 系統管理員身分執行的 Windows PowerShell 工作階段。
2. 請確定 Windows 遠端管理服務在您的用戶端上執行。 在命令提示字元中，輸入：

    `winrm quickconfig`

3. 變數指派給裝置的 IP 位址。

    $ip = "<device_ip>"

    取代`<device_ip>`與您的裝置的 IP 位址。

4. 若要將您裝置的 IP 位址新增至用戶端的信任的主機清單中，輸入下列命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在裝置上啟動 Windows PowerShell 工作階段：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 提供當系統提示您的密碼。 使用相同的密碼，用來登入的本機 web UI。 預設的本機 web UI 密碼*Password1*。 當您成功連線到使用遠端 PowerShell 的裝置時，您會看到下列的範例輸出：  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>從 Linux 用戶端遠端連線

Linux 用戶端，您將用於連線：

- [安裝最新 PowerShell Core for Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6)從 GitHub 取得 SSH 遠端功能。 
- [只安裝`gss-ntlmssp`NTLM 模組中的封裝](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 Ubuntu 的用戶端，請使用下列命令：
    - `sudo apt-get install gss-ntlmssp`

如需詳細資訊，請移至[透過 SSH 的 PowerShell 遠端執行功能](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)。

遵循下列步驟，NFS 用戶端從遠端連線。

1. 若要開啟 PowerShell 工作階段，請輸入：

    `sudo pwsh`
 
2. 使用遠端用戶端的連線，請輸入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出現提示時，提供用來登入您的裝置的密碼。
 
> [!NOTE]
> 此程序在 Mac OS 上無法運作。
