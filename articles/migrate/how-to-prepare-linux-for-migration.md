---
title: 準備 VMware Vm 至 Azure，Azure 移轉的伺服器移轉，移轉執行 Linux |Microsoft Docs
description: 描述如何準備移轉到 Azure 移轉的伺服器移轉的 Azure Linux VM
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811787"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>準備 Linux VMware Vm 移轉至 Azure 

這篇文章說明如何準備執行 Linux，當您想要將它們移轉到 azure 的 VMware Vm [Azure Migrate](migrate-overview.md)。 

> [!NOTE]
> Azure 移轉的伺服器移轉目前處於公開預覽狀態。 您可以使用現有的 GA 版本的 Azure Migrate 探索及評估 Vm 移轉，但實際的移轉不支援在現有的 GA 版本中。

請準備 Linux 機器，如下所示：

1. 安裝 HYPER-V Linux Integration Services。 較新版本的 Linux 散發套件可能會有預設安裝此項目）。
2. 重建 Linux init 映像，使其包含必要的 HYPER-V 驅動程式，並使 VM 會在 （需要某些散發套件） 的 Azure 中開機。
3. 啟用序列主控台進行疑難排解的記錄。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)。
4. 更新裝置對應檔的磁碟區關聯，若要使用持續性的裝置識別碼的裝置名稱。
5. 更新 fstab 項目，以使用永續性磁碟區的識別項。
6. 移除任何 udev 規則，可保留 MAC 位址等為基礎的介面名稱。
7. 更新網路介面接收 DHCP IP 位址。
8. 請確定 ssh 已啟用。 Sshd 服務設定為在重新開機時自動啟動的檢查。
9. 確保連入 ssh 連線要求不會遭到作業系統防火牆或 IP 資料表規則。

[了解更多](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)相關的最受歡迎的 Linux 散發套件上進行這些變更。

## <a name="sample-script"></a>範例指令碼

此指令碼 (準備-針對-azure.sh) 會提供準備 Linux 機器的範例。 指令碼可能不適用於所有的散發套件和環境，但適合當做起點。

指令碼示範如何： 

- 重新產生必要的驅動程式，視 Linux init 映像。
- 更新 fstab 項目，以使用永續性磁碟區的識別項。
- 主控台記錄檔重新導向至序列埠。
- 啟用 Azure 的序列存取主控台
- 移除網路 udev 的規則
- 將開機指令碼執行時啟動的 VM 上執行。 它會檢查電腦是否正在 Azure 上執行。 如果是，它會更新 VM 上的網路設定，並設定為使用 DHCP 取得 IP 位址的第一個乙太網路介面。

### <a name="before-you-start"></a>開始之前

- 範例指令碼包含範例的步驟。 它不應該在實際系統上執行。 它可能損毀，或在其執行所在的 VM 會損毀。
- 我們建議您在測試 VM 上執行。 開始之前，需要 VM 備份或快照集，以便您可以在需要時還原 VM。 
- 當 VM 正在執行其中一個這類 Linux 散發套件的運作方式的指令碼：
    - Red Hat Enterprise Linux 6.5 + 7.1 +
    - %OS 6.5 + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1、 SP2 SP3
    - Ubuntu 14.04、 16.04、 18.04
    - Debian 7、 8

### <a name="run-the-script"></a>執行指令碼

1. 複製到 Linux 指令碼測試使用 sftp 或例如 FileZilla 或 WinScp scp 用戶端 VM。
2. 透過 ssh 連線到 Linux 機器，使用系統管理員帳戶。
3. 瀏覽至 指令碼目錄。
4. 若要讓指令碼為可執行檔，執行**sudo chmod 777 準備-針對-azure.sh**。
5. 執行指令碼 **./prepare-for-azure.sh**。

以下是指令碼的執行方式：

![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux 指令碼](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>後續步驟

- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)建立高度信賴群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
