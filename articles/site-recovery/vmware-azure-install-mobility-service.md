---
title: 安裝行動服務以進行從 VMware VM 和實體伺服器至 Azure 的災害復原 | Microsoft Docs
description: 了解如何安裝行動服務代理程式，以使用 Azure Site Recovery 服務進行從 VMware VM 和實體伺服器至 Azure 的災害復原。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 14be544c53bf3393466cfa33b2ad815f07d0005d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007411"
---
# <a name="install-the-mobility-service-for-disaster-recovery-of-vmware-vms-and-physical-servers"></a>安裝行動服務以進行 VMware VM 和實體伺服器的災害復原

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 [Site Recovery 行動服務](vmware-physical-mobility-service-overview.md)。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。

## <a name="install-on-windows-machine"></a>在 Windows 機器上安裝

在您想要保護的每部 Windows 機器上，執行下列操作：

1. 確定機器與處理伺服器之間有網路連線。 如果您尚未設定個別的處理伺服器，則預設會在設定伺服器上執行。
1. 建立可供處理序伺服器存取電腦的帳戶。 帳戶應該具有本機或網域系統管理員權限。 此帳戶僅適用於推送安裝和代理程式更新。
2. 如果您未使用網域帳戶，請依照下列方式，停用本機電腦上的「遠端使用者存取控制」：
    - 在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼底下，新增 DWORD：**LocalAccountTokenFilterPolicy**。 將值設定為 **1**。
    -  做法是在命令提示字元執行下列命令︰  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. 在您想要保護之機器的 [Windows 防火牆] 中，選取 [允許應用程式或功能通過防火牆]。 啟用 [檔案及印表機共用] 和 [Windows Management Instrumentation (WMI)]。 針對隸屬於網域的電腦，您可以利用群組原則物件 (GPO) 進行防火牆設定。

   ![防火牆設定](./media/vmware-azure-install-mobility-service/mobility1.png)

4. 新增您在 CSPSConfigtool 中建立的帳戶。 若要這樣做，請登入您的設定伺服器。
5. 開啟 **cspsconfigtool.exe**。 它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾中。
6. 在 [管理帳戶] 索引標籤上，選取 [新增帳戶]。
7. 加入您所建立的帳戶。
8. 輸入您為電腦啟用複寫時使用的認證。

## <a name="install-on-linux-machine"></a>在 Linux 機器上安裝

在您想要保護的每部 Linux 機器上，執行下列操作：

1. 確定 Linux 機器與處理伺服器之間有網路連線。
2. 建立可供處理序伺服器存取電腦的帳戶。 帳戶應該是來源 Linux 伺服器上的**根**使用者。 此帳戶僅適用於推送安裝和更新。
3. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案，該檔案具有將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4. 在您要複寫的電腦上安裝最新的 openssh、openssh-server 和 openssl 套件。
5. 請確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
4. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證。 若要這麼做，請以 **root** 使用者身分登入。
5. 在 **/etc/ssh/sshd_config** 檔案中，找到以 **PasswordAuthentication** 開頭這一行。
6. 取消該行的註解，並將值變更為 **yes**。
7. 找到以 **Subsystem** 開頭這一行，並取消其註解。

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. 重新啟動 **sshd** 服務。
9. 新增您在 CSPSConfigtool 中建立的帳戶。 若要這樣做，請登入您的設定伺服器。
10. 開啟 **cspsconfigtool.exe**。 它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾中。
11. 在 [管理帳戶] 索引標籤上，選取 [新增帳戶]。
12. 加入您所建立的帳戶。
13. 輸入您為電腦啟用複寫時使用的認證。

## <a name="next-steps"></a>後續步驟

安裝「行動服務」之後，在 Azure 入口網站中，選取 [+ 複寫] 以開始保護這些 VM。 深入了解如何為 [VMware VM](vmware-azure-enable-replication.md) 和[實體伺服器](physical-azure-disaster-recovery.md#enable-replication)啟用複寫。


