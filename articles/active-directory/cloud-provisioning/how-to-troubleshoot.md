---
title: Azure AD Connect 雲端布建疑難排解
description: 本檔說明如何針對雲端布建代理程式可能發生的問題進行疑難排解。
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794233"
---
# <a name="cloud-provisioning-troubleshooting"></a>雲端布建疑難排解

雲端布建觸及許多不同的專案，並有許多不同的相依性。  自然地，這可能會增加各種問題。  本檔的設計目的是要協助您開始針對這些問題進行疑難排解。  本檔將為您介紹應專注于的一般區域、如何收集其他資訊，以及可用於追蹤問題的各種技術。  


## <a name="common-troubleshooting-areas"></a>常見的疑難排解區域

|Name|描述|
|-----|-----|
|[代理程式問題](#agent-issues)|請確認代理程式已正確安裝，且正在與 Azure AD 通訊。|
|[物件同步處理問題](#object-synchronization-issues)|使用布建記錄來針對物件同步處理問題進行疑難排解。|
|[布建隔離問題](#provisioning-quarantined-issues)|瞭解布建隔離問題，以及如何加以修正。|


## <a name="agent-issues"></a>代理程式問題
您想要向代理程式驗證的一些第一件事是：


-  是否已安裝？
-  代理程式是否正在本機執行？
-  代理程式在入口網站中嗎？
-  代理程式是否標示為狀況良好？  

這些專案可以在 Azure 入口網站以及執行代理程式的本機伺服器上進行驗證。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證

若要確認 Azure 正在查看代理程式，且狀況良好，請遵循下列步驟：

1. 登入 Azure 入口網站。
2. 在左側選取 [ **Azure Active Directory**]，按一下 [ **Azure AD Connect** ]，然後在中央選取 [管理布建 **（預覽）** ]。
3.  在 [ **Azure AD 布建（預覽）** ] 畫面上，按一下 [**審查所有代理**程式]。
 ![Azure AD 布建](media/how-to-install/install7.png)</br>
 
4. 在 [**內部部署布建代理**程式] 畫面上，您會看到已安裝的代理程式。  確認有問題的代理程式存在，而且標示為**狀況良好**。
 ![布建代理程式](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>驗證埠

若要確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊，您可以使用下列工具：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試會驗證您的代理程式是否能夠透過埠443與 Azure 通訊。  開啟瀏覽器，並從安裝代理程式的伺服器流覽至上述 URL。
 ![服務](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本機伺服器上

若要確認代理程式是否正在執行，請遵循下列步驟：

1.  在安裝代理程式的伺服器上，藉由流覽至它或移至 [開始]/[執行/服務] 來開啟 [**服務**]。
2.  在 [**服務**] 下，確定**Microsoft Azure AD 連接代理程式更新程式**，並**Microsoft Azure AD connect 布建代理程式**已存在，且**狀態為 [** 執行中]。
 ![服務](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>常見的代理程式安裝問題

以下是一些常見的代理程式安裝問題，以及一般的解決方式。

#### <a name="agent-failed-to-start"></a>代理程式無法啟動

如果您收到錯誤訊息，指出：

**服務 ' Microsfoft Azure AD Connect 布建代理程式 ' 無法啟動。 請確認您有足夠的許可權可以啟動系統服務。** 

這通常是因為群組原則導致無法將許可權套用至安裝程式所建立的本機 NT 服務「登入帳戶」（NT SERVICE\AADConnectProvisioningAgent），所以需要這些許可權才能啟動服務。

若要解決此問題，請使用下列步驟：

1.  使用系統管理員帳戶登入伺服器
2.  藉由流覽至該**服務**或移至 [開始]/[執行中]/[services.msc] 來開啟它們。
3.  在 [**服務**] 下，按兩下 [Microsoft Azure AD 連線布建**代理程式]**
4. 在 [] 索引標籤上，將「登入帳戶」變更為網域系統管理員，然後重新開機服務。 

 ![服務](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>代理程式超時或憑證無效

如果您嘗試註冊代理程式，您可能會收到下列錯誤。

 ![服務](media/how-to-troubleshoot/troubleshoot4.png)

這通常是因為代理程式無法連線到混合式身分識別服務，而且需要設定 HTTP proxy。  若要解決此問題，請設定輸出 proxy。 

「佈建代理程式」支援使用連出 Proxy。 您可以藉由編輯代理程式設定檔**C:\Program Files\Microsoft Azure AD Connect**布建 Agent\AADConnectProvisioningAgent.exe.config 來設定它。將下列幾行新增到結尾 `</configuration>` 標記前面的檔案結尾。
以您的 Proxy 伺服器名稱和連接埠值取代 [proxy-server] 和 [proxy-port] 變數。

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>代理程式註冊失敗，發生安全性錯誤

安裝雲端布建代理程式時，您可能會看到下列錯誤。

這通常是因為本機 PowerShell 執行原則導致代理程式無法執行 PowerShell 註冊腳本所造成。

若要解決此問題，請變更伺服器上的 PowerShell 執行原則。 您的電腦和使用者原則必須是 "Undefined" 或 "RemoteSigned"。 如果它是「不受限制」，您會看到此錯誤。  如需詳細資訊，請參閱[PowerShell 執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)。 

### <a name="log-files"></a>記錄檔

根據預設，代理程式會發出非常少的錯誤訊息和堆疊追蹤資訊。 您可以在下列資料夾中找到這些追蹤記錄： **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect**布建 Agent\Trace

使用下列步驟來收集針對代理程式相關問題進行疑難排解的其他詳細資料。

1. 停止服務**Microsoft Azure AD 連接布建代理程式**
2. 建立原始設定檔的複本： **C:\Program Files\Microsoft Azure AD Connect**布建 Agent\AADConnectProvisioningAgent.exe.config
3. 以下列內容取代現有的 < diagnostics > 區段，所有追蹤訊息將會移至**ProvAgentTrace**檔案

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. 啟動服務**Microsoft Azure AD 連接布建代理程式**
5. 您可以使用下列命令來結尾檔案和調試問題： 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>物件同步處理問題

下一節包含疑難排解物件同步處理的資訊。

### <a name="provisioning-logs"></a>佈建記錄

在 Azure 入口網站中，您可以使用布建記錄來協助追蹤和疑難排解物件同步處理的問題。  若要查看記錄，請選取 [**記錄**]。
 ![布建記錄檔](media/how-to-troubleshoot/log1.png)

布建記錄會提供有關在內部部署 AD 環境與 Azure 之間同步處理之物件狀態的豐富資訊。

 ![佈建記錄](media/how-to-troubleshoot/log2.png)

您可以使用頁面頂端的下拉式清單，在特定問題、日期等中，將此視圖篩選為零。 按兩下個別事件將會提供其他詳細資訊。

 ![佈建記錄](media/how-to-troubleshoot/log3.png)

此資訊將提供詳細的步驟，以及發生同步處理問題的位置。  因此，可讓您將零放在中，並找出問題的確切位置。


## <a name="provisioning-quarantined-issues"></a>布建隔離的問題

雲端布建會監視您設定的健康情況，並將狀況不良的物件置於「隔離」狀態。 如果對目標系統進行的大部分或所有呼叫一直因為錯誤而失敗（例如，不正確系統管理員認證），則布建工作會標示為在隔離中。

 ![隔離](media/how-to-troubleshoot/quarantine1.png)

按一下 [狀態]，即可查看有關隔離的其他資訊。  您也可以取得錯誤碼和訊息。

 ![隔離](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>解析隔離

- 使用 Azure 入口網站重新開機布建作業。 在 [代理程式設定] 頁面上，選取 [**重新開機**布建]。

  ![隔離](media/how-to-troubleshoot/quarantine3.png)

- 使用 Microsoft Graph[重新開機](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)布建作業。 您將可完全掌控重新開機的內容。 您可以選擇清除 [escrows] （以重新開機會回報隔離狀態的 [託管] 計數器）、[清除隔離] （以移除應用程式的隔離），或 [清除浮水印]。 使用下列要求：
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)



