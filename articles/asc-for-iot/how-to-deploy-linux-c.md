---
title: 安裝和部署 Azure 資訊安全中心的 IoT 代理程式預覽的 Linux C 代理程式的指南 |Microsoft Docs
description: 了解如何在 32 位元和 64 位元的 Linux 上安裝 Azure 資訊安全中心，IoT 代理程式。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 0089fd1af6576f9bcdebe4b7f270a573205dea82
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783495"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>部署 IoT C 為基礎的安全性代理程式，適用於 Linux 的 Azure 資訊安全中心

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本指南說明如何安裝和部署 IoT C 為基礎的安全性代理程式在 Linux 上的 Azure 資訊安全中心 (ASC)。

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * Install
> * 驗證部署
> * 解除安裝代理程式
> * 疑難排解 

## <a name="prerequisites"></a>必要條件

如需其他平台和代理程式版本，請參閱[選擇正確的安全性代理程式](how-to-deploy-agent.md)。

1. 若要部署安全性代理程式，您想要安裝 (sudo) 的電腦上需要本機系統管理員權限。

1. 為裝置[建立安全性模組](quickstart-create-security-twin.md)。

## <a name="installation"></a>安裝 

若要安裝和部署安全性代理程式，請執行下列步驟：


1. 從 [GitHub](https://aka.ms/iot-security-github-c) 下載最新版本到您的電腦。

1. 解壓縮套件的內容，然後瀏覽至 /Install 資料夾。

1. 執行下列命令將執行權限新增至 **InstallSecurityAgent** 指令碼：
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 接下來，請執行： 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   如需驗證參數的詳細資訊，請參閱[如何設定驗證](concept-security-agent-authentication-methods.md)。

此指令碼會執行下列動作︰

1. 安裝必要條件。

2. 新增服務的使用者 (並停用互動式登入)。

3. 將代理程式安裝為**精靈** - 假設裝置使用 **systemd** 來管理服務。

4. 使用所提供的驗證參數來設定代理程式。 

如需其他協助，請使用 –help 參數來執行指令碼： 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>解除安裝代理程式

若要解除安裝代理程式，請執行 指令碼--uninstall 參數：

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>疑難排解
執行下列命令來確認部署狀態：

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>後續步驟
- 閱讀適用於 IoT 的 ASC 服務[概觀](overview.md)
- 深入了解適用於 IoT 的 ASC [架構](architecture.md)
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題集](resources-frequently-asked-questions.md)
- 了解[安全性警示](concept-security-alerts.md)