---
title: 安裝和部署 Linux 指南C#代理程式的 IoT 預覽的 Azure 資訊安全中心 |Microsoft Docs
description: 了解如何在 32 位元和 64 位元的 Linux 上安裝 Azure 資訊安全中心，IoT 代理程式。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 808ff912a997a4c09a22048ada7546daab895701
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618256"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>針對 IoT 部署 Azure 資訊安全中心C#-適用於 Linux 的安全性代理程式

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南說明如何安裝和部署 iot 的 Azure 資訊安全中心 (ASC) C#-以 Linux 為主的安全性代理程式。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 安裝
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>先決條件

如需其他平台和代理程式版本，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您必須在將要用來安裝代理程式的電腦上，擁有其本機系統管理員的權限。 

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝 

若要部署安全性代理程式，請執行下列步驟：

1. 從 [GitHub](https://aka.ms/iot-security-github-cs) 下載最新版本到您的電腦。

1. 解壓縮套件的內容，然後瀏覽至 /Install  資料夾。

1. 執行 `chmod +x InstallSecurityAgent.sh` 將執行權限新增至 **InstallSecurityAgent** 指令碼 

1. 接下來，請執行： 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   如需驗證參數的詳細資訊，請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)。

此指令碼會執行下列動作︰

- 安裝必要條件。

- 新增服務的使用者 (並停用互動式登入)。

- 將代理程式安裝為**精靈** - 這會假設裝置使用 **systemd** 來管理服務。

- 請設定 **sudoers** 以允許代理程式透過 root 身分執行特定工作。

- 使用所提供的驗證參數來設定代理程式。


如需其他協助，請使用 –help 參數來執行指令碼：`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式，請使用 –u 參數來執行指令碼：`./InstallSecurityAgent.sh -u`。 

> [!NOTE]
> 解除安裝並不會移除安裝期間所安裝的任何遺漏必要條件。

## <a name="troubleshooting"></a>疑難排解  

1. 執行下列命令來確認部署狀態：

    `systemctl status ASCIoTAgent.service`

2. 啟用記錄。  
   如果代理程式無法啟動，請開啟記錄以取得詳細資訊。

   透過下列步驟開啟記錄：

   1. 在任何 Linux 編輯器中開啟要編輯的組態檔：

        `vi /var/ASCIoTAgent/General.config`

   1. 編輯下列值： 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       您可以設定 **LogFilePath** 值。 

       > [!NOTE]
       > 建議您在疑難排解完成後將記錄**關閉**。 讓記錄保持**開啟**會增加記錄檔大小和資料使用量。

   1. 執行下列命令來重新啟動代理程式：

       `systemctl restart ASCIoTAgent.service`

   1. 如需失敗的詳細資訊，請檢視記錄檔。  

       記錄檔位置是：`/var/ASCIoTAgent/IotAgentLog.log`

       根據您在步驟 2 為 **logFilePath** 選擇的名稱，來變更檔案位置路徑。 

## <a name="next-steps"></a>後續步驟

- 閱讀適用於 IoT 的 ASC 服務[概觀](overview.md)
- 深入了解適用於 IoT 的 ASC [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題集](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)