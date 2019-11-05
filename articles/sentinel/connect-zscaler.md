---
title: 將 Zscaler 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Zscaler 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: fe7ba0f6daec0b85ec73611ba4e48d72f16146e3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515131"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>將 Zscaler 網際網路存取連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Zscaler 資料連線器目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的 Zscaler 網際網路存取設備連接到 Azure Sentinel。 Zscaler 資料連線器可讓您輕鬆地將 Zscaler Internet Access （ZIA）記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示，以及改善調查。 在 Azure Sentinel 上使用 Zscaler，可讓您深入瞭解組織的網際網路使用方式，並將增強其安全性作業功能。 


## <a name="how-it-works"></a>運作方式

您需要在專用的 Linux 機器（VM 或內部部署）上部署代理程式，以支援 Zscaler 網際網路存取與 Azure Sentinel 之間的通訊。 下圖說明 Azure 中 Linux VM 的事件設定。

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果您在另一個雲端或內部部署機器中使用 VM，則會有此設定。 

 ![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>安全性考量

請務必根據貴組織的安全性原則來設定電腦的安全性。 例如，您可以設定您的網路以配合公司網路安全性原則，並變更背景程式中的埠和通訊協定，以符合您的需求。 您可以使用下列指示來改善您的電腦安全性性設定：  [Azure 中的安全 VM](../virtual-machines/linux/security-policy.md)、[網路安全性的最佳作法](../security/fundamentals/network-best-practices.md)。

若要在安全性解決方案和 Syslog 電腦之間使用 TLS 通訊，您必須將 Syslog daemon （rsyslog 或 Syslog）設定為在 TLS 中進行通訊：[使用 tls Rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)，[使用 tls 加密記錄檔訊息–syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。

 
## <a name="prerequisites"></a>必要條件
請確定您用來做為 proxy 的 Linux 機器正在執行下列其中一個作業系統：

- 64 位元
  - CentOS 6 和 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 和 7
  - Red Hat Enterprise Linux Server 6 和 7
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 位元
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 和 9
   - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
 - Daemon 版本
   - Syslog-ng： 2.1-3.22。1
   - Rsyslog： v8
  
 - 支援的 Syslog Rfc
   - Syslog RFC 3164
   - Syslog RFC 5424
 
請確定您的電腦也符合下列需求： 
- 權限
    - 您的電腦上必須具有更高的許可權（sudo）。 
- 軟體需求
    - 確定您的電腦上正在執行 Python
## <a name="step-1-deploy-the-agent"></a>步驟1：部署代理程式

在此步驟中，您必須選取將作為 Azure Sentinel 與安全性解決方案之間的 proxy 的 Linux 機器。 您將必須在 proxy 電腦上執行腳本，其如下所示：
- 會安裝 Log Analytics 代理程式，並視需要將它設定為透過 TCP 接聽埠514上的 Syslog 訊息，並將 CEF 訊息傳送至您的 Azure Sentinel 工作區。
- 設定 Syslog daemon，使用埠25226將 CEF 訊息轉送到 Log Analytics 代理程式。
- 設定 Syslog 代理程式來收集資料，並安全地將它傳送至 Log Analytics，並在其中進行剖析和擴充。
 
 
1. 在 Azure Sentinel 入口網站中，按一下 [**資料連線器**]，然後選取 [ **Zscaler** ] 和 [**開啟連接器] 頁面**。 

1. 在 **[安裝和設定 Syslog 代理程式**] 下，選取您的電腦類型： [Azure]、[其他雲端] 或 [內部部署]。 
   > [!NOTE]
   > 因為下一個步驟中的腳本會安裝 Log Analytics 代理程式，並將電腦連接到您的 Azure Sentinel 工作區，請確定這部電腦未連線到任何其他工作區。
1. 您的電腦上必須具有更高的許可權（sudo）。 請確定您的電腦上有 Python，並使用下列命令： `python –version`

1. 在您的 proxy 電腦上執行下列腳本。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 當腳本正在執行時，請檢查以確定您未收到任何錯誤或警告訊息。


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>步驟2：設定您的 Zscaler 以傳送 CEF 訊息

1. 在 Zscaler 設備上，您必須設定這些值，讓應用裝置根據 Log Analytics 代理程式，將必要的記錄檔傳送至 Azure Sentinel Syslog 代理程式。 您可以在應用裝置中修改這些參數，只要您也在 Azure Sentinel 代理程式上的 Syslog daemon 中加以修改即可。
    - 通訊協定 = TCP
    - 埠 = 514
    - 格式 = CEF
    - IP 位址-請務必將 CEF 訊息傳送到您專用於此用途之虛擬機器的 IP 位址。
 如需詳細資訊，請參閱[Zscaler Azure Sentinel 整合指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解決方案支援 Syslog RFC 3164 或 RFC 5424。


1. 若要在 Log Analytics 中針對 CEF 事件使用相關的架構，請搜尋 `CommonSecurityLog`。

## <a name="step-3-validate-connectivity"></a>步驟3：驗證連線能力

1. 開啟 Log Analytics，以確保使用 CommonSecurityLog 架構來接收記錄。<br> 可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 

1. 執行腳本之前，建議您從安全性解決方案傳送訊息，以確定它們正轉送至您設定的 Syslog proxy 電腦。 
1. 您的電腦上必須具有更高的許可權（sudo）。 請確定您的電腦上有 Python，並使用下列命令： `python –version`
1. 執行下列腳本，以檢查代理程式、Azure Sentinel 和安全性解決方案之間的連線能力。 它會檢查是否已正確設定背景程式轉送、接聽正確的埠，而且不會封鎖守護程式與 Log Analytics 代理程式之間的通訊。 此腳本也會傳送 mock 訊息 ' TestCommonEventFormat '，以檢查端對端連線能力。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Zscaler 網際網路存取連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

