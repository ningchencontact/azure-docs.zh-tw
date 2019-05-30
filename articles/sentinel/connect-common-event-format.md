---
title: CEF 資料連接至 Azure 的 Sentinel 預覽 |Microsoft Docs
description: 了解如何將 CEF 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 8e711c0586ce63d4293e2fb0914bbe884b55971f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389956"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>連接您外部解決方案中使用常見事件格式

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以連接 Azure Sentinel 外部的解決方案，可讓您將 Syslog 中儲存的記錄檔。 如果您的應用裝置可讓您將記錄檔儲存為 Syslog 常見事件格式 (CEF)，與 Azure Sentinel 整合可讓您輕鬆地執行分析和查詢資料。

> [!NOTE] 
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="how-it-works"></a>運作方式

Azure Sentinel 與您的 CEF 設備之間的連線會發生在三個步驟：

1. 在應用裝置上，您需要設定這些值，以便應用裝置會傳送所需之登入 Azure Sentinel Syslog 代理程式的必要格式。 只要您也在 Azure Sentinel 代理程式上的 Syslog 精靈修改，您可以修改這些參數在您的應用裝置。
    - 通訊協定 = UDP
    - 連接埠 = 514
    - 設備 = 本機 4
    - 格式 = CEF
2. Syslog 代理程式收集的資料，並將它安全地傳送至 Log Analytics，它會在其中剖析和豐富。
3. 代理程式在 Log Analytics 工作區中儲存的資料，因此如有需要使用分析、 相互關聯規則和儀表板可供查詢。

> [!NOTE]
> 代理程式可以從多個來源收集記錄，但必須安裝在專用的 proxy 電腦上。

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>步驟 1：連接到您的 CEF 應用裝置，透過專用的 Azure VM

您必須部署在專用的 Linux 機器上的代理程式 (VM 或內部部署上) 以支援應用裝置與 Azure Sentinel 之間的通訊。 您可以透過自動或手動來部署代理程式。 自動部署 Resource Manager 範本為基礎，而且您專用的 Linux 機器是您要在 Azure 中建立新的 VM 時，才可以使用。

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，您可以透過手動方式在現有的 Azure VM、在另一個雲端中的 VM 或在內部部署機器上部署代理程式。 

 ![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>部署在 Azure 中的代理程式


1. 在 Azure Sentinel 入口網站中，按一下**資料連接器**，然後選取您的設備類型。 

1. 底下**Linux Syslog 代理程式設定**:
   - 選擇**自動部署**如果您想要建立新的機器會預先安裝 Azure Sentinel 代理程式，並且包含所有組態必要，如上面所述。 選取 **自動部署**然後按一下**自動代理程式部署**。 這會帶您前往 [購買] 頁面會自動連接到您的工作區的專用 Linux 虛擬機器。 VM 處於**標準 D2s v3 系列 （2 個 Vcpu，8 GB 記憶體）** 且具有公用 IP 位址。
      1. 在 **自訂部署**頁面提供您詳細資料並選擇使用者名稱和密碼，如果您同意條款及條件，購買的 VM。
      1. 設定您的應用裝置傳送記錄檔使用 [連接] 頁面中列出的設定。 泛型的常見事件格式 connector，使用這些設定：
         - 通訊協定 = UDP
         - 連接埠 = 514
         - 設備 = 本機 4
         - 格式 = CEF
   - 選擇**手動部署**如果您想要使用現有的 VM 以專用的 Azure Sentinel 代理程式應該安裝至其上的 Linux 機器。 
      1. 底下**下載並安裝代理程式 Syslog**，選取**Azure Linux 虛擬機器**。 
      1. 在 **虛擬機器**隨即開啟的畫面中，選取您想要使用，然後按一下的機器**Connect**。
      1. 在 [連接器] 畫面中，在**設定和轉送的 Syslog**，將您的 Syslog 服務精靈是否**rsyslog.d**或**syslog ng**。 
      1. 複製下列命令，並在您的應用裝置上加以執行：
          - 如果您選取 rsyslog.d:
              
            1. 告訴 Syslog 服務精靈設備 local_4 上接聽，並將 Syslog 訊息傳送至 Azure 的 Sentinel 代理程式使用連接埠 25226。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
            
            1. 重新啟動 syslog 精靈 `sudo service rsyslog restart`<br> 如需詳細資訊，請參閱[rsyslog 文件](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - 如果您已選取 syslog ng:

              1. 告訴 Syslog 服務精靈設備 local_4 上接聽，並將 Syslog 訊息傳送至 Azure 的 Sentinel 代理程式使用連接埠 25226。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。

              3. 重新啟動 syslog 精靈 `sudo service syslog-ng restart` <br>如需詳細資訊，請參閱[syslog ng 文件](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. 重新啟動 Syslog 代理程式，使用下列命令： `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 確認沒有任何錯誤的代理程式記錄檔中執行下列命令： `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 若要在 Log Analytics 中的相關的結構描述使用 CEF 事件中，搜尋`CommonSecurityLog`。


### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>部署內部部署 Linux 伺服器上的代理程式

如果您未使用 Azure，以手動方式部署 Azure Sentinel 代理程式專用的 Linux 伺服器上執行。


1. 在 Azure Sentinel 入口網站中，按一下**資料連接器**，然後選取您的設備類型。
1. 底下建立專用的 Linux VM **Linux Syslog 代理程式設定**選擇**手動部署**。
   1. 底下**下載並安裝代理程式 Syslog**，選取**非 Azure Linux 機器**。 
   1. 在 **直接代理程式**畫面隨即開啟，並選取**Agent for Linux**下載代理程式，或執行下列命令來下載您的 Linux 機器上：   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. 在 [連接器] 畫面中，在**設定和轉送的 Syslog**，將您的 Syslog 服務精靈是否**rsyslog.d**或**syslog ng**。 
      1. 複製下列命令，並在您的應用裝置上加以執行：
         - 如果您選取 rsyslog:
           1. 告訴 Syslog 服務精靈設備 local_4 上接聽，並將 Syslog 訊息傳送至 Azure 的 Sentinel 代理程式使用連接埠 25226。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
           3. 重新啟動 syslog 精靈 `sudo service rsyslog restart`
         - 如果您已選取 syslog ng:
            1. 告訴 Syslog 服務精靈設備 local_4 上接聽，並將 Syslog 訊息傳送至 Azure 的 Sentinel 代理程式使用連接埠 25226。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
            3. 重新啟動 syslog 精靈 `sudo service syslog-ng restart`
      1. 重新啟動 Syslog 代理程式，使用下列命令： `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 確認沒有任何錯誤的代理程式記錄檔中執行下列命令： `tail /var/opt/microsoft/omsagent/log/omsagent.log`
  
 若要在 Log Analytics 中的相關的結構描述使用 CEF 事件中，搜尋`CommonSecurityLog`。

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>步驟 2：正向的常見事件格式 (CEF) 記錄到 Syslog 代理程式

設定您的安全性解決方案，以 CEF 格式的 Syslog 訊息傳送至 Syslog 代理程式。 請確定您使用相同的參數出現在您的代理程式設定。 這些通常是：

- 連接埠 514
- 設備 local4

## <a name="step-3-validate-connectivity"></a>步驟 3：驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 

1. 請確定您使用正確的設備。 此設備必須在您的應用裝置和 Azure Sentinel 相同。 您可以檢查您正在使用中 Azure Sentinel，修改檔案中的功能檔案`security-config-omsagent.conf`。 

2. 請確定您的記錄檔會進入 Syslog 代理程式中的正確連接埠。 Syslog 代理程式電腦上執行此命令：`tcpdump -A -ni any  port 514 -vv` 此命令會顯示從裝置到 Syslog 機器資料流處理的記錄檔。 請確定記錄檔會收到來源應用裝置上的右側連接埠和正確的設備。

3. 請確定您所傳送的記錄檔遵守[RFC 5424](https://tools.ietf.org/html/rfc542)。

4. 在電腦上執行 Syslog 代理程式，請確定這些連接埠 514、 25226 會開啟並在接聽，使用命令`netstat -a -n:`。 如需使用此命令的詳細資訊，請參閱[netstat(8)-Linux man 頁面](https://linux.die.net/man/8/netstat)。 如果它正在接聽正確，您會看到這個：

   ![Azure 的 Sentinel 連接埠](./media/connect-cef/ports.png) 

5. 請確定將精靈設定為接聽連接埠 514，在其上，您要傳送記錄檔。
    - Rsyslog:<br>請確定檔案`/etc/rsyslog.conf`包含這項設定：

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      如需詳細資訊，請參閱[imudp:UDP Syslog 輸入模組](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module)和[imtcp:TCP Syslog 輸入模組](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - 為 syslog-ng:<br>請確定檔案`/etc/syslog-ng/syslog-ng.conf`包含這項設定：

           # source s_network {
            network( transport(UDP) port(514));
             };
     如需詳細資訊，請參閱 [imudp:UDP Syslog 輸入模組] (如需詳細資訊，請參閱[syslog ng 開放來源版本 3.16-系統管理指南](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)。

1. 檢查 Syslog 服務精靈與代理程式之間的通訊。 Syslog 代理程式電腦上執行此命令：`tcpdump -A -ni any  port 25226 -vv` 此命令會顯示從裝置到 Syslog 機器資料流處理的記錄檔。請確定記錄檔，也在代理程式上接收。

6. 如果這兩個這些命令提供成功的結果，請檢查以查看您的記錄檔會同時抵達的 Log Analytics。 從這些設備串流處理的所有事件會都出現在 Log Analytics 中的未經處理格式`CommonSecurityLog`型別。

7. 檢查是否有錯誤，或如果未到達記錄檔，查看`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`。 如果它顯示有記錄檔格式不符的錯誤，請移至`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`並查看檔案`security_events.conf`並確定您的記錄符合 regex 格式，您會看到此檔案中。

8. 請確定您 Syslog 訊息的預設大小限制為 2048 個位元組 (2 KB)。 如果記錄檔太長，更新 security_events.conf 使用下列命令： `message_length_limit 4096`


## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連線至 Azure 的 Sentinel 的 CEF 設備。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

