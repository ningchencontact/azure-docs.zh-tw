---
title: 快速入門 - 使用適用於伺服器的 Azure Arc 將機器連線至 Azure - 入口網站
description: 在此快速入門中，您將了解如何從入口網站，使用適用於伺服器的 Azure Arc 將機器連線至 Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid, onboard, Azure 自動化, 預期狀態設定, 更新管理, 變更追蹤, 清查, 圖形化, 混合式, 上線
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 2ae7c8545286baebc83077276e356cd2e41f0dc3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668666"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>快速入門：使用適用於伺服器的 Azure Arc 將機器連線至 Azure - 入口網站

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

檢閱[適用於伺服器的 Azure Arc 概觀](overview.md)中的受支援用戶端和必要網路設定。

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>使用 Azure 入口網站產生代理程式安裝指令碼

1. 啟動 [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. 按一下 [+新增] 
1. 遵循精靈的步驟來完成
1. 最後一頁會產生可供您複製 (或下載) 的指令碼。

指令碼必須在您想要連線的目標機器上執行。 它會下載並安裝代理程式，然後將機器連線為單一作業。

在您想要管理的非 Azure 伺服器上：

1. 登入伺服器 (使用 SSH、RDP 或 PowerShell 遠端)
1. 啟動殼層：在 Linux 上使用 Bash，在 Windows 上以系統管理員身分使用 PowerShell
1. 貼上來自入口網站的指令碼，並在要連線至 Azure 的伺服器上執行它。
1. 將個別伺服器上線的預設驗證是「互動式」  的，它使用 Azure「裝置登入」。 當您執行指令碼時，您會看到類似以下的訊息：

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   開啟瀏覽器並輸入代碼來驗證。 瀏覽器不需要在您要上線的伺服器上執行，它可能位於另一部電腦上，例如您的膝上型電腦。

1. 如果您想要以非互動方式進行驗證，請遵循[建立服務主體](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale)中的步驟，然後修改從入口網站產生的指令碼。

> [!NOTE]
> 如果您第一次在伺服器上登入是使用 Internet Explorer，將會發生錯誤。您可以重新開啟瀏覽器，然後再執行一次。

## <a name="execute-the-script-on-target-nodes"></a>在目標節點上執行指令碼

登入每個節點，並執行您從入口網站產生的指令碼。 指令碼順利完成之後，移至 Azure 入口網站確認伺服器已成功連線。

![上線成功](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>清除

若要讓機器與適用於伺服器 Azure Arc 中斷連線，您必須執行兩個步驟。

1. 在[入口網站](https://aka.ms/hybridmachineportal)中選取機器，按一下省略符號 (`...`)，然後選取 [刪除]  。
1. 將代理程式從機器解除安裝。

   在 Windows 上，您可以使用 [應用程式與功能] 控制台來解除安裝代理程式。
  
  ![應用程式與功能](./media/quickstart-onboard/apps-and-features.png)

   如果您想要透過指令碼進行解除安裝，您可以使用下列範例來擷取 **PackageId**，並使用 `msiexec /X` 來解除安裝代理程式。

   在登錄機碼 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`之下查看並尋找 **PackageId**。 然後，您可以使用 `msiexec` 來解除安裝代理程式。

   下列範例示範如何解除安裝代理程式。

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   在 Linux 上，執行下列命令來解除安裝代理程式。

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將原則指派給已連線的機器](../../governance/policy/assign-policy-portal.md)