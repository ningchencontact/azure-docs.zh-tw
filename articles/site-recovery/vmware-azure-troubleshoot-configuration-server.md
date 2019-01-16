---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原期間的組態伺服器問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 進行 VMware VM 和實體伺服器至 Azure 的災害復原，提供部署組態伺服器的疑難排解資訊。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050790"
---
# <a name="troubleshoot-configuration-server-issues"></a>針對組態伺服器問題進行疑難排解

本文可協助您疑難排解在部署和管理 [Azure Site Recovery](site-recovery-overview.md) 組態伺服器時遭遇的問題。 組態伺服器可作為管理伺服器。 透過使用 Site Recovery，以使用組態伺服器來設定內部部署 VMware VM 和實體伺服器至 Azure 的災害復原。 下列各節將討論當您新增新組態伺服器時與管理組態伺服器時，可能會碰到的常見失敗情況。

## <a name="registration-failures"></a>註冊失敗

當您安裝行動代理程式時，來源電腦會向設定伺服器註冊。 您可以遵循下列的指導方針，對此步驟期間所發生的任何失敗偵錯：

1. 開啟 C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 檔案。 (ProgramData 資料夾可能是隱藏的資料夾。 如果您沒有看見 ProgramData 資料夾，請在檔案總管中的 [檢視] 索引標籤上，[顯示/隱藏] 區段中，選取 [隱藏項目] 核取方塊。)失敗可能是由多個問題所造成。

2. 搜尋字串 **No Valid IP Address found**。 如果找到該字串：
    1. 請驗證要求的主機識別碼與來源電腦的主機識別碼相同。
    2. 確認來源電腦具有至少一個指派給實體 NIC 的 IP。 為了使來源電腦成功向設定伺服器進行代理程式註冊，來源電腦必須具有至少一個指派給實體 NIC 的有效 IP v4 位址。
    3. 在來源電腦上執行下列其中一個命令，以取得來源電腦的所有 IP 位址：
      - 對於 Windows：`> ipconfig /all`
      - 對於 Linux：`# ifconfig -a`

3. 如果找不到字串 **No Valid IP Address found**，則搜尋字串 **Reason=>NULL**。 如果來源電腦使用空白主機向設定伺服器註冊時，就會發生此錯誤。 如果找到該字串：
    - 在您解決問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中所列的指導方針來手動重試註冊。

4. 如果找不到字串 **Reason=>NULL**，則在來源電腦上開啟 C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log 檔案。 (ProgramData 資料夾可能是隱藏的資料夾。 如果您沒有看見 ProgramData 資料夾，請在檔案總管中的 [檢視] 索引標籤上，[顯示/隱藏] 區段中，選取 [隱藏項目] 核取方塊。)失敗可能是由多個問題所造成。 

5. 搜尋字串 **post request:(7) - Couldn't connect to server**。 如果找到該字串：
    1. 解決來源電腦與設定伺服器之間的網路問題。 確認可透過使用如 ping、traceroute 或網頁瀏覽器等網路工具，從來源電腦連線到設定伺服器。 確認來源電腦可透過連接埠 443 連線到設定伺服器。
    2. 檢查來源電腦上是否有任何防火牆規則會封鎖來源電腦與設定伺服器之間的連線。 請與您的網路管理員合作來解除封鎖任何連線問題。
    3. 請確認[自防毒程式排除的 Site Recovery 資料夾](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中所列的資料夾已自防毒軟體排除。
    4. 解決網路問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指導方針重試註冊。

6. 如果找不到字串 **post request:(7) - Couldn't connect to server**，則請在相同的記錄檔中尋找字串 **request:(60) - Peer certificate cannot be authenticated with given CA certificates**。 因為設定伺服器憑證已過期，或來源電腦不支援 TLS 1.0 或更新版本的 SSL 通訊協定，因此可能會發生此錯誤。 如果防火牆封鎖來源電腦與設定伺服器之間的 SSL 通訊，也可能會發生此錯誤。 如果找到該字串： 
    1. 若要解決，請在來源電腦上使用網頁瀏覽器連線至設定伺服器的 IP 位址。 使用 URI https:\/\/<設定伺服器 IP 位址\>:443/。 確認來源電腦可透過連接埠 443 連線到設定伺服器。
    2. 檢查來源電腦上是否必須新增或移除任何防火牆規則，來源電腦才能與設定伺服器通訊。 由於可能有各種防火牆軟體正在使用中，因此我們無法列出所有必要的防火牆設定。 請與您的網路管理員合作來解除封鎖任何連線問題。
    3. 請確認[自防毒程式排除的 Site Recovery 資料夾](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)中所列的資料夾已自防毒軟體排除。  
    4. 在您解決問題後，請依照[向設定伺服器註冊來源電腦](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)中的指導方針重試註冊。

7. 在 Linux 上，如果 <INSTALLATION_DIR\>/etc/drscout.conf 中的平台值損毀，則註冊會失敗。 若要識別此問題，請開啟 /var/log/ua_install.log 檔案。 搜尋字串 **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure**。 平台應設為 **VmWare** 或 **Azure**。 如果 drscout.conf 檔案已損毀，建議您[解除安裝行動代理程式](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)，然後重新安裝行動代理程式。 如果解除安裝失敗，請完成下列步驟：
    1. 開啟 Installation_Directory/uninstall.sh 檔案，並為 **StopServices** 函式的呼叫加上註解。
    2. 開啟 Installation_Directory/Vx/bin/uninstall.sh 檔案，並為 **stop_services** 函式的呼叫加上註解。
    3. 開啟 Installation_Directory/Fx/uninstall.sh 檔案，並將嘗試停止 Fx 服務的整個區段加上註解。
    4. [解除安裝](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)行動代理程式。 成功解除安裝之後，請將系統重新開機，然後嘗試重新安裝代理程式。

## <a name="installation-failure-failed-to-load-accounts"></a>安裝失敗：無法載入帳戶

如果服務在安裝行動代理程式並向設定伺服器註冊時，無法從傳輸連線讀取資料，就會發生此錯誤。 若要解決此問題，請確定已在來源電腦上啟用 TLS 1.0。

## <a name="change-the-ip-address-of-the-configuration-server"></a>變更設定伺服器的 IP 位址

強烈建議不要變更設定伺服器的 IP 位址。 確定指派給設定伺服器的所有 IP 位址皆為靜態位址。 請勿使用 DHCP IP 位址。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008：SAML 權杖無效

若要避免這個錯誤，請確定系統時鐘上的時間與本地時間的差異未超過 15 分鐘。 重新執行安裝程式以完成註冊。

## <a name="failed-to-create-a-certificate"></a>無法建立憑證

無法建立驗證 Site Recovery 所需的憑證。 請先確定您是以本機系統管理員身分執行安裝程式，再重新執行安裝程式。

## <a name="register-the-source-machine-with-the-configuration-server"></a>向設定伺服器註冊來源電腦

### <a name="if-the-source-machine-runs-windows"></a>如果來源電腦執行 Windows

在來源電腦上執行下列命令：

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

設定 | 詳細資料
--- | ---
使用量 | UnifiedAgentConfigurator.exe  /CSEndPoint <設定伺服器 IP位址\> /PassphraseFilePath <複雜密碼檔案路徑\>
代理程式設定記錄 | 位在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 底下。
/CSEndPoint | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
/PassphraseFilePath |  必要。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="if-the-source-machine-runs-linux"></a>如果來源電腦執行 Linux

在來源電腦上執行下列命令：

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

設定 | 詳細資料
--- | ---
使用量 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <設定伺服器 IP 位址\> -P <複雜密碼檔案路徑\>
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  必要。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

