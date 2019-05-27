---
title: 將檢查點資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將檢查點資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: c487856c7fb959f684700dee1d463783954b1a53
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921956"
---
# <a name="connect-your-check-point-appliance"></a>連接您的檢查點的設備

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以連接 Azure Sentinel 至任何檢查點的設備為 Syslog CEF 儲存記錄檔。 與 Azure Sentinel 整合可讓您輕鬆地分析和查詢執行的記錄檔資料之間的從檢查點。 如需有關如何內嵌 Azure Sentinel CEF 資料的詳細資訊，請參閱 <<c0> [ 連線 CEF 設備](connect-common-event-format.md)。

> [!NOTE]
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>步驟 1：連接您的檢查點設備使用代理程式

若要連接您的檢查點的設備 Azure Sentinel，您需要代理程式部署到專用的機器 （VM 或內部） 支援的設備與 Azure Sentinel 之間的通訊。 您可以透過自動或手動來部署代理程式。 只有當您的專用機器是在 Azure 中建立的新 VM 時，才能使用自動部署。 

或者，您可以透過手動方式在現有的 Azure VM、在另一個雲端中的 VM 或在內部部署機器上部署代理程式。

若要查看這兩個選項的網路圖表，請參閱[將資料來源連接](connect-data-sources.md)。

### <a name="deploy-the-agent-in-azure"></a>部署在 Azure 中的代理程式

1. 在 Azure Sentinel 入口網站中，按一下**資料連接器**，然後選取您的設備類型。 

1. 底下**Linux Syslog 代理程式設定**:
   - 選擇**自動部署**如果您想要建立新的機器會預先安裝 Azure Sentinel 代理程式，並且包含所有組態必要，如上面所述。 選取 **自動部署**然後按一下**自動代理程式部署**。 這會帶您前往 [購買] 頁面會自動連接到您的工作區的專用 vm。 VM 處於**標準 D2s v3 系列 （2 個 Vcpu，8 GB 記憶體）** 且具有公用 IP 位址。
     1. 在 **自訂部署**頁面提供您詳細資料並選擇使用者名稱和密碼，如果您同意條款及條件，購買的 VM。
      
        1. 若要確定所有的檢查點記錄檔將會對應至 Azure 的 Sentinel 代理程式 Syslog 代理程式電腦上執行下列命令：
           - 如果您使用 Syslog ng，執行下列命令 （請注意，它會重新啟動 Syslog 代理程式）：
            
                sudo bash-c"printf ' 篩選 f_local4_oms {facility(local4);}; \n 個目的地 security_oms {tcp (\"127.0.0.1\" port(25226));}; {source(src); filter(f_local4_oms) destination(security_oms);} 「 \n 」 記錄檔; \n\nfilter f_msg_oms {比對 (\"Check Point\"值 (\"訊息\"));}; \n 目的地 security_msg_oms {tcp (\"127.0.0.1\" port(25226));}; \n 記錄檔 {source(src) filter(f_msg_oms); destination(security_msg_oms);};' > /etc/syslog-ng/security-config-omsagent.conf"

             重新啟動 Syslog 精靈： `sudo service syslog-ng restart`
           - 如果您使用 rsyslog，執行下列命令 （請注意，它會重新啟動 Syslog 代理程式）：
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             重新啟動 Syslog 精靈： `sudo service rsyslog restart`

   - 選擇**手動部署**如果您想要使用現有的 VM 以專用的 Azure Sentinel 代理程式應該安裝至其上的 Linux 機器。 
      1. 底下**下載並安裝代理程式 Syslog**，選取**Azure Linux 虛擬機器**。 
      1. 在 **虛擬機器**隨即開啟的畫面中，選取您想要使用，然後按一下的機器**Connect**。
      1. 在 [連接器] 畫面中，在**設定和轉送的 Syslog**，將您的 Syslog 服務精靈是否**rsyslog.d**或**syslog ng**。 
      1. 複製下列命令，並在您的應用裝置上加以執行：
          - 如果您選取 rsyslog.d:
              
            1. 告訴 Syslog 服務精靈來接聽 「 檢查點 」，並於設備 local_4 及 Azure Sentinel 代理程式使用連接埠 25226 傳送 Syslog 訊息。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
           
            1. 重新啟動 syslog 精靈 `sudo service rsyslog restart`
             
          - 如果您已選取 syslog ng:

              1. 告訴 Syslog 服務精靈來接聽 「 檢查點 」，並於設備 local_4 及 Azure Sentinel 代理程式使用連接埠 25226 傳送 Syslog 訊息。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。

              3. 重新啟動 syslog 精靈 `sudo service syslog-ng restart`
      2. 重新啟動 Syslog 代理程式，使用下列命令： `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 確認沒有任何錯誤的代理程式記錄檔中執行下列命令： `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>部署內部部署 Linux 伺服器上的代理程式

如果您未使用 Azure，以手動方式部署 Azure Sentinel 代理程式專用的 Linux 伺服器上執行。

1. 底下建立專用的 Linux VM **Linux Syslog 代理程式設定**選擇**手動部署**。
   1. 底下**下載並安裝代理程式 Syslog**，選取**非 Azure Linux 機器**。 
   1. 在 **直接代理程式**畫面隨即開啟，並選取**Agent for Linux**下載代理程式，或執行下列命令來下載您的 Linux 機器上：   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. 在 [連接器] 畫面中，在**設定和轉送的 Syslog**，將您的 Syslog 服務精靈是否**rsyslog.d**或**syslog ng**。 
      1. 複製下列命令，並在您的應用裝置上加以執行：
         - 如果您選取**rsyslog**:
           1. 告訴 Syslog 服務精靈來接聽 「 檢查點 」，並於設備 local_4 及 Azure Sentinel 代理程式使用連接埠 25226 傳送 Syslog 訊息。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
           3. 重新啟動 syslog 精靈 `sudo service rsyslog restart`
         - 如果您選取**syslog ng**:
            1. 告訴 Syslog 服務精靈來接聽 「 檢查點 」，並於設備 local_4 及 Azure Sentinel 代理程式使用連接埠 25226 傳送 Syslog 訊息。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. 下載並安裝[security_events 組態檔](https://aka.ms/asi-syslog-config-file-linux)，會設定為接聽連接埠 25226 上的 Syslog 代理程式。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 其中{0}應該取代為您的工作區的 GUID。
            3. 重新啟動 syslog 精靈 `sudo service syslog-ng restart`
      1. 重新啟動 Syslog 代理程式，使用下列命令： `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 確認沒有任何錯誤的代理程式記錄檔中執行下列命令： `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>步驟 2：順向的檢查點記錄至 Syslog 代理程式

設定 Check Point 設備轉送至 Azure 透過 Syslog 代理程式工作區的 CEF 格式的 Syslog 訊息。

1. 移至[檢查點記錄檔匯出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
2. 向下捲動至**基本部署**並遵循指示來設定連線，使用下列指導方針：
   - 設定**Syslog 連接埠**要**514**或您設定代理程式的連接埠。
     - 取代**名稱**並**目標伺服器的 IP 位址**在 CLI 中的 Syslog 代理程式名稱和 IP 位址。
     - 將格式設為**CEF**。
3. 如果您使用版本 R77.30 或 R80.10，向上捲動至**安裝**並遵循指示來安裝您的版本記錄匯出工具。
 
## <a name="step-3-validate-connectivity"></a>步驟 3：驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 

1. 請確定您使用正確的設備。 此設備必須在您的應用裝置和 Azure Sentinel 相同。 您可以檢查您正在使用中 Azure Sentinel，修改檔案中的功能檔案`security-config-omsagent.conf`。 

2. 請確定您的記錄檔會進入 Syslog 代理程式中的正確連接埠。 Syslog 代理程式電腦上執行此命令：`tcpdump -A -ni any  port 514 -vv` 此命令會顯示從裝置到 Syslog 機器資料流處理的記錄檔。請確定記錄檔會收到來源應用裝置上的右側連接埠和正確的設備。

3. 請確定您所傳送的記錄檔遵守[RFC 5424](https://tools.ietf.org/html/rfc542)。

4. 在電腦上執行 Syslog 代理程式，請確定這些連接埠 514、 25226 會開啟並在接聽，使用命令`netstat -a -n:`。 如需使用此命令的詳細資訊，請參閱[netstat(8)-Linux man 頁面](https://linux.die.netman/8/netstat)。 如果它正在接聽正確，您會看到這個：

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

4. 請務必執行下列命令：
  
   - 如果您使用 Syslog ng，執行下列命令 （請注意，它會重新啟動 Syslog 代理程式）：

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        重新啟動 Syslog 精靈： `sudo service syslog-ng restart`

   - 如果您使用 rsyslog，執行下列命令 （請注意，它會重新啟動 Syslog 代理程式）： 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     重新啟動 Syslog 精靈： `sudo service rsyslog restart`




## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Check Point 設備。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

