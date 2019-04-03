---
title: Azure 資訊安全中心的 IoT 代理程式預覽的 Windows 安裝 |Microsoft Docs
description: 深入了解如何安裝 Azure 資訊安全中心在 32 位元或 64 位元的 Windows 裝置上的 IoT 代理程式。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: b45d64f56653ad6d0baabef4a63351ef14f24c42
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861950"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>針對 IoT 部署 Azure 資訊安全中心C#為基礎的 Windows 安全性代理程式

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南說明如何安裝 Azure 資訊安全中心 (ASC) 適用於 IoT C#-在 Windows 上的安全性代理程式。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * Install
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>必要條件

如需其他平台和代理程式類型，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 您想要安裝在電腦上的本機系統管理權限。 

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝 

若要安裝安全性代理程式，請執行下列步驟：

1. 若要安裝適用於 IoT Windows ASCC#代理程式在裝置上，下載最新版本到您的電腦與 ASC iot [GitHub 存放庫](https://github.com/Azure/Azure-IoT-Security-Agent-CS)。

2. 解壓縮套件的內容，然後瀏覽至 /Install 資料夾。

3. 系統管理員身分開啟 Windows PowerShell。 
    1. 執行 ```Unblock-File .\InstallSecurityAgent.ps1``` 將執行權限新增至 InstallSecurityAgent 指令碼
    
        然後執行：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    例如︰
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    如需驗證參數的詳細資訊，請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)。

此指令碼會執行下列動作︰

- 安裝必要條件。

- 新增服務的使用者 (並停用互動式登入)。

- 將代理程式安裝為**系統服務**。

- 使用所提供的驗證參數來設定代理程式。


如需其他協助，請在 PowerShell 中使用 Get-Help 命令 <br>Get-Help 範例：  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>確認部署狀態

- 執行下列命令來確認代理程式的部署狀態：<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式：

1. 執行下列 PowerShell 指令碼，其中 **-mode** 參數請設定為 **Uninstall**。  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>疑難排解

如果代理程式無法啟動，請開啟記錄 (預設會「關閉」記錄) 以取得詳細資訊。

若要開啟記錄：

1. 開啟 [使用標準檔案編輯器編輯的組態檔 (General.config)]。

1. 編輯下列值：

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 建議您在疑難排解完成後將記錄**關閉**。 讓記錄保持**開啟**會增加記錄檔大小和資料使用量。 

1. 執行下列 PowerShell 或命令列來重新啟動代理程式：

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   或

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. 如需失敗的詳細資訊，請檢閱記錄檔。

   記錄檔位置： `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>後續步驟
- 閱讀適用於 IoT 的 ASC 服務[概觀](overview.md)
- 深入了解適用於 IoT 的 ASC [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題集](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)