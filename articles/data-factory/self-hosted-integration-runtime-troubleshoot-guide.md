---
title: 針對 Azure Data Factory 中的自我裝載整合執行時間進行疑難排解
description: 瞭解如何針對 Azure Data Factory 中的自我裝載整合執行時間問題進行疑難排解。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907278"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>針對自我裝載整合執行時間進行疑難排解

本文探討 Azure Data Factory 中自我裝載整合執行時間的常見疑難排解方法。

## <a name="common-errors-and-resolutions"></a>常見的錯誤和解決方式

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>錯誤訊息：自我裝載整合執行時間無法連線到雲端服務。

- **徵兆**： 

    ![自我裝載 IR 連接問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **原因**：自我裝載整合執行時間無法連線到 data factory 服務（後端）。 最常見的原因是防火牆中的網路設定。

- **解決方式**： 

    1. 檢查 windows 服務「Integration Runtime 服務」是否正在執行。
    
        ![自我裝載 IR 服務執行狀態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. 如果 [1] 中顯示的 windows 服務正在執行，請視需要遵循下列指示：

        1. 如果未在自我裝載整合執行時間上設定「proxy」（預設值為 [無 proxy 設定]），請在安裝自我裝載整合執行時間的電腦上執行下列 PowerShell 命令： 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > 服務 URL 可能會根據您的 data factory 位置而有所不同。 您可以在 ADF UI-> 連線-> Integration runtime-> 編輯自我裝載的 IR > 節點-> 視圖服務 Url 底下找到服務 URL。
            
            以下是預期的回應：
            
            ![Powershell 命令回應](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            如果回應不同，請視需要遵循下列指示：
            
            * 如果您收到「無法解析遠端名稱」錯誤，DNS 會發生問題。 請與網路小組聯絡，以修正 DNS 解析問題！ 
            * 如果您收到「ssl/tls 憑證不受信任」錯誤，請檢查電腦上是否信任「 https://wu2.frontend.clouddatahub.net/」的憑證，使用「憑證管理員」安裝公開憑證，這應該會降低此問題。
            * 檢查 Windows > 事件檢視器（記錄）-> 的應用程式和服務記錄檔-> Integration Runtime 是否有任何失敗，主要是由 DNS、防火牆規則和公司的網路設定（Forcedly 關閉連線）所造成。 針對此問題，請洽詢您的網路小組進行進一步的疑難排解，因為每一家公司都有自訂的網路設定。

        2. 如果已在自我裝載整合執行時間上設定 "proxy"，請確認您的 proxy 伺服器是否能夠存取我們的服務端點。 如需範例命令，請[參閱。](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)    
                
            ```powershell
            $user = $env:username
            $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
            Settings').ProxyServer
            $pwd = Read-Host "Password?" -assecurestring
            $proxy = new-object System.Net.WebProxy
            $proxy.Address = $webproxy
            $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
            $proxy.credentials = $account
            $url = "https://wu2.frontend.clouddatahub.net/"
            $wc = new-object system.net.WebClient
            $wc.proxy = $proxy
            $webpage = $wc.DownloadData($url)
            $string = [System.Text.Encoding]::ASCII.GetString($webpage)
            $string
            ```

            以下是預期的回應：
            
            ![Powershell 命令回應2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Proxy 考慮：
            > * 檢查 proxy 伺服器是否需要允許清單。 若是如此，請將[這些網域](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)列入允許清單。
            > * 檢查 proxy 伺服器上的 "wu2.frontend.clouddatahub.net/" 的 TLS/SSL 憑證是否受信任。
            > * 如果您使用 proxy 中的 active directory 驗證，請將服務帳戶變更為可存取 proxy 的使用者帳戶（「Integration Runtime 服務」）。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>錯誤訊息：自我裝載整合執行時間節點/邏輯 SHIR 處於非作用中/「執行中（有限制）」狀態

- **原因**：您可能會看到自我裝載 IR 節點處於非作用中狀態，如下列螢幕擷取畫面所示：

    ![非使用中的自我裝載 IR 節點](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    當節點無法彼此通訊時，就會發生這種情況。 

- **解決方式**： 

    登入裝載的 VM 節點，並開啟 應用程式及服務記錄檔-> Integration Runtime 下的 事件 視圖，篩選所有錯誤記錄檔。 

     1. 如果錯誤記錄檔包含： 
    
        **錯誤記錄**檔： EndpointNotFoundException：無法連接到 net.tcp：//xxxxxxx.bwld.com： 8060/ExternalService .Svc/WorkerManager。 連接嘗試持續等待00：00：00.9940994 的時間範圍。 TCP 錯誤碼10061：無法建立連線，因為目的電腦主動拒絕它10.2.4.10：8060。  ---> SocketException：無法建立連線，因為目的電腦主動拒絕它10.2.4.10：8060
    
           在系統 DoConnect （端點 endPointSnapshot、SocketAddress socketAddress）
           
           在系統 .Net. Socket. Connect （端點 remoteEP）
           
           在 System.servicemodel. SocketConnectionInitiator （Uri uri，TimeSpan timeout）
    
        **解決方案：** 啟動命令列： telnet 10.2.4.10 8060
        
        如果您遇到下列錯誤，請洽詢您的 IT 人員，以取得修正此問題的協助。 成功 telnet 之後，如果您仍然遇到 IR 節點狀態的問題，請洽詢 Microsoft 支援服務。
        
        ![命令列錯誤](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. 如果錯誤記錄檔包含：
     
        **錯誤記錄檔：** 無法連線至背景工作角色管理員： net.tcp：//xxxxxx： 8060/ExternalService .svc/沒有主機 azranlcir01r1 的 DNS 專案存在。 沒有這類主機是已知的例外狀況詳細資料： EndpointNotFoundException：沒有主機 xxxxx 的 DNS 專案存在。 ---> SocketException：不知道這種主機在系統 GetAddrInfo （字串名稱）的 InternalGetHostByName （字串主機名稱，布林 includeIPv6），位於系統 .net. dns. System.net.dns.gethostentry （String hostNameOrAddress）位於 DnsCache （Uri uri）---內部例外狀況堆疊追蹤的結尾---伺服器堆疊追蹤：在 System.servicemodel. DnsCache. Resolve （Uri uri） 
    
        **解決方案：** 下列兩個動作的其中一項可協助解決此問題：
         1. 將所有節點放在相同的網域中。
         2. 在所有託管 VM 的 hosts 檔案中新增 IP 到主機的對應。


## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 對應資料流程效能指南](concepts-data-flow-performance.md)
