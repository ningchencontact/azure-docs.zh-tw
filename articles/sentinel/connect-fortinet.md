---
title: 將 Fortinet 資料連線至 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Fortinet 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679270"
---
# <a name="connect-your-fortinet-appliance"></a>連接您的 Fortinet 應用裝置

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供。 我們不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊, 請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

您可以將記錄檔儲存為 Syslog 一般事件格式 (CEF), 以將 Azure Sentinel 連接到任何 Fortinet 設備。 透過與 Azure Sentinel 的整合, 您可以輕鬆地從 Fortinet 跨記錄檔資料執行分析和查詢。 如需有關 Azure Sentinel 如何內嵌 CEF 資料的詳細資訊, 請參閱[連接 CEF 設備](connect-common-event-format.md)。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>步驟 1:使用代理程式連接您的 Fortinet 應用裝置

若要將您的 Fortinet 應用裝置連線到 Azure Sentinel, 請在專用的 VM 或內部部署機器上部署代理程式, 以支援設備和 Azure Sentinel 之間的通訊。 

您也可以在現有的 Azure VM、另一個雲端中的 VM 或在內部部署機器上手動部署代理程式。

> [!NOTE]
> 請務必根據貴組織的安全性原則來設定電腦的安全性。 例如, 您可以設定您的網路以配合公司網路安全性原則, 並變更背景程式中的埠和通訊協定, 以符合您的需求。 

若要查看這兩個選項的網狀圖, 請參閱[連接資料來源](connect-data-sources.md#agent-options)。

### <a name="deploy-the-agent"></a>部署代理程式

1. 在 Azure Sentinel 入口網站中, 按一下 [**資料連線器**], 然後選取 [ **Fortinet** ] 和 [**開啟連接器] 頁面**。 

1. 在 **[下載並安裝 Syslog 代理程式**] 底下, 選取 [Azure] 或 [內部部署] 電腦類型。 
1. 在開啟的 [**虛擬機器**] 畫面中, 選取您要使用的機器, 然後按一下 [連線 **]** 。
1. 如果您選擇 [**下載並安裝 Azure Linux 虛擬機器的代理程式**], 請選取該機器, 然後按一下 [連線 **]** 。 如果您選擇 [**下載並安裝非 Azure Linux 虛擬機器的代理程式**], 請在 [**直接代理程式**] 畫面中, 執行 [**下載並上架代理程式**] 底下的腳本。
1. 在 [連接器] 畫面的 [**設定並轉寄 Syslog**] 底下, 設定 syslog daemon 為**rsyslog**或**syslog-ng**。 
1. 複製這些命令, 並在您的應用裝置上執行:
   - 如果您已選取 [rsyslog]:
            
     1. 告訴 Syslog 守護程式在設備 local_4 上接聽, 並使用埠25226將 Syslog 訊息傳送至 Azure Sentinel 代理程式。 使用此命令:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. 下載並安裝[security_events 設定檔](https://aka.ms/asi-syslog-config-file-linux), 將 Syslog 代理程式設定為在埠25226上接聽。 使用此命令: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , {0}其中應取代為您的工作區 GUID。
     1. 使用此命令重新開機 syslog daemon:`sudo service rsyslog restart`
            
   - 如果您選取 syslog-ng:

      1. 告訴 Syslog 守護程式在設備 local_4 上接聽, 並使用埠25226將 Syslog 訊息傳送至 Azure Sentinel 代理程式。 使用此命令:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. 下載並安裝[security_events 設定檔](https://aka.ms/asi-syslog-config-file-linux), 將 Syslog 代理程式設定為在埠25226上接聽。 使用此命令: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , {0}其中應取代為您的工作區 GUID。
      1. 使用此命令重新開機 syslog daemon:`sudo service syslog-ng restart`
1. 使用此命令重新開機 Syslog 代理程式:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 執行下列命令, 確認代理程式記錄檔中沒有任何錯誤:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>步驟 2:將 Fortinet 記錄轉送到 Syslog 代理程式

設定 Fortinet, 以透過 Syslog 代理程式將 Syslog 訊息的 CEF 格式轉送至您的 Azure 工作區。

1. 在您的 Fortinet 應用裝置上開啟 CLI, 然後執行下列命令:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 以代理程式的 IP 位址取代伺服器的**ip 位址**。
    - 將**facility_name**設定為使用您在代理程式中設定的設備。 根據預設, 代理程式會將此設定為 local4。
    - 將**syslog 埠**設定為**514**或代理程式上設定的埠。
    - 若要在早期 FortiOS 版本中啟用 CEF 格式, 您可能需要執行命令組**csv disable**。
 
   > [!NOTE] 
   > 如需詳細資訊, 請移至[Fortinet 文件庫](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)。 選取您的版本, 並使用**手冊**和**記錄訊息參考**。

 若要在 Fortinet 事件的 Azure 監視器 Log Analytics 中使用相關的架構, 請`CommonSecurityLog`搜尋。


## <a name="step-3-validate-connectivity"></a>步驟 3：驗證連線能力

最多可能需要20分鐘的時間, 您的記錄才會開始出現在 Log Analytics 中。 

1. 請確定您使用的是正確的設備。 設備和 Azure Sentinel 中的設備必須相同。 您可以在 Azure Sentinel 中檢查您所使用的設施檔案, 並在檔案中`security-config-omsagent.conf`加以修改。 

2. 請確定您的記錄檔會到達 Syslog 代理程式中的正確埠。 在 Syslog 代理程式電腦上執行此命令`tcpdump -A -ni any  port 514 -vv`:。 此命令會顯示從裝置串流到 Syslog 電腦的記錄。 請確定已從正確埠上的來源應用裝置接收記錄, 以及正確的設備。

3. 請確定您傳送的記錄符合[RFC 3164](https://tools.ietf.org/html/rfc3164)。

4. 在執行 Syslog 代理程式的電腦上, 請確定埠514和25226已開啟, 並使用命令`netstat -a -n:`進行接聽。 如需使用此命令的詳細資訊, 請參閱[netstat (8)-Linux man 頁面](https://linux.die.net/man/8/netstat)。 如果它接聽正常, 您會看到:

   ![Azure Sentinel 埠](./media/connect-cef/ports.png) 

5. 請確定守護程式已設定為在您要傳送記錄的埠514上接聽。
    - 針對 rsyslog:<br>請確定`/etc/rsyslog.conf`檔案包含下列設定:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      如需詳細資訊, [請參閱 imudp:UDP Syslog 輸入模組](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module)和[imtcp:TCP Syslog 輸入模組](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)。

   - 針對 syslog-ng:<br>請確定`/etc/syslog-ng/syslog-ng.conf`檔案包含下列設定:

           # source s_network {
            network( transport(UDP) port(514));
             };
     如需詳細資訊, [請參閱 imudp:UDP Syslog 輸入模組](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html)和[Syslog-ng 開放原始碼版本 3.16-系統管理指南](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)。

1. 檢查 Syslog daemon 和代理程式之間是否有通訊。 在 Syslog 代理程式電腦上執行此命令`tcpdump -A -ni any  port 25226 -vv`:。 此命令會顯示從裝置串流到 Syslog 電腦的記錄。 請確定代理程式上也收到記錄。

6. 如果這兩個命令都提供成功的結果, 請檢查 Log Analytics, 以查看您的記錄是否到達。 所有從這些設備串流的事件都會以原始格式顯示在 Log `CommonSecurityLog` Analytics 的 [類型] 之下。

7. 若要檢查是否有錯誤, 或記錄檔未抵達, 請查看`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`。 如果出現記錄檔格式不符的錯誤, 請移至`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , 並查看`security_events.conf`檔案。 請確定您的記錄符合您在此檔案中看到的 RegEx 格式。

8. 請確定您的 Syslog 訊息預設大小限制為2048個位元組 (2 KB)。 如果記錄太長, 請使用下列命令更新 security_events:`message_length_limit 4096`

10. 如果代理程式未收到您的 Fortinet 記錄, 請執行此命令, 視您使用的 Syslog daemon 類型而定, 以設定設備並將記錄設定為搜尋記錄檔中的 Fortinet 一詞:
       - rsyslog。 d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     使用此命令重新開機 Syslog daemon:`sudo service rsyslog restart`
       - syslog-ng:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     使用此命令重新開機 Syslog daemon:`sudo service syslog-ng restart`

## <a name="next-steps"></a>後續步驟
在本文中, 您已瞭解如何將 Fortinet 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

