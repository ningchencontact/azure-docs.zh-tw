---
title: 備份 Azure 備份的 MARS 代理程式的 Windows 機器
description: 使用 Azure 備份 Microsoft 復原服務 (MARS) 代理程式來備份 Windows 機器。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810372"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>備份 Azure 備份的 MARS 代理程式的 Windows 機器

這篇文章說明如何備份使用的 Windows 電腦[Azure 備份](backup-overview.md)服務和 Microsoft Azure 復原服務 (MARS) 代理程式，也就是 Azure 備份代理程式。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 確認必要條件和建立復原服務保存庫。
> * 下載並設定 MARS 代理程式
> * 建立備份原則和排程。
> * 執行臨機操作備份。

## <a name="about-the-mars-agent"></a>關於 MARS 代理程式

MARS 代理程式會使用 Azure 備份將備份檔案、 資料夾和系統狀態從內部部署機器和 Azure Vm 到 Azure 備份復原服務保存庫。 您可以執行代理程式，如下所示：

- 直接在內部部署 Windows 機器上執行代理程式，以便它們可以直接備份至 Azure 備份復原服務保存庫。
- 執行代理程式的 Azure Vm 特定的檔案和資料夾備份的 VM 上執行 Windows （--並存的 Azure VM 備份擴充功能）。
- 在 Microsoft Azure 備份伺服器 (MABS) 或 System Center Data Protection Manager (DPM) 伺服器上執行代理程式。 在此案例中，機器和工作負載備份到 MABS/DPM 中，並接著 MABS/DPM 備份至保存庫在 Azure 中使用 MARS 代理程式。
您可以備份的內容取決於安裝的代理程式。

> [!NOTE]
> 備份 Azure Vm 的主要方法是在 VM 上使用 Azure 備份擴充功能。 此步驟會備份整個 VM。 您可能想要安裝和使用 MARS 代理程式，連同延伸模組，如果您想要備份 VM 上的特定檔案和資料夾。 [深入了解](backup-architecture.md#architecture-direct-backup-of-azure-vms)。

![備份程序步驟](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始之前

- [了解如何](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)Azure 備份會備份與 MARS 代理程式的 Windows 機器。
- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs)備份次要的 MABS 或 DPM 伺服器上執行的 MARS 代理程式的架構。
- [檢閱](backup-support-matrix-mars-agent.md)支援的項目和項目可以備份使用 MARS 代理程式。
- 請確認您想要備份的電腦上的網際網路存取。
- 若要將伺服器或用戶端備份至 Azure，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立 [免費帳戶](https://azure.microsoft.com/free/) 。

### <a name="verify-internet-access"></a>確認網際網路存取

如果您的電腦具有受限的網際網路存取，請確定電腦或 proxy 上的防火牆設定允許這些 Url 和 IP 位址：

**URL**

- www\.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**IP 位址**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫會儲存所有備份和復原點，您建立一段時間，並包含套用至備份的機器的備份原則。 請依照下列方式建立保存庫：

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 在搜尋中，輸入**復原服務**然後按一下**復原服務保存庫**。

    ![建立復原服務保存庫的步驟 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. 在 **復原服務保存庫**功能表上，按一下 **+ 新增**。

    ![建立復原服務保存庫的步驟 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. 在 [名稱] 中，輸入易記名稱來識別保存庫。

   - 必須是 Azure 訂用帳戶中唯一的名稱。
   - 它可以包含 2 到 50 個字元。
   - 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

5. 选择要在其中创建保管库的 Azure 订阅、资源组和地理区域。 備份資料會傳送至保存庫。 接著，按一下 [建立]。

    ![建立復原服務保存庫的步驟 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - 建立保存庫可能需要一些時間。
   - 請監視入口網站右上方區域中的狀態通知。 如果在數分鐘後，您沒有看到保存庫，按一下**重新整理**。

     ![按一下 [重新整理] 按鈕。](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>設定儲存體備援

Azure 備份會自動處理保存庫的儲存體。 您必須指定該儲存體的複寫方式。

1. 從 [復原服務保存庫] 刀鋒視窗，按一下 [新增保存庫]。 底下**設定**區段中，按一下**屬性**。
2. 在“属性”中的“备份配置”下，单击“更新”。

3. 选择存储复制类型，然后单击“保存”。

      ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- 我們建議，如果您使用 Azure 作為主要的備份儲存體端點，繼續使用預設值**異地備援**設定。
- 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]，以減少 Azure 儲存體成本。
- 深入了解[異地](../storage/common/storage-redundancy-grs.md)並[本機](../storage/common/storage-redundancy-lrs.md)備援。

## <a name="download-the-mars-agent"></a>下載的 MARS 代理程式

下載您想要備份的電腦上安裝的 MARS 代理程式。

- 如果您已經在任何電腦上安裝代理程式，請確定您正在執行最新版本。
- 最新的版本是用於入口網站，或使用[直接下載](https://aka.ms/azurebackup_agent)

1. 在保存庫底下**快速入門**，按一下**備份**。

    ![開啟備份目標刀鋒視窗](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. 在**地方執行您的工作負載？**，選取**內部**。 即使您想要在 Azure VM 上安裝的 MARS 代理程式，您應該選取此選項。
3. 在**怎麼想要備份？**，選取**檔案和資料夾**和/或**系統狀態**。 有許多其他選項，但如果您執行次要備份伺服器，都只會支援這些。 按一下 **準備基礎結構**。

      ![設定檔案和資料夾](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. 在 **準備基礎結構**下方**安裝復原服務代理程式**，下載 MARS 代理程式。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. 在下載快顯功能表中，按一下 [儲存]。 根據預設，**MARSagentinstaller.exe** 檔案會儲存至 [下載] 資料夾。

6. 現在，請檢查**已下載或使用最新的復原服務代理程式**，然後下載保存庫認證。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. 按一下 [檔案] 。 檔案會下載至您的下載資料夾中。 您無法開啟保存庫認證檔。

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

1. 執行**MARSagentinstaller.exe**您想要備份的機器上的檔案。
2. 在 MARS 代理程式安裝精靈 >**安裝設定**，指定您想要用來安裝代理程式，並用於快取位置。 然後按 [下一步] 。
   - Azure 備份會使用快取，然後傳送給 Azure 中儲存資料快照集。
   - 快取位置的可用空間應該等於至少 5%的資料，您將備份的大小。

     ![MARS 精靈安裝設定](./media/backup-configure-vault/mars1.png)

2. 在  **Proxy 設定**，指定如何在 Windows 電腦上執行的代理程式會連接到網際網路。 然後按 [下一步] 。

   - 如果您使用自訂 proxy 指定的 proxy 設定和如果所需的認證。
   - 請記住，代理程式需要存取[這些 Url](#verify-internet-access)。

     ![MARS 精靈的網際網路存取](./media/backup-configure-vault/mars2.png)

3. 在 **安裝**檢閱必要條件檢查，然後按一下 **安裝**。
4. 在安裝代理程式之後，請按一下**繼續註冊**。
5. 在 [**註冊的伺服器] 精靈** > **保存庫識別**，瀏覽並選取您所下載的憑證檔案。 然後按 [下一步] 。

    ![註冊-保存庫認證](./media/backup-configure-vault/register1.png)

6. 在 **加密設定**，指定將用來加密與解密機器備份的複雜密碼。

    - 將加密複雜密碼存放在安全的位置。
    - 如果您遺失或忘記複雜密碼，Microsoft 無法協助您復原備份資料。 將檔案存放在安全的位置。 您需要還原的備份。

7. 按一下 **完成**。 現已安裝代理程式，且已向保存庫註冊您的電腦。 您已準備好可以設定及排程備份。

## <a name="create-a-backup-policy"></a>建立備份原則

備份原則會指定何時才會建立復原點資料的快照集，以及要保留復原點。

- 您設定使用 MARS 代理程式的備份原則。
- Azure 備份不會自動考量日光節約時間 (DST)。 這可能會造成一些實際的時間和排定的備份時間之間的差異。

建立原則如下所示：

1. 每個電腦上，開啟的 MARS 代理程式。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。
2. 在 **動作**，按一下**排程備份**。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

3. 在 排程備份精靈 >**快速入門**，按一下**下一步**。
4. 在 **選取要備份的項目**，按一下**新增的項目**。
5. 在 **選取項目**，選取您想要備份。 然後按一下 [確定] 。
6. 在 [**選取要備份的項目**頁面上，按一下**下一步]**。
7. 在 **指定備份排程**頁面上，指定當您想要進行每日或每週備份。 然後按 [下一步] 。

    - 在建立備份時，會建立復原點。
    - 在您的環境中建立的復原點數目是取決於您的備份排程。

1. 您可以排程每日備份，最多三次一天。 例如，螢幕擷取畫面顯示兩個每日備份，一個在午夜，一個在下午 6 點。

    ![每日排程](./media/backup-configure-vault/day-schedule.png)

9. 您也可以執行每週備份。 例如，螢幕擷取畫面會顯示備份每個替代週的星期日和星期三上午 9:30 和 1:00 AM。

    ![每週排程](./media/backup-configure-vault/week-schedule.png)

8. 在 **選取保留原則**頁面上，指定如何將儲存資料的歷程記錄複本。 然後按 [下一步] 。

   - 保留設定指定應儲存的復原點，以及應該會將它們儲存為的時間長度。
   - 例如，當您設定每日的保留設定，您指示，在為每日保留指定的時間，最新的復原點將會保留指定天數。 或者，您也可以另舉一例，您可以指定每月的保留原則，以指出，應該儲存 12 個月的每個月 30 號上建立的復原點。
   - 每日及每週復原點保留期通常會伴隨的備份排程。 這表示根據排程觸發的備份時，備份所建立的復原點會儲存每日或每週保留原則所示的持續時間。
   - 例如，下列螢幕擷取畫面：
     - 午夜和下午 6 點的每日備份會保留七天。
     - 星期六午夜和下午 6 點所進行的備份會保留 4 週。
     - 上星期六午夜和下午 6 個月的最後一週上建立的備份會保留 12 個月。 -星期六年 3 月的最後一週中所進行的備份會保留 10 年。

   ![保留範例](./media/backup-configure-vault/retention-example.png)

11. 在 **選擇初始備份類型**指定才會在初始備份，透過網路或離線的方式。 然後按 [下一步] 。

10. 在 **確認**，檢閱資訊，，然後按一下**完成**。
11. 當精靈建立好備份排程時，請按一下 [關閉] 。

### <a name="perform-the-initial-backup-offline"></a>執行離線初始備份

您可以執行初始備份會自動透過網路或離線。 離線植入初始備份是很有用，如果您有大量的資料，而需要大量網路頻寬來傳輸。 這麼做將離線的傳輸，如下所示：

1. 您可以撰寫備份資料到暫存位置。
2. 您可以用以 AzureOfflineBackupDiskPrep 工具的資料複製到一或多個 SATA 磁碟的預備位置。
3. 此工具會建立 Azure 匯入作業。 [了解更多](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)關於 Azure 匯入和匯出。
4. 您可將 SATA 磁碟傳送至 Azure 資料中心中。
5. 在資料中心，磁碟的資料會複製到 Azure 儲存體帳戶。
6. Azure 備份將資料從儲存體帳戶複製到保存庫，並排程增量備份。

[了解更多](backup-azure-backup-import-export.md)有關離線植入。

### <a name="enable-network-throttling"></a>啟用網路節流

您可以控制網路頻寬的使用方式的 MARS 代理程式藉由啟用網路節流設定。 節流是有幫助，如果您要在工作時間內備份資料，但想要控制多少頻寬用於備份和還原活動。

- Azure 備份網路節流會使用[服務品質 (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top)本機作業系統上。
- 備份的節流的網路是適用於 Windows Server 2008 R2 及更新版本和 Windows 7 及更新版本。 作業系統應該執行的最新的 service pack。

啟用網路節流設定，如下所示：

1. 在 MARS 代理程式中，按一下**變更屬性**。
2. 在 **節流**索引標籤上，勾選**啟用網際網路頻寬使用節流來執行備份作業**。

    ![網路節流](./media/backup-configure-vault/throttling-dialog.png)
3. 在工作和工作時間以外，請指定允許的頻寬。 頻寬值從開始 512 Kbps，並最多 1023 MBps。 然後按一下 [確定] 。

## <a name="run-an-ad-hoc-backup"></a>執行臨機操作備份

1. 在 MARS 代理程式中，按一下**立即備份**。 這樣就能開始初始複寫的網路上。

    ![Windows Server 立即備份](./media/backup-configure-vault/backup-now.png)

2. 在 **確認**，檢閱設定，然後按一下**Back Up**。
3. 按一下 [關閉]  即可關閉精靈。 如果在備份完成之前，您可以這樣做，精靈會繼續在背景執行。

完成初始備份之後，備份主控台中會顯示 [作業已完成]  狀態。

## <a name="next-steps"></a>後續步驟

[了解如何](backup-azure-restore-windows-server.md)還原檔案。
