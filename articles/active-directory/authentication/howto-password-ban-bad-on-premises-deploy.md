---
title: 部署 Azure AD 密碼保護 (預覽)
description: 部署 Azure AD 密碼保護 (預覽) 以禁止在內部部署環境中使用錯誤的密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4a4b760652ce38e27e12e9eb73fbe7692eddbc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204367"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>預覽：部署 Azure AD 密碼保護

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

既然我們已了解[如何強制執行適用於 Windows Server Active Directory 的 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)，下一步就是規劃並執行部署。

## <a name="deployment-strategy"></a>部署策略

Microsoft 建議進行任何部署時，都以稽核模式開始。 稽核模式是預設的初始設定，除了可以繼續設定密碼之外，任何會被封鎖的行為都會在事件記錄檔中建立項目。 以稽核模式完全部署 Proxy 伺服器和 DC 代理程式之後，應該執行定期監視，以便判斷當強制執行密碼原則時，密碼原則強制執行會對使用者和環境造成什麼影響。

在稽核階段，許多組織都發現：

* 它們必須改善現有的作業程序，才能使用更安全的密碼。
* 使用者習慣於定期選擇不安全的密碼
* 它們必須通知使用者關於即將推出的安全性強制執行變更、此變更可能對他們造成的影響，並協助他們更深入了解如何選擇更安全的密碼。

以稽核模式執行此功能達一段合理的時間之後，強制執行設定即可從 [稽核] 翻轉成 [強制]，從而要求更安全的密碼。 在此期間進行專注的監視是個不錯的主意。

## <a name="deployment-requirements"></a>部署需求

* 即將安裝 Azure AD 密碼保護 DC 代理程式服務的所有網域控制站，都必須執行 Windows Server 2012 或更新版本。
* 即將安裝 Azure AD 密碼保護 Proxy 服務的所有機器，都必須執行 Windows Server 2012 R2 或更新版本。
* 安裝 Azure AD 密碼保護元件的所有機器 (包括網域控制站)，都必須安裝 Universal C 執行階段。
透過 Windows Update 來完整修補此機器，是達到此需求的較佳做法。 否則，可能會安裝適當的 OS 專屬更新套件 - 請參閱[更新 Windows 中的 Universal C 執行階段](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* 每個網域中至少要有一個網域控制站有網路連線，且至少要有一部伺服器裝載 Azure AD 密碼保護 Proxy 服務。 此連線必須允許網域控制站存取 Proxy 服務上的 RPC 端點對應程式連接埠 (135) 與 RPC 伺服器連接埠。 根據預設，RPC 伺服器連接埠是動態 RPC 連接埠，但可設定 (請參閱下文) 為使用靜態連接埠。
* 裝載 Azure AD 密碼保護 Proxy 服務的所有電腦都必須具備下列端點的網路存取權：

    |端點 |目的|
    | --- | --- |
    |`https://login.microsoftonline.com`|驗證要求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密碼保護功能|

* 一個全域系統管理員帳戶，用以向 Azure AD 註冊 Azure AD 密碼保護 Proxy 服務與樹系。
* 具樹系根網域中 Active Directory 網域系統管理員權限的帳戶，向 Azure AD 註冊 Windows Server Active Directory 樹系。
* 執行 DC 代理程式服務軟體的任何 Active Directory 網域，都必須使用 DFSR 進行 sysvol 複寫。

## <a name="single-forest-deployment"></a>單一樹系部署

下圖顯示 Azure AD 密碼保護的基本元件在內部部署 Active Directory 環境中如何一起運作。  

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>下載軟體

Azure AD 密碼保護有兩個可從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)下載的必要安裝程式

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>安裝並設定 Azure AD 密碼保護 Proxy 服務

1. 選擇一或多個要裝載 Azure AD 密碼保護 Proxy 服務的伺服器。
   * 每個這類服務只能為一個樹系提供密碼原則，而且主機電腦必須已加入該樹系中某個網域 (同時支援根網域和子網域) 的網域。 為了讓 Azure AD 密碼保護 Proxy 服務完成其任務，樹系的每個網域中必須至少有一個 DC 與 Azure AD 密碼保護 Proxy 電腦之間有網路連線。
   * 支援在網域控制站上安裝並執行 Azure AD 密碼保護 Proxy 服務來進行測試，缺點是網域控制站需要網際網路連線能力，而可能會有安全疑慮。 Microsoft 建議僅針對測試使用此設定。
   * 基於備援目的，建議至少有兩個 Proxy 伺服器。 [請參閱高可用性](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. 使用 AzureADPasswordProtectionProxySetup.msi MSI 套件來安裝 Azure AD 密碼保護 Proxy 服務。
   * 軟體安裝並不需要重新開機。 您可以使用標準 MSI 程序來自動執行軟體安裝，例如：`msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > 安裝 AzureADPasswordProtectionProxySetup.msi MSI 套件之前，Windows 防火牆服務必須正在執行，否則會發生安裝錯誤。 如果 Windows 防火牆設定為不執行，則因應措施是在安裝過程中暫時啟用並啟動 Windows 防火牆服務。 安裝後，Proxy 軟體對 Windows 防火牆軟體沒有特定相依性。 如果您使用第三方防火牆，仍必須加以設定來滿足部署需求 (允許對連接埠 135 和動態或靜態 Proxy RPC 伺服器連接埠的輸入存取)。 [請參閱部署需求](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. 以系統管理員身分開啟 PowerShell 視窗。
   * Azure AD 密碼保護 Proxy 軟體包含一個名為 AzureADPasswordProtection 的新 PowerShell 模組。 下列步驟就是以從這個 PowerShell 模組執行各種 Cmdlet 為基礎，並假設您已開啟一個新的 PowerShell 視窗，而且已依下列方式匯入新的模組：

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * 使用下列 PowerShell 命令來確認服務是否正在執行：`Get-Service AzureADPasswordProtectionProxy | fl`。
     結果應該會產生一個 **Status** 傳回 "Running" 的結果。

4. 註冊 Proxy。
   * 完成步驟 3 之後，Azure AD 密碼保護 Proxy 服務就會在電腦上執行，但還沒有與 Azure AD 通訊所需的必要認證。 必須使用 `Register-AzureADPasswordProtectionProxy` PowerShell Cmdlet 向 Azure AD 註冊，才能啟用該功能。 此 Cmdlet 除了樹系根網域中的內部部署 Active Directory 網域系統管理員權限之外，也需要您 Azure 租用戶的全域管理員認證。 所指定 Proxy 服務的註冊成功之後，額外的 `Register-AzureADPasswordProtectionProxy` 引動過程也會跟著執行成功，但並非必要。

      Register-AzureADPasswordProtectionProxy Cmdlet 支援以下三種不同驗證模式。

      * 互動式驗證模式：

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > 此模式無法在 Server Core 作業系統上運作。 請改用以下所述的其中一種替代驗證模式。

         > [!NOTE]
         > 如果啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 因應措施是停用 IESC、註冊 Proxy，然後重新啟用 IESC。

      * 裝置代碼驗證模式：

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         您接著可以遵循不同裝置上顯示的指示來完成驗證。

      * 無訊息 (密碼型) 驗證模式：

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > 如果驗證基於任何原因需要 MFA，此模式將會失敗。 若發生這種情況，請使用先前兩種模式的其中之一來完成 MFA 型驗證。

      目前不需要指定 -ForestCredential 參數，此參數保留以供未來的功能使用。

   > [!NOTE]
   > Azure AD 密碼保護 Proxy 服務的註冊步驟在服務的生命週期中應該只需執行一次。 之後，此 Proxy 服務就會自動執行所有其他必要的維護。 所指定 Proxy 的註冊成功之後，額外的 'Register-AzureADPasswordProtectionProxy' 引動過程也會跟著執行成功，但並非必要。

   > [!TIP]
   > 第一次為指定的 Azure 租用戶執行此 Cmdlet 時，在 Cmdlet 執行完成之前，可能會有相當長的延遲 (很多秒)。 除非系統回報失敗，否則不應該將此延遲視為警示。

5. 註冊樹系。
   * 內部部署 Active Directory 樹系必須使用 `Register-AzureADPasswordProtectionForest` PowerShell Cmdlet 以必要的認證初始化，才能與 Azure 進行通訊。 此 Cmdlet 除了樹系根網域中的內部部署 Active Directory 網域系統管理員權限之外，也需要您 Azure 租用戶的全域管理員認證。 此步驟會針對每一樹系執行一次。

      Register-AzureADPasswordProtectionForest Cmdlet 支援以下三種不同驗證模式。

      * 互動式驗證模式：

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > 此模式無法在 Server Core 作業系統上運作。 請改用以下所述的其中一種替代驗證模式。

         > [!NOTE]
         > 如果啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 因應措施是停用 IESC、註冊 Proxy，然後重新啟用 IESC。  

      * 裝置代碼驗證模式：

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         您接著可以遵循不同裝置上顯示的指示來完成驗證。

      * 無訊息 (密碼型) 驗證模式：
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > 如果驗證需要 MFA，此模式將會失敗。 若發生這種情況，請使用先前兩種模式的其中之一來完成 MFA 型驗證。

      只有當目前登入的使用者也是根網域的 Active Directory 網域系統管理員時，上述範例才能運作。 如果不是這種情況，您可以透過 -ForestCredential 參數提供替代網域認證。

   > [!NOTE]
   > 如果您的環境中安裝了多個 Proxy 伺服器，則使用哪一個 Proxy 伺服器來註冊樹系並無關緊要。

   > [!TIP]
   > 第一次為指定的 Azure 租用戶執行此 Cmdlet 時，在 Cmdlet 執行完成之前，可能會有相當長的延遲 (很多秒)。 除非系統回報失敗，否則不應該將此延遲視為警示。

   > [!NOTE]
   > Active Directory 樹系的註冊步驟在樹系的生命週期中應該只需執行一次。 之後，在樹系中執行的網域控制站代理程式就會自動執行所有其他必要的維護。 所指定樹系的註冊成功之後，額外的 `Register-AzureADPasswordProtectionForest` 引動過程也會跟著執行成功，但並非必要。

   > [!NOTE]
   > 為了讓 `Register-AzureADPasswordProtectionForest` 可成功執行，Proxy 伺服器的網域中至少要有一個 Windows Server 2012 或更新版本的網域控制站可用。 不過，在此步驟之前，不需要在任何網域控制站上安裝 DC 代理程式軟體。

6. 將 Azure AD 密碼保護 Proxy 服務設定為透過 HTTP Proxy 通訊

   如果您的環境需要使用特定的 HTTP Proxy 來與 Azure 通訊，可以透過下列方式達成。

   在 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 資料夾中建立一個含有下列內容且名為 `proxyservice.exe.config` 的檔案：

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   如果您的 HTTP Proxy 需要驗證，請新增 useDefaultCredentials 標籤，如下所示：

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   在這兩種情況中，您都要使用您特定之 HTTP Proxy 伺服器的位址和連接埠取代 `http://yourhttpproxy.com:8080`。

   如果您的 HTTP Proxy 已設定使用授權原則，則必須授與存取權限給裝載 Azure AD 密碼保護 Proxy 服務之電腦的 Active Directory 電腦帳戶。

   在建立或更新 `proxyservice.exe.config` 檔案之後，您應該停止 Azure AD 密碼保護 Proxy 服務並重新啟動。

   Azure AD 密碼保護 Proxy 服務不支援使用特定認證連線至 HTTP Proxy。

7. 選用：將 Azure AD 密碼保護 Proxy 服務設定成在特定連接埠上進行接聽。
   * 網域控制站上的 Azure AD 密碼保護 DC 代理程式軟體會使用「透過 TCP 的 RPC」與 Azure AD 密碼保護 Proxy 服務進行通訊。 Azure AD 密碼保護 Proxy 服務預設會在任何可用的動態 RPC 端點上進行接聽。 因網路拓撲或防火牆需求而有必要時，可以改為將服務設定為在特定的 TCP 連接埠上進行接聽。
      * 若要將服務設定成在靜態連接埠下執行，請使用 `Set-AzureADPasswordProtectionProxyConfiguration` Cmdlet。
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 您必須將服務停止後再重新啟動，這些變更才會生效。

      * 若要將服務設定成在動態連接埠下執行，請使用相同的程序，但必須將 StaticPort 設回零，像這樣：
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 您必須將服務停止後再重新啟動，這些變更才會生效。

   > [!NOTE]
   > 對連接埠設定進行任何變更之後，都必須手動重新啟動 Azure AD 密碼保護 Proxy 服務。 進行此性質的設定變更之後，並不需要重新啟動在網域控制站上執行的 DC 代理程式服務軟體。

   * 您可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` Cmdlet 來查詢目前的服務設定，如以下範例所示：

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>安裝 Azure AD 密碼保護 DC 代理程式服務

   使用 `AzureADPasswordProtectionDCAgent.msi` MSI 套件來安裝 Azure AD 密碼保護 DC 代理程式服務軟體

   由於作業系統要求只能在重新開機時載入或卸載密碼篩選 dll，因此軟體安裝在安裝和解除安裝時確實需要重新開機。

   支援在尚未成為網域控制站的電腦上安裝 DC 代理程式服務。 在此情況下，服務將會啟動並執行，但會處於非作用中，直到電腦被升階成網域控制站為止。

   您可以使用標準 MSI 程序來自動執行軟體安裝，例如：

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > 上述範例 msiexec 命令會導致立即重新開機；只要指定 `/norestart` 旗標，即可避免發生此情況。

安裝在網域控制站上並重新開機之後，Azure AD 密碼保護 DC 代理程式軟體安裝便已完成。 任何其他設定都無須進行，也無法進行。

## <a name="multiple-forest-deployments"></a>多個樹系部署

沒有任何要在多個樹系部署 Azure AD 密碼保護的額外需求。 如單一樹系部署小節所述，每個樹系都是以獨立方式設定的。 每個 Azure AD 密碼保護 Proxy 都只能支援其已加入樹系中的網域控制站。 不論 Active Directory 信任設定為何，所指定樹系中的 Azure AD 密碼保護軟體都不會知道部署在另一個樹系中的 Azure AD 密碼保護軟體。

## <a name="read-only-domain-controllers"></a>唯讀網域控制站

系統一律不會在唯讀網域控制站 (RODC) 上處理和保存密碼變更\設定；而是會轉送給可寫入的網域控制站進行處理和保存。 因此，不需要在 RODC 上安裝 DC 代理程式軟體。

## <a name="high-availability"></a>高可用性

當樹系中的網域控制站嘗試從 Azure 下載新原則或其他資料時，若要確保 Azure AD 密碼保護的高可用性，主要考量將會是 Proxy 伺服器的可用性。 每個 DC 代理程式在決定要呼叫的 Proxy 伺服器時，都是使用簡單的循環配置資源型演算法，並且會略過沒有回應的 Proxy 伺服器。 對大部分具有良好複寫 (目錄和 sysvol 兩者狀態) 的完全連線 Active Directory 部署而言，兩個 (2) Proxy 伺服器就應該足以確保可用性，因而能及時下載新原則和其他資料。 您可視需要部署額外的 Proxy 伺服器。

與高可用性相關的尋常問題可藉由 DC 代理程式軟體的設計來解決。 DC 代理程式會保有最近所下載密碼原則的本機快取。 即使因任何原因而導致所有註冊的 Proxy 伺服器都變成無法使用，DC 代理程式仍會繼續強制執行其已快取的密碼原則。 大型部署中合理的密碼原則更新頻率通常是幾天，而非幾小時或更少。 因此，短暫的 Proxy 伺服器服務中斷並不會對 Azure AD 密碼保護功能或其安全性優點造成重大影響。

## <a name="next-steps"></a>後續步驟

既然您已在內部部署伺服器上安裝 Azure AD 密碼保護所需的服務，現在請完成[安裝後設定並收集報告資訊](howto-password-ban-bad-on-premises-operations.md)，以完成您的部署。

[Azure AD 密碼保護的概念性概觀](concept-password-ban-bad-on-premises.md)
