---
title: 部署 Azure AD 密碼保護-Azure Active Directory
description: 部署 Azure AD 密碼保護，禁止使用不正確的密碼在內部部署
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
ms.openlocfilehash: 4c22c9c202e6de3b31b99803dce4a07d38287a92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057295"
---
# <a name="deploy-azure-ad-password-protection"></a>部署 Azure AD 密碼保護

既然您了解[如何強制執行 Windows Server Active Directory 的 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)下, 一個步驟是計劃和執行您的部署。

## <a name="deployment-strategy"></a>部署策略

我們建議您在稽核模式中啟動部署。 稽核模式是預設的初始設定，密碼可以繼續設定。 將會被封鎖的密碼會記錄在事件記錄檔。 在稽核模式部署的 proxy 伺服器和 DC 代理程式之後，您應該監視時強制執行原則，將會有的使用者和環境的密碼原則的影響。

在稽核階段中，許多組織會找出的：

* 它們必須改善現有的作業程序，才能使用更安全的密碼。
* 使用者通常會使用不安全的密碼。
* 他們需要通知使用者有關即將推出的變更中強制執行安全性，可能會影響，以及如何選擇更安全的密碼。

功能已在稽核模式中執行的合理時間之後，您可以切換的組態*稽核*要*強制*要求更安全的密碼。 在此期間進行專注的監視是個不錯的主意。

## <a name="deployment-requirements"></a>部署需求

* 授權需求，Azure AD 密碼保護，請參閱文章[排除在您的組織中的密碼錯誤](concept-password-ban-bad.md#license-requirements)。
* 所有網域控制站服務安裝的 Azure AD 密碼保護，必須執行 Windows Server 2012 或更新版本，取得 DC 代理程式。 這項需求並不表示，Active Directory 網域或樹系必須也是在 Windows Server 2012 網域或樹系功能等級。 中所述[設計原則](concept-password-ban-bad-on-premises.md#design-principles)，沒有任何最低網域功能等級為或 FFL 所需的 DC 代理程式或 proxy 軟體執行。
* 取得安裝的 DC 代理程式服務的所有機器必須都已安裝.NET 4.5。
* 取得 proxy 服務已安裝的 Azure AD 密碼保護，必須執行 Windows Server 2012 R2 或更新版本的所有機器。
   > [!NOTE]
   > Proxy 服務部署會部署 Azure AD 密碼保護，即使網域控制站可能會有輸出直接的網際網路連線能力是強制性需求。 
   >
* 安裝 Azure AD 密碼保護 Proxy 服務的所有機器都必須都有安裝的.NET 4.7。
  完整地進行更新的 Windows Server 上時，應該已安裝.NET 4.7。 如果這不是這樣，下載並執行安裝程式，請參閱[for Windows 的.NET Framework 4.7 離線安裝程式](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows)。
* 所有的機器，包括網域控制站，取得安裝的 Azure AD 密碼保護元件必須安裝的通用 C 執行階段。 您可以確定您已從 Windows Update 的所有更新，以取得執行階段。 或者，您可以將它取得特定 OS 的更新程式封裝中。 如需詳細資訊，請參閱 <<c0> [ 在 Windows 中的通用 C 執行階段更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 網路連線之間必須存在每個網域中的至少一個網域控制站和至少一部伺服器裝載的密碼保護的 proxy 服務。 此連線必須允許存取 RPC 端點對應程式連接埠 135 和 RPC 伺服器連接埠上的 proxy 服務的網域控制站。 根據預設，RPC 伺服器連接埠是動態的 RPC 連接埠，但將它設定為[使用靜態連接埠](#static)。
* 裝載 proxy 服務的所有機器都必須都具有下列端點的網路存取：

    |**端點**|**用途**|
    | --- | --- |
    |`https://login.microsoftonline.com`|驗證要求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密碼保護功能|

* 所有裝載 proxy 服務的密碼保護的機器必須設定為允許輸出的 TLS 1.2 的 HTTP 流量。
* 註冊 proxy 服務的密碼保護和樹系與 Azure AD 全域管理員帳戶。
* 與 Azure AD 註冊的 Windows Server Active Directory 樹系的樹系根網域中具有 Active Directory 網域系統管理員權限的帳戶。
* 執行 DC 代理程式服務的軟體的任何 Active Directory 網域必須使用分散式檔案系統複寫 (DFSR) 進行 sysvol 複寫。
* 金鑰發佈服務必須能夠在執行 Windows Server 2012 網域中，所有網域控制站上。 根據預設，此服務會啟用透過手動觸發程序開始。

## <a name="single-forest-deployment"></a>單一樹系部署

下圖顯示 Azure AD 密碼保護的基本元件如何一起在內部部署 Active Directory 環境中。

![Azure AD 密碼保護元件搭配運作的方式](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

它是個不錯的主意，若要檢閱您在部署之前，軟體的運作方式。 請參閱[概念的概觀，Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

### <a name="download-the-software"></a>下載軟體

有兩個必要的安裝程式的 Azure AD 密碼保護。 它們可從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)。

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>安裝和設定密碼保護的 proxy 服務

1. 選擇裝載的密碼保護的 proxy 服務的一或多個伺服器。
   * 每個這類服務的單一樹系只能提供密碼原則。 主機電腦必須加入該樹系的網域。 支援根和子網域。 您必須至少一個樹系的每個網域中的 DC 和密碼保護電腦之間網路連線。
   * 您可以測試的網域控制站上執行的 proxy 服務。 但該網域控制站然後需要網際網路連線，它可以是有安全性考量。 我們建議僅供測試此組態。
   * 我們建議至少兩個 proxy 伺服器，以提供備援。 請參閱[高可用性](howto-password-ban-bad-on-premises-deploy.md#high-availability)。

1. 安裝 Azure AD 密碼保護 Proxy 服務使用`AzureADPasswordProtectionProxySetup.exe`軟體安裝程式。
   * 軟體安裝並不需要重新開機。 您可以使用標準 MSI 程序來自動執行軟體安裝，例如：

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > 在安裝 AzureADPasswordProtectionProxySetup.msi 套件，以避免安裝錯誤之前，必須執行 Windows 防火牆服務。 如果 Windows 防火牆設定為不會執行，因應措施是暫時啟用，並在安裝期間執行防火牆服務。 Proxy 軟體對 Windows 防火牆在安裝後沒有特定的相依性。 如果您使用協力廠商防火牆，它必須仍設定為滿足部署需求。 這些包括允許輸入連接埠 135 和 RPC 伺服器連接埠的 proxy 的存取。 請參閱[部署需求](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)。

1. 以系統管理員身分開啟 PowerShell 視窗。
   * 密碼保護 proxy 軟體包含新的 PowerShell 模組*AzureADPasswordProtection*。 下列步驟執行此 PowerShell 模組中的各種指令程式。 如下所示匯入新的模組：

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * 若要檢查服務正在執行，請使用下列 PowerShell 命令：

      `Get-Service AzureADPasswordProtectionProxy | fl` 。

     結果應該會顯示**狀態**為"Running"。

1. 註冊 Proxy。
   * 步驟 3 完成後，在電腦上執行的 proxy 服務。 但是，服務還沒有與 Azure AD 通訊所需的認證。 向 Azure AD 註冊，則需要：

     `Register-AzureADPasswordProtectionProxy`

     此 cmdlet 需要 Azure 租用戶的全域管理員認證。 您也需要在內部部署 Active Directory 網域系統管理員權限的樹系根網域中。 此命令一次成功的 proxy 服務之後，它的其他引動過程會成功，但不需要。

      `Register-AzureADPasswordProtectionProxy` Cmdlet 支援下列三種驗證模式。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式中不在 Server Core 作業系統上運作的。 相反地，使用下列驗證模式的其中一個。 此外，此模式也可能會失敗，如果已啟用 Internet Explorer 增強式安全性設定。 因應措施是停用該設定、 註冊 proxy，然後再重新啟用。

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        您接著會依據不同的裝置上顯示的指示完成驗證。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 需要 Azure Multi-factor Authentication 時，這個模式就會失敗。 在此情況下，使用其中一個先前的兩種驗證模式。

       您目前沒有在指定 *-ForestCredential*參數，其保留供未來的功能。

   註冊密碼保護的 proxy 服務作業所需一次存留期間的服務。 在那之後，proxy 服務會自動執行任何其他必要的維護作業。

   > [!TIP]
   > 可能有明顯的延遲，完成第一次針對特定的 Azure 租用戶執行這個指令程式之前。 除非在報告失敗時，不必擔心這種延遲。

1. 註冊樹系。
   * 您必須初始化使用與 Azure 進行通訊所需的認證與內部部署 Active Directory 樹系`Register-AzureADPasswordProtectionForest`PowerShell cmdlet。 Cmdlet 需要您的 Azure 租用戶全域管理員認證。 您也必須在內部部署 Active Directory 網域系統管理員權限的樹系根網域中。 此步驟會針對每一樹系執行一次。

      `Register-AzureADPasswordProtectionForest` Cmdlet 支援下列三種驗證模式。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式中不會在 Server Core 作業系統上運作的。 改為使用其中一個下列兩種驗證模式。 此外，此模式也可能會失敗，如果已啟用 Internet Explorer 增強式安全性設定。 因應措施是停用該設定、 註冊 proxy，然後再重新啟用。  

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        您接著會依據不同的裝置上顯示的指示完成驗證。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 需要 Azure Multi-factor Authentication 時，這個模式就會失敗。 在此情況下，使用其中一個先前的兩種驗證模式。

       如果目前登入的使用者也是根網域的 Active Directory 網域系統管理員，這些範例只會成功。 如果並非如此，您可以提供替代的網域認證，透過 *-ForestCredential*參數。

   > [!NOTE]
   > 如果您的環境中安裝多部 proxy 伺服器，並不重要的 proxy 伺服器，您用來註冊樹系。
   >
   > [!TIP]
   > 可能有明顯的延遲，完成第一次針對特定的 Azure 租用戶執行這個指令程式之前。 除非在報告失敗時，不必擔心這種延遲。

   註冊 Active Directory 樹系作業所需一次在樹系的存留期。 在那之後，樹系中網域控制站的代理程式會自動執行任何其他必要的維護作業。 之後`Register-AzureADPasswordProtectionForest`樹系執行成功，指令程式的其他引動過程成功，但不需要。

   針對`Register-AzureADPasswordProtectionForest`成功，至少一個網域控制站執行 Windows Server 2012 或更新版本中必須存在 proxy 伺服器的網域。 但 DC 代理程式軟體並沒有安裝在此步驟之前的任何網域控制站上。

1. 設定密碼保護，以透過 HTTP proxy 進行通訊的 proxy 服務。

   如果您的環境需要使用特定的 HTTP proxy 來與 Azure 通訊，請使用此方法：建立*AzureADPasswordProtectionProxy.exe.config* %ProgramFiles%\Azure AD 密碼保護 Proxy\Service 資料夾中的檔案。 包含下列內容：

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

   如果您的 HTTP proxy 需要驗證，請新增*useDefaultCredentials*標記：

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

   在這兩種情況下，取代`http://yourhttpproxy.com:8080`地址與您特定的 HTTP proxy 伺服器的連接埠。

   如果您的 HTTP proxy 設定給我們授權原則，您必須授與存取權的密碼保護的 proxy 服務的主機電腦的 Active Directory 電腦帳戶。

   我們建議您停止並重新啟動的 proxy 服務，在您建立或更新後*AzureADPasswordProtectionProxy.exe.config*檔案。

   Proxy 服務不支援使用特定的認證來連接到 HTTP proxy。

1. 選用：Proxy 服務設定為接聽特定通訊埠上的密碼保護。
   * 網域控制站上的密碼保護的 DC 代理程式軟體會使用 RPC over TCP 通訊的 proxy 服務。 根據預設，proxy 服務會接聽任何可用的動態 RPC 端點。 但如果這是因為網路拓樸或您的環境中的防火牆需求所需，您可以設定為接聽特定 TCP 通訊埠，服務。
      * <a id="static" /></a>若要設定靜態連接埠之下執行服務，請使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet。

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 您必須將服務停止後再重新啟動，這些變更才會生效。

      * 若要設定動態連接埠之下執行服務，使用相同的程序，但設定*StaticPort*設回零：

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 您必須將服務停止後再重新啟動，這些變更才會生效。

   > [!NOTE]
   > 密碼保護的 proxy 服務連接埠組態中的任何變更之後，需要手動重新啟動。 但您不需要進行這些設定變更之後，重新啟動網域控制站上的 DC 代理程式服務軟體。

   * 若要查詢的目前組態的服務，請使用`Get-AzureADPasswordProtectionProxyConfiguration`cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>安裝 DC 代理程式服務

   安裝 DC 代理程式服務密碼保護使用`AzureADPasswordProtectionDCAgentSetup.msi`封裝。

   軟體安裝或解除安裝需要重新啟動。 這是因為密碼篩選 Dll 只載入或卸載重新啟動。

   您可以在尚無法在網域控制站的電腦上安裝 DC 代理程式服務。 在此情況下，服務會啟動並執行，但保持非使用中，直到電腦升級為網域控制站。

   您可以使用標準的 MSI 程序，以自動化軟體安裝。 例如:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > 以下範例 msiexec 命令會導致立即重新啟動。 若要避免這種情況，使用`/norestart`旗標。

DC 安裝代理程式軟體是在網域控制站，並重新啟動該電腦之後，已完成安裝。 任何其他設定都無須進行，也無法進行。

## <a name="multiple-forest-deployments"></a>多個樹系部署

沒有任何要在多個樹系部署 Azure AD 密碼保護的額外需求。 「 單一樹系部署 」 一節中所述，獨立設定每個樹系。 每個密碼保護 proxy 只能支援來自已加入的樹系的網域控制站。 密碼保護軟體，在任何樹系不會知道其他樹系，而不論 Active Directory 信任組態中已部署的密碼保護軟體。

## <a name="read-only-domain-controllers"></a>唯讀網域控制站

密碼變更/設定不會處理並保存在唯讀網域控制站 (Rodc) 上。 它們會轉送至可寫入網域控制站。 因此，您不需要在 Rodc 上安裝 DC 代理程式軟體。

## <a name="high-availability"></a>高可用性

當樹系中的網域控制站嘗試從 Azure 下載新原則或其他資料時，密碼保護的主要可用性考量會是 proxy 伺服器的可用性。 當您決定要呼叫哪一個 proxy 伺服器時，每個 DC 代理程式會使用簡單的循環配置資源樣式演算法。 代理程式會略過 proxy 伺服器不回應。 最完整已連線的 Active Directory 部署具有 directory 和 sysvol 的資料夾狀態的狀況良好的複寫，兩個 proxy 伺服器就足以確保可用性。 這會導致及時下載新的原則和其他資料。 但是，您可以部署額外的 proxy 伺服器。

DC 代理程式軟體的設計可降低高可用性相關聯的一般問題。 「 DC 代理程式 」 會維護最近下載的密碼原則的本機快取。 即使所有已註冊的 proxy 伺服器變成無法使用，DC 代理程式會繼續強制執行其快取的密碼原則。 通常是合理的更新頻率，在大型部署中的密碼原則*天*，不必等到幾小時或更少。 因此，proxy 伺服器的短暫中斷不會明顯地影響 Azure AD 密碼保護。

## <a name="next-steps"></a>後續步驟

既然您已安裝的服務，您需要 Azure AD 密碼保護您的內部部署伺服器上[執行後續安裝組態和報告資訊的收集](howto-password-ban-bad-on-premises-operations.md)以完成部署。

[Azure AD 密碼保護的概念性概觀](concept-password-ban-bad-on-premises.md)
