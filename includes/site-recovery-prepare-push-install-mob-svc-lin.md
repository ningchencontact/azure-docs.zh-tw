---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122839"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>在 Linux 服务器上准备推送安装

1. 确保 Linux 计算机与进程服务器之间已建立网络连接。
1. 创建可供进程服务器用来访问计算机的帐户。 帳戶應該是來源 Linux 伺服器上的**根**使用者。 此帳戶僅適用於推送安裝和更新。
1. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案，該檔案具有將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
1. 在您要複寫的電腦上安裝最新的 openssh、openssh-server 和 openssl 套件。
1. 請確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
1. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證。 請遵循下列步驟：

    a. 以 **root** 的身分登入。

    b. 在 **/etc/ssh/sshd_config** 檔案中，找到以 **PasswordAuthentication** 開頭這一行。

    c. 取消該行的註解，並將值變更為 **yes**。

    d. 找到以 **Subsystem** 開頭這一行，並取消其註解。

      ![ Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. 重新啟動 **sshd** 服務。

1. 新增您在 CSPSConfigtool 中建立的帳戶。 請遵循下列步驟：

    a. 登入您的組態伺服器。

    b. 開啟 **cspsconfigtool.exe**。 它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾中。

    c. 在 [管理帳戶] 索引標籤上，選取 [新增帳戶]。

    d. 加入您所建立的帳戶。

    d. 輸入您為電腦啟用複寫時使用的認證。
