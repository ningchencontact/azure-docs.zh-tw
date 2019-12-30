---
title: 關於 MARS 代理程式
description: 瞭解 MARS 代理程式如何支援備份案例
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897321"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>關於 Microsoft Azure 復原服務（MARS）代理程式

本文說明 Azure 備份服務如何使用 Microsoft Azure 復原服務（MARS）代理程式，將檔案/資料夾、磁片區或系統狀態從內部部署電腦備份和還原至 Azure。

MARS 代理程式支援下列備份案例：

![復原服務保存庫儀表板](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- 檔案**和資料夾**：選擇性地保護 Windows 檔案和資料夾。
- **磁片區層級**：保護電腦的整個 Windows 磁片區。
- **系統層級**：保護整個 Windows 系統狀態。

MARS 代理程式支援下列還原案例：

![復原服務保存庫儀表板](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **相同的伺服器**：原先建立備份的同一部伺服器。
    -    檔案**和資料夾**：您可以流覽並選擇您想要還原的個別檔案和資料夾。
    -    **磁片區層級**：您可以選擇要還原的磁片區和復原點，並將它還原到相同電腦上的相同位置或替代位置。  您可以建立現有檔案的複本、覆寫現有的檔案，或略過復原現有的檔案。
    -    **系統層級**：您可以選擇要在指定位置還原至同一部電腦的系統狀態和復原點。


-   **替代伺服器**：另一部伺服器，也就是執行備份的伺服器不是相同的伺服器。
    -    檔案**和資料夾**：您可以流覽並選擇您想要將復原點還原至目的電腦的個別檔案和資料夾。
    -    **磁片區層級**：您可以藉由建立現有檔案的複本、覆寫現有檔案或略過復原現有檔案的方式，選擇要還原至替代位置的磁片區和復原點。
    -    **系統層級**：您可以選擇 [系統狀態] 和 [復原點]，將系統狀態檔案還原至替代電腦。

## <a name="backup-process"></a>備份程序

1.  從 Azure 入口網站建立復原服務保存[庫](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)，並從備份目標選擇檔案和資料夾及/或系統狀態。
2.  將復原服務保存庫認證和代理程式安裝程式[下載](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)到內部部署電腦。 若要保護內部部署機器，請選擇 [檔案和資料夾] 和 [系統狀態]，然後下載 MARS 代理程式。
3.  準備基礎結構：

    a.    執行安裝程式以[安裝](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)代理程式。

    b.這是另一個 C# 主控台應用程式。  使用已下載的保存庫認證將電腦註冊到復原服務保存庫。
4.  從用戶端上的代理程式主控台，使用[排程備份](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy)來設定備份。 指定備份資料的保留原則，然後開始保護。

![復原服務保存庫儀表板](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>其他案例
-   **備份 AZURE vm 中的特定檔案和資料夾**-備份 azure 虛擬機器（vm）的主要方法是使用 VM 上的 Azure 備份延伸模組。 此步驟會備份整個 VM。 如果您想要備份 VM 內的特定檔案和資料夾，您可以在 Azure Vm 中安裝 MARS 代理程式。 [深入了解提出技術問題。

-   **離線植**入-將資料備份至 Azure 的初始完整備份，通常會傳輸大量資料，而相較于只傳輸差異/增量的後續備份，則需要更多的網路頻寬。 Azure 備份會壓縮初始備份。 透過離線植入程序，Azure 備份可以使用磁碟將壓縮後的初始備份資料離線上傳至 Azure。 [深入了解提出技術問題。


## <a name="next-steps"></a>後續步驟
[MARS 代理程式支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[常見問題-MARS 代理程式](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
