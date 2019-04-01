---
title: 安裝和部署 Linux 指南C#代理程式的 IoT 預覽的 Azure 資訊安全中心 |Microsoft Docs
description: 了解如何在 32 位元和 64 位元的 Linux 上安裝 Azure 資訊安全中心，IoT 代理程式。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: be4c663d3a1e99ef67cbbbc2f39b315f1080125c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758340"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>針對 IoT 部署 Azure 資訊安全中心C#-適用於 Linux 的安全性代理程式

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南說明如何安裝和部署 iot 的 Azure 資訊安全中心 (ASC) C#-以 Linux 為主的安全性代理程式。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * Install
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>必要條件

如需其他平台和代理程式版本，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您想要安裝在電腦上需要本機系統管理員權限。 

1. [建立安全性模組](quickstart-create-security-twin.md)裝置。

## <a name="installation"></a>安裝 

若要部署安全性代理程式，執行下列作業：

1. 最新的版本下載到電腦上從  [Github](https://aka.ms/iot-security-github-cs)。

1. 擷取封裝的內容，並瀏覽至 _/install_資料夾。

1. 新增執行權限**InstallSecurityAgent 指令碼**執行 `chmod +x InstallSecurityAgent.sh` 

1. 接下來，執行： 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)如需有關驗證參數。

此指令碼會執行下列動作︰

- 安裝必要條件。

- 將服務的使用者 （使用停用的互動式登入）。

- 安裝代理程式做**Daemon** -這是假設裝置會使用**systemd**服務管理。

- 會設定**sudoers**允許執行特定工作，以 root 身分執行代理程式。

- 使用提供的驗證參數來設定代理程式。


如需其他協助，請執行指令碼-help 參數，使用： `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>解除安裝代理程式

解除安裝代理程式，請使用-u 參數執行指令碼： `./InstallSecurityAgent.sh -u`。 

> [!NOTE]
> 解除安裝不會移除安裝期間，已安裝任何遺漏的必要條件。

## <a name="troubleshooting"></a>疑難排解  

1. 執行檢查部署狀態：

    `systemctl status ASCIoTAgent.service`

2. 啟用記錄。  
   如果代理程式無法啟動，請開啟記錄功能，以取得詳細資訊。

   開啟的記錄：

   1. 開啟在任何 Linux 編輯器中編輯的組態檔：

        `vi /var/ASCIoTAgent/General.config`

   1. 編輯下列值： 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **LogFilePath**是設定的值。 

       > [!NOTE]
       > 我們建議您開啟記錄**關閉**疑難排解完成之後。 保留記錄**上**增加記錄檔大小和資料使用情況。

   1. 執行重新啟動代理程式：

       `systemctl restart ASCIoTAgent.service`

   1. 檢視失敗的詳細資訊的記錄檔。  

       記錄檔位置是： `/var/ASCIoTAgent/IotAgentLog.log`

       變更檔案位置路徑，根據您選擇的名稱**logFilePath**在步驟 2。 

## <a name="next-steps"></a>後續步驟

- 閱讀 IoT 服務 ASC[概觀](overview.md)
- 深入了解 ASC iot[架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 讀取[常見問題集](resources-frequently-asked-questions.md)
- 了解[警示](concept-security-alerts.md)