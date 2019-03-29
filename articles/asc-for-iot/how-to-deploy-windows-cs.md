---
title: IoT 代理程式預覽的 Windows 安裝的 ASC |Microsoft Docs
description: 深入了解如何為 32 位元或 64 位元的 Windows 裝置上的 IoT 代理程式安裝 ASC。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621823"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>針對 IoT 部署 ASC C#-Windows 的安全性代理程式

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南說明如何安裝適用於 IoT ASC C#-在 Windows 上的安全性代理程式。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * Install
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>必要條件

如需其他平台和代理程式 flavours，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 您想要安裝在電腦上的本機系統管理權限。 

1. [建立安全性模組](quickstart-create-security-twin.md)裝置。

## <a name="installation"></a>安裝 

若要安裝安全性代理程式，執行下列作業：

1. 若要安裝適用於 IoT Windows ASCC#代理程式在裝置上，下載最新版本到您的電腦與 ASC iot [GitHub 存放庫](https://github.com/Azure/Azure-IoT-Security-Agent-CS)。

2. 擷取封裝的內容，並瀏覽至 [/Install] 資料夾。

3. 系統管理員身分開啟 Windows PowerShell。 
    1. 加入 InstallSecurityAgent 指令碼中的執行權限執行 ```Unblock-File .\InstallSecurityAgent.ps1```
    
        然後執行：

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    例如︰
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)如需有關驗證參數。

此指令碼會執行下列動作︰

- 安裝必要條件。

- 將服務的使用者 （使用停用的互動式登入）。

- 安裝代理程式做**系統服務**。

- 使用提供的驗證參數來設定代理程式。


如需其他協助，請在 PowerShell 中使用 Get-help 命令 <br>取得說明的範例：  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>確認 部署狀態

- 執行檢查代理程式部署狀態：<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式：

1. 執行下列 PowerShell 指令碼 **-模式**參數設為**解除安裝**。  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>疑難排解

如果代理程式無法啟動，請開啟記錄 (記錄*關閉*預設) 以取得詳細資訊。

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
    > 我們建議您開啟記錄**關閉**疑難排解完成之後。 保留記錄**上**增加記錄檔大小和資料使用情況。 

1. 重新啟動代理程式，以藉由執行下列 PowerShell 或命令列：

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

1. 檢閱失敗的詳細資訊的記錄檔。

   記錄檔位置： `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>後續步驟
- 閱讀 IoT 服務 ASC[概觀](overview.md)
- 深入了解 ASC iot[架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 讀取[常見問題集](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)