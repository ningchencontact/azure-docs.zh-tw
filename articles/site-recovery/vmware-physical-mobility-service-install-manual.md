---
title: 手動安裝行動服務以使用 Azure Site Recovery 來進行 VMware VM 和實體伺服器的災害復原 | Microsoft Docs
description: 了解如何安裝行動服務代理程式，以使用 Azure Site Recovery 服務進行將 VMware VM 和實體伺服器復原到 Azure 的災害復原。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 862846c8ec544cf082d45cea650269b6518a016f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003539"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>在 VMware VM 和實體伺服器上手動安裝行動服務

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 [Site Recovery 行動服務](vmware-physical-mobility-service-overview.md)。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。

本文說明如何在您想要保護的每部機器上手動安裝行動服務。

## <a name="create-a-passphrase"></a>建立複雜密碼

在安裝之前，請先建立將在安裝期間使用的複雜密碼。

1. 登入設定伺服器。
2. 以系統管理員身分開啟命令提示字元。
3. 將目錄切換至 bin 資料夾，然後建立複雜密碼檔案。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 將複雜密碼檔案儲存於安全的位置。 


## <a name="install-the-service-from-the-ui"></a>從 UI 安裝服務

>[!IMPORTANT]
> 如果您要將 Azure IaaS VM 從一個 Azure 區域複寫到另一個區域，請勿使用此方法。 請改用命令列型安裝方法。

1. 找出您機器作業系統所需的安裝程式版本。 安裝程式位於 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 資料夾中。 [查看](vmware-physical-mobility-service-overview.md#installer-files)您所需的是哪個安裝程式。
2. 將安裝檔案複製到機器並執行。
3. 在 [安裝選項] 中，選取 [安裝行動服務]。
4. 選取安裝位置 > [安裝]。

    ![[行動服務安裝選項] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. 在 [安裝進度] 中監視安裝。 安裝完成後，選取 [繼續進行設定] ，以向設定伺服器註冊服務。

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  在 [設定伺服器詳細資料] 中，指定您所設定的 IP 位址和複雜密碼。  

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. 選取 [註冊] 完成註冊。

    ![[行動服務註冊完成] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>從命令提示字元安裝服務

### <a name="on-a-windows-machine"></a>在 Windows 機器上

1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 C:\Temp)。 

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. 依以下方式進行安裝：

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```

3. 向設定伺服器註冊代理程式。

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="installation-settings"></a>安裝設定

**設定** | **詳細資料**
--- | ---
使用量 | UnifiedAgent.exe /Role <MS\|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
安裝記錄檔 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 底下。
/Role | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
/InstallLocation| 選擇性參數。 指定行動服務安裝位置 (任何資料夾)。
/Platform | 必要。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。 
/Silent| 選用。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

**設定** | **詳細資料**
--- | ---
使用量 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
代理程式設定記錄 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 底下。
/CSEndPoint | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
/PassphraseFilePath |  必要。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。


### <a name="on-a-linux-machine"></a>在 Linux 機器上

1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 /tmp)。 在終端機中執行下列命令：
   ```
   cd /tmp ;

   tar -xvzf Microsoft-ASR_UA*release.tar.gz
   ```
2. 依以下方式進行安裝：

   ```
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```
3. 安裝完成後，行動服務必須向組態伺服器註冊。 執行下列命令來向組態伺服器註冊行動服務：

   ```
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```


#### <a name="installation-settings"></a>安裝設定

**設定** | **詳細資料**
--- | ---
使用量 | ./install -d <Install Location> -r <MS\|MT> -v VmWare -q
-r | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
-d | 選擇性參數。 指定行動服務安裝位置：/usr/local/ASR。
-v | 必要。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。 
-q | 選用。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

**設定** | **詳細資料**
--- | ---
使用量 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  必要。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾

## <a name="next-steps"></a>後續步驟

- [為 VMware VM 設定災害復原](vmware-azure-tutorial.md)
- [為實體伺服器設定災害復原](physical-azure-disaster-recovery.md)
