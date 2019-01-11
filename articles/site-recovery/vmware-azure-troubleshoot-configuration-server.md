---
title: 針對使用 Azure Site Recovery 從 VMware VM 和實體伺服器至 Azure 的災害復原期間的組態伺服器問題進行疑難排解 | Microsoft Docs
description: 本文針對使用 Azure Site Recovery 進行 VMware VM 和實體伺服器的災害復原，提供部署組態伺服器的疑難排解資訊。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998960"
---
# <a name="troubleshoot-configuration-server-issues"></a>針對組態伺服器問題進行疑難排解

本文可協助您在部署及管理 [Azure Site Recovery](site-recovery-overview.md) 組態伺服器時排解疑難問題。 設定伺服器可作為管理伺服器，並可用於以 Site Recovery 來設定內部部署 VMware VM 和實體伺服器至 Azure 的災害復原。 下列各節將討論新增設定伺服器和管理設定伺服器時最常見的失敗。

## <a name="registration-failures"></a>註冊失敗

來源電腦會在行動代理程式安裝期間註冊設定伺服器。 遵循下面所列的指導方針，即可對此步驟期間所發生的任何失敗進行偵錯：

1. 移至 C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 檔案。 ProgramData 可能是隱藏的資料夾。 如果找不到，請嘗試取消隱藏資料夾。 失敗可能是由多個問題所造成。
2. 搜尋字串 “No Valid IP Address found”。 如果找到該字串，
    - 請驗證要求的主機識別碼是否與來源電腦相同。
    - 來源電腦應該至少將一個 IP 位址指派給實體 NIC，代理程式才能成功註冊 CS。
    - 在來源電腦 `> ipconfig /all` (適用於 Windows OS) 和 `# ifconfig -a` (適用於 Linux OS) 上執行命令，以取得來源電腦的所有 IP 位址。
    - 請注意，代理程式註冊需要指派給實體 NIC 的有效 IP v4 位址。
3. 如果找不到上述字串，請搜尋字串 "Reason"=>"NULL"。 如果找到，
    - 當來源電腦使用空白主機來註冊設定伺服器時，就會發生此錯誤。
    - 解決問題之後，請遵循[這裡](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指導方針手動重試註冊。
4. 如果找不到上述字串，請移至來源電腦，並檢查記錄檔 C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log。ProgramData 可以是隱藏的資料夾。 如果找不到，請嘗試取消隱藏資料夾。 失敗可能是由多個問題所造成。 搜尋字串 “post request:(7) - Couldn't connect to server”。 如果找到，
    - 解決來源電腦與設定伺服器之間的網路問題。 使用如 ping、traceroute、網頁瀏覽器等網路工具來確認設定伺服器可連線至來源電腦，並確定該來源電腦可透過連接埠 443 連線至設定伺服器。
    - 檢查來源電腦上是否有任何防火牆規則會封鎖來源電腦與設定伺服器之間的連線。 請與您的網路管理員合作來解除封鎖連線問題。
    - 確定防毒軟體中未包含[這裡](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所提及的資料夾。
    - 解決網路問題之後，請遵循[這裡](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指導方針重試註冊。
5. 如果找不到，請在同一個記錄檔中尋找字串 "request:(60) - Peer certificate cannot be authenticated with given CA certificates."。 如果找到， 
    - 此錯誤可能是因為設定伺服器憑證已過期、來源電腦不支援 TLS 1.0 與上述 SSL 通訊協定，或是有防火牆會封鎖來源電腦與設定伺服器之間的 SSL 通訊。
    - 若要解決，請在來源電腦上使用網頁瀏覽器透過 URI https://<CSIPADDRESS>:443/ 連線至設定伺服器的 IP 位址。 請確定該來源電腦可透過連接埠 443 連線至設定伺服器。
    - 檢查來源電腦上是否有任何防火牆規則會封鎖來源電腦與設定伺服器之間的連線。 請與您的網路管理員合作來解除封鎖連線問題。
    - 確定防毒軟體中未包含[這裡](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所提及的資料夾。  
    - 解決問題之後，請遵循[這裡](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)提供的指導方針重試註冊。
6. 在 Linux 中，如果 <INSTALLATION_DIR>/etc/drscout.conf 中的平台值已損毀，則註冊會失敗。 若要識別，請移至記錄檔 /var/log/ua_install.log。 您會看到字串 "Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure"。 平台應該設定為 "VmWare" 或 "Azure"。 因為 drscout.conf 檔案已損毀，所以建議您[解除安裝](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)該行動代理程式並重新安裝。 如果解除安裝失敗，請遵循下列步驟：
    - 開啟 Installation_Directory/uninstall.sh 檔案，並為 *StopServices* 函式的呼叫加上註解
    - 開啟 Installation_Directory/Vx/bin/uninstall 檔案，並將 `stop_services` 函式的呼叫加上註解
    - 開啟 Installation_Directory/Fx/uninstall 檔案，並將嘗試停止 Fx 服務的完整區段加上註解。
    - 現在請嘗試[解除安裝](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service)行動代理程式。 成功解除安裝之後，請重新啟動系統，然後再次嘗試安裝代理程式。

## <a name="installation-failure---failed-to-load-accounts"></a>安裝失敗 - 無法載入帳戶

如果服務在安裝行動代理程式並向設定伺服器註冊時無法從傳輸連線讀取資料，就會發生此錯誤。 若要解決，請確定已在來源電腦上啟用 TLS 1.0。

## <a name="change-ip-address-of-configuration-server"></a>變更設定伺服器的 IP 位址

強烈建議不要變更設定伺服器的 IP 位址。 請確定指派給設定伺服器的所有 IP 都是靜態 IP 而不是 DHCP IP。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008：SAML 權杖無效

若要避免這個錯誤，請確定系統時鐘上的時間與本地時間的偏差未超過 15 分鐘。 重新執行安裝程式以完成註冊。

## <a name="failed-to-create-certificate"></a>無法建立憑證

無法建立驗證 Site Recovery 所需的憑證。 確定您是以本機系統管理員身分執行安裝程式之後，請重新執行安裝程式。

## <a name="register-source-machine-with-configuration-server"></a>向設定伺服器註冊來源電腦

### <a name="if-source-machine-has-windows-os"></a>如果來源電腦具有 Windows OS

請在來源電腦上執行下列命令

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**設定** | **詳細資料**
--- | ---
使用量 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
代理程式設定記錄 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 底下。
/CSEndPoint | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
/PassphraseFilePath |  必要。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="if-source-machine-has-linux-os"></a>如果來源電腦具有 Linux OS

請在來源電腦上執行下列命令

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**設定** | **詳細資料**
--- | ---
使用量 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  必要。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾