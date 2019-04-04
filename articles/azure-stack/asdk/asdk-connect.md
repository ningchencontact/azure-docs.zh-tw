---
title: 連線到 Azure Stack | Microsoft Docs
description: 了解如何連線到 ADSK。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 31025582516198bdfe9da9312bae33852986a423
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884851"
---
# <a name="connect-to-the-asdk"></a>連線到 ASDK

若要管理資源，您必須先連線到「Azure Stack 開發套件」(ASDK)。 在本文中，我們將說明使用下列連線選項連線至 ASDK 所需採取的步驟：

* [遠端桌面連線 (RDP)](#connect-with-rdp)。 當您使用「遠端桌面連線」進行連線時，單一使用者可以快速連線至開發套件。
* [虛擬私人網路 (VPN)](#connect-with-vpn)。 當您使用 VPN 進行連線時，多名使用者可以同時從 Azure Stack 基礎結構外部的用戶端連線至 Azure Stack 入口網站。 VPN 連線需要進行某些設定。

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>使用 RDP 連線至 Azure Stack

單一並行使用者可以透過「遠端桌面連線」直接從 ASDK 主機電腦在 Azure Stack 系統管理入口網站或使用者入口網站中管理資源。 

> [!TIP]
> 此選項也可讓您在已登入 ASDK 主機電腦的情況下，使用 RDP 登入在 ASDK 主機電腦上建立的虛擬機器。 

1. 開啟遠端桌面連線 (mstc.exe)，然後使用有權從遠端登入 ASDK 主機電腦的帳戶連線至開發套件主機電腦 IP 位址。 根據預設，**AzureStack\AzureStackAdmin** 具有從遠端登入 ASDK 主機電腦的權限。  

2. 在開發套件主機電腦上，開啟 [伺服器管理員] (ServerManager.exe)。 選取 [本機伺服器]、關閉 [IE 增強式安全性設定]，然後關閉 [伺服器管理員]。

3. 以 **AzureStack\CloudAdmin** 身分或使用其他「Azure Stack 操作員」認證來登入管理入口網站。 ASDK 管理入口網站位址為 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)。

4. 以 **AzureStack\CloudAdmin** 身分或使用其他 Azure Stack 使用者認證來登入使用者入口網站。 ASDK 使用者入口網站位址為 [https://portal.local.azurestack.external](https://portal.local.azurestack.external)。

> [!NOTE]
> 如需有關何時該使用哪個帳戶的詳細資訊，請參閱 [ASDK 管理基本概念](asdk-admin-basics.md#what-account-should-i-use)。

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>使用 VPN 連線至 Azure Stack

您可以建立對 ASDK 主機電腦的分割通道 VPN 連線，以存取 Azure Stack 入口網站及安裝在本機的工具 (例如 Visual Studio 和 PowerShell)。 使用 VPN 連線時，多個使用者可以同時連線到 ASDK 所裝載的 Azure Stack 資源。

Azure AD 和「Active Directory 同盟服務」(AD FS) 部署都支援 VPN 連線。

> [!NOTE]
> VPN 連線*無法*讓您連線至 Azure Stack VM。 透過 VPN 連線時，您無法使用 RDP 連線至 Azure Stack VM。

### <a name="prerequisites"></a>必要條件
在設定與 ASDK 的 VPN 連線之前，請確定您符合下列先決條件。

- 在您的本機電腦上安裝 [Azure Stack 相容的 Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)。  
- 下載[處理 Azure Stack 所需的工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

### <a name="set-up-vpn-connectivity"></a>設定 VPN 連線能力

若要建立與 ASDK 的 VPN 連線，請在 Windows 型本機電腦上，以系統管理員身分開啟 PowerShell。 然後，執行下列指令碼 (針對您的環境更新 IP 位址和密碼值)：

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果設定成功，**azurestack** 會出現在您的 VPN 連線清單。

![網路連線](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>連線至 Azure Stack

  請使用下列其中一個方法來連線到 Azure Stack 執行個體：  

  * 使用 `Connect-AzsVpn` 命令：
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * 在您的本機電腦上，選取 [網路設定] > [VPN] > [azurestack] > [連線]。 出現登入提示時，輸入使用者名稱 (**AzureStack\AzureStackAdmin**) 和密碼。

第一次連線時，系統會提示您在本機電腦的憑證存放區中安裝來自 **AzureStackCertificateAuthority** 的 Azure Stack 根憑證。 此步驟會將 ASDK 憑證授權單位 (CA) 新增至信任主機清單。 按一下 [是] 可安裝憑證。

![根憑證](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > PowerShell 視窗或其他應用程式可能會隱藏提示。

### <a name="test-vpn-connectivity"></a>測試 VPN 連線能力

若要測試入口網站連線，請開啟網頁瀏覽器，然後移至使用者入口網站 (https://portal.local.azurestack.external/)) 或管理入口網站 (https://adminportal.local.azurestack.external/))。 

使用適當的訂用帳戶認證登入，以建立和管理資源。  

## <a name="next-steps"></a>後續步驟

[疑難排解](asdk-troubleshooting.md)
