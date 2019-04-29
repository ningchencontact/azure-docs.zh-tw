---
title: 在 SAP HANA on Azure (大型執行個體) 上進行 HANA 備份和還原 | Microsoft Docs
description: 如何在 SAP HANA on Azure (大型執行個體) 上執行 HANA 備份和還原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098315"
---
# <a name="backup-and-restore"></a>備份與還原

>[!IMPORTANT]
>本說明文件不可用 SAP HANA 管理說明文件或 SAP 附註取代。 本文預期讀者對 SAP HANA 管理和操作有深入的了解和專業知識，尤其是關於備份、還原、高可用性和災害復原的主題。 在這份說明文件中，會顯示 SAP HANA Studio 的螢幕擷取畫面。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

請務必使用您的 HANA 版本和版次，在您的環境中執行步驟和程序。 為了能讓大眾更全面地了解，這份說明文件中所述的某些程序已簡化，但並非用做最終操作手冊的詳細步驟。 如果您想要為組態建立操作手冊，則需要測試及執行程序，並記錄與特定組態相關的處理程序。 

操作資料庫的其中一個最重要層面，就是保護資料庫免於遭受災難事件。 這些事件可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫時，若提供還原到任何時間點 (例如在某人刪除重要資料之前) 的功能，便可讓還原到儘可能接近中斷之前的狀態。

必須執行兩種類型的備份，以達成這類功能還原：

- 資料庫備份：完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過儲存體快照集執行備份。 儲存體快照集並不會取代交易記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 不過，儲存體快照集可加快復原速度，因為它們可以快速地提供資料庫的向前復原映像。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- 自己動手做 (DIY)。 確定沒有足夠的磁碟空間之後，請使用下列的磁碟備份方法的其中一個執行完整的資料庫和記錄備份。 您可以直接備份到連結至 HANA 大型執行個體單位的磁碟區，或備份到 Azure 虛擬機器 (VM) 中設定的網路檔案共用 (NFS)。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果對直接連結至 HANA 大型執行個體單位的磁碟區執行備份，您必須將備份複製到 Azure 儲存體帳戶 (在設定 Azure VM 來匯出以 Azure 儲存體為基礎的 NFS 共用之後)。 您也可以使用 Azure 備份保存庫或 Azure 冷儲存體。 

   另一個選項是在備份複製到 Azure 儲存體帳戶之後，使用第三方資料保護工具來儲存備份。 針對因合規性和稽核目的而需要較長儲存期的資料，DIY 備份選項可能也有其必要性。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。

- 基礎結構備份和還原功能。 您也可以使用 SAP HANA on Azure (大型執行個體) 的底層基礎結構所提供的備份和還原功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。 本節也涵蓋備份和還原對於 HANA 大型執行個體所提供之災害復原功能的關聯性。

> [!NOTE]
>   HANA 大型執行個體的底層基礎結構所使用的快照集技術相依於 SAP HANA 快照集。 此時，SAP HANA 快照集還無法搭配 SAP HANA 多租用戶資料庫容器的多個租用戶一起使用。 如果只部署一個租用戶，則可使用 SAP HANA 快照集並利用此方法。

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 SAP HANA on Azure (大型執行個體) 的儲存體快照

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區的儲存體快照集。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 針對 /hana/data 和 /hana/shared (包括 /usr/sap) 磁碟區來觸發快照集時，快照集技術會先起始 SAP HANA 快照集，再執行儲存體快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。 若要成功的 HANA 快照集，您需要作用中的 HANA 執行個體。  在 HSR 案例中，無法執行 HANA 快照集的目前次要節點上不支援儲存體快照集。
- 在順利執行儲存體快照集後，系統會刪除 SAP HANA 快照集。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區，對它個別擷取快照集。 在此情況下，您不需要執行 HANA 快照集。
- 如果您必須還原資料庫至特定點時間，請要求該 （適用於生產環境中斷） 的 Microsoft Azure 支援服務 」 或 「 SAP HANA 上 Azure 還原到特定的儲存體快照集。 例如，將沙箱系統還原到其原始狀態的計劃性還原。
- 儲存體快照集所包含的 SAP HANA 快照集是一個位移點，用來套用在儲存體快照集建立後已執行和儲存的交易記錄備份。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

您可以執行以三種磁碟區類別為目標的儲存體快照集：

- 針對 /hana/data 和 /hana/shared (包括 /usr/sap) 的合併快照集。 此快照集需要建立 SAP HANA 快照集，作為儲存體快照集的準備工作。 從儲存體觀點來看，SAP HANA 快照集會確保資料庫處於一致的狀態。 對於還原程序而言，這是一個設定重點。
- 針對 /hana/logbackups 的個別快照集。
- 作業系統分割區。

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 取得最新快照集指令碼和文件。 當您下載快照集指令碼套件，從[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)，取得三個檔案都有提供的功能的 PDF 文件集。 請確定，您繼續沿著一章中的指示 '取得快照集工具' 時下載的工具集。

## <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集會取用已配置給 HANA 大型執行個體單位的儲存空間。 您必須考慮以下有關排定儲存體快照集和保留多少儲存體快照集等方面的問題。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 特定的儲存體快照集 (在建立快照集的時間點) 只會取用少量儲存體。
- 隨著在存放磁碟區上資料內容發生變更及 SAP HANA 資料檔中的內容發生變更，快照便需要儲存原始區塊內容以及資料變更。
- 因此，儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 您需要決定何時要排定儲存體快照集。 您也必須監視存放磁碟區的空間耗用量，以及管理您儲存的快照集數目。 您可以在匯入大量資料或對 HANA 資料庫執行其他重大變更時，停用儲存體快照集。 


下列各節提供執行這些快照集的資訊，包括一般建議事項：

- 雖然硬體的每一磁碟區都可支援 255 個快照，但建議您讓快照數目保持遠低於這個數字。 建議為 250 或更少。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可以降低保留的快照集數目，也可以擴充磁碟區。 您可以訂購額外的儲存空間，以 1 TB 為單位。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，請在 /hana/data 磁碟區上停用儲存體快照集。 
- 在進行較大規模的 SAP HANA 資料表重組期間，應儘可能避免執行儲存體快照。
- 儲存體快照集也是利用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服務儲存體快照集的必要條件

為了確保快照集指令碼執行成功，請確定 HANA 大型執行個體伺服器的 Linux 作業系統上已安裝 Perl。 Perl 已預先安裝在您的 HANA 大型執行個體單元上。 若要檢查 Perl 版本，請使用下列命令：

`perl -v`

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>設定儲存體快照集

若要使用 HANA 大型執行個體來設定儲存體快照集，請遵循下列步驟：
1. 確定 HANA (大型執行個體) 伺服器的 Linux 作業系統上已安裝 Perl。
1. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
1. 針對您目前執行的每個 SAP HANA 執行個體，在主要節點上建立 SAP HANA 備份使用者帳戶 (如果適用)。
1. 在所有 SAP HANA (大型執行個體) 伺服器上安裝 SAP HANA HDB 用戶端。
1. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上，建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
1. 將指令碼和組態檔從 [ GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 複製到 SAP HANA 安裝中的 **hdbsql** 位置。
1. 視需要修改 HANABackupDetails.txt 檔案以符合適當的客戶規格。

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 取得最新快照集指令碼和文件。 如上面所列的詳細步驟，請參閱[Microsoft 的 Azure 上的 SAP HANA 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>MCOD 案例的考量
如果您在某個 HANA 大型執行個體單位上使用多個 SAP HANA 執行個體執行 [MCOD 案例](https://launchpad.support.sap.com/#/notes/1681092)，則會有為每個 SAP HANA 執行個體佈建的個別存放磁碟區。 如需 MDC 和其他考量的詳細資訊，請檢查[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章 **'重要的注意事項'**。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步驟 1：安裝 SAP HANA HDB 用戶端

安裝在 SAP HANA on Azure (大型執行個體) 上的 Linux 作業系統包含針對備份和災害復原目的執行 SAP HANA 儲存體快照集時所需的資料夾與指令碼。 檢查 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 中是否有更近期的版本。 最新發行版本的指令碼為 4.0。 不同的指令碼在相同的主要版本中可能有不同的次要版本。

安裝 SAP HANA 時，是由您負責在 HANA 大型執行個體單位上安裝 SAP HANA HDB 用戶端。

### <a name="step-2-change-the-etcsshsshconfig"></a>步驟 2：變更 /etc/ssh/ssh\_config

此步驟中所述在檢查中的較新版本的詳細資料[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中 **'啟用與存放裝置通訊'**


### <a name="step-3-create-a-public-key"></a>步驟 3：建立公開金鑰

若要能夠存取 HANA 大型執行個體租用戶的儲存體快照集介面，您必須透過公開金鑰建立登入程序。 在租用戶的第一部 SAP HANA on Azure (大型執行個體) 伺服器上，建立用來存取儲存體基礎結構的公開金鑰。 此公開金鑰可確保不需密碼即可登入儲存體快照集介面。 建立公開金鑰，也表示您不需要維護密碼認證。 確切步驟如何產生公用金鑰所述[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中 **'啟用與存放裝置通訊'**


### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

若要開始建立 SAP HANA 快照集，您必須在 SAP HANA 中建立使用者帳戶，以供儲存體快照集指令碼使用。 為此目的，在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 必須在 SYSTEMDB 之下 (而「非」在 MDC 的 SID 資料庫之下) 建立使用者。 在單一容器環境中，會建立租用戶資料庫中的使用者。 此帳戶必須具有下列權限：**備份系統管理員**和**目錄讀取**。 如使用者所設定，並使用使用者的確切步驟，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)一章中 **[啟用與 SAP HANA 的通訊]**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您可針對您所建立的 SAP HANA 使用者帳戶進行授權，讓指令碼不必在執行階段提交密碼。 SAP HANA 命令 `hdbuserstore` 可讓您建立儲存在一或多個 SAP HANA 節點上的 SAP HANA 使用者金鑰。 使用者金鑰可讓使用者存取 SAP HANA，而不需要在指令碼程序內管理密碼。 本文稍後會討論指令碼處理。

>[!IMPORTANT]
>使用相同的使用者內容中執行的快照集命令執行這些組態命令。 否則，快照集命令無法正常運作。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 下載最新版本的指令碼。 版本 4.0 中的指令碼中，指令碼要安裝的方式已變更 majorly。 如需確切詳細資料，閱讀[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中 **[啟用與 SAP HANA 的通訊]**

針對實際命令順序，讀取一章 **'的快照集工具 （預設值） 的簡單安裝 」** 文件[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 我們建議的預設安裝的使用方式。 如果您想要升級從版本 3.x 至 4.0 中，檢查區段 **'升級現有安裝'** 的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 解除安裝的 4.0 版的工具集，請依照下列中的指示 **'的快照集工具解除安裝作業'** 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

別忘了執行中所述的步驟 **'的快照集工具完成安裝程式 」** 的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

不同的指令碼和檔案的用途為它們取得已安裝已列出且詳述 **'這些快照集工具為何？'** 文件[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

設定之前的快照集的工具，請確定，您也 HANA 備份的位置和設定已正確設定中所述 **「 SAP HANA 設定 」** 文件的[Microsoft 的 SAP HANA 快照集工具在 Azure 上](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

快照集的工具組的組態中將詳細說明 **'Config file-HANABackupCustomerDetails.txt'** 文件[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

#### <a name="testing-connectivity-with-sap-hana"></a>測試與 SAP HANA 的連線

將所有組態資料放入 HANABackupCustomerDetails.txt 檔案後，請檢查 HANA 執行個體資料的組態是否正確。 使用指令碼 `testHANAConnection`，其與 SAP HANA 相應增加或相應放大組態無關。

如需詳細資訊，請參閱 **[檢查與 SAP HANA – testHANAConnection 連線]** 文件的[Microsoft 的 Azure 上的 SAP HANA 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>測試的儲存體連線能力

下一個測試步驟是根據您放入 HANABackupCustomerDetails.txt 組態檔的資料來檢查儲存體的連線，然後執行測試快照集。 在執行之前`azure_hana_backup`命令時，您必須執行這項測試。 這項測試的命令序列會列在 **[檢查與儲存體-testStorageSnapshotConnection 連線]** 文件[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 2 並建立測試快照集。 SAP HANA 的三個節點相應放大組態輸出如下所示：

如果已使用指令碼順利執行的測試快照集，您可以繼續進行排程實際的儲存體快照集。 如果不成功，請先調查問題，再繼續進行。 測試快照集應先留著，直到完成第一個實際快照集為止。


### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

當準備步驟都完成後時，您可以開始設定及排程實際的儲存體快照集。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 如需定期或正常執行備份指令碼，請透過使用 cron 公用程式排程指令碼。 

確切的命令語法和功能，請閱讀 **'執行快照集備份-azure_hana_backup'** 文件[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。  

執行 `azure_hana_backup` 指令碼會以下列三個階段建立儲存體快照集：

1. 執行 SAP HANA 快照集
1. 執行儲存體快照集
1. 先移除已建立的 SAP HANA 快照集，再執行儲存體快照集

若要執行指令碼，您可從複製指令碼過去的 HDB 可執行檔資料夾中呼叫它。 

保留期取決於您執行指令碼時以參數送出的快照集數目。 儲存體快照集所涵蓋的時間量，是執行期間的函式以及在執行指令碼時提交做為參數的快照集數目函式構成。 如果所保留的快照集數目超出指令碼呼叫中以參數指定的數目，將會先刪除相同標籤的最舊儲存體快照集，然後再執行新的快照集。 您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 藉此數字，您也可以間接控制快照集所使用的磁碟空間。 


## <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 此功能依賴儲存快照集，其可能需要有關儲存體快照集執行頻率和期間的特殊建議。 

在下面的考量和建議中，假設您「不」使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集來建立備份，且您能夠提供過去 30 天的時間點復原。 在快照集數量和空間有限的情況下，客戶考量了下列需求：

- 時間點復原的復原時間。
- 使用的空間。
- 從災害潛在復原的復原點和復原時間目標。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 **backint** 介面時，執行儲存體快照集都會失敗。 如果除了儲存體快照集之外，您還打算執行完整資料庫備份，請確定在此期間停用儲存體快照集的執行。
- 每個磁碟區的快照集數目 (只能有 250 個)。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

對於不使用 HANA 大型執行個體災害復原功能的客戶，快照集期間較不頻繁。 在這種情況下，客戶會以 12 小時或 24 小時的期間來對 /hana/data 和 /hana/shared (包括 /usr/sap) 執行合併的快照集，並保留這些快照集一個月。 記錄備份磁碟區的快照集也是如此。 然而，執行 SAP HANA 交易記錄備份，記錄備份磁碟區會以 5 到 15 分鐘的期間。

使用 cron 來執行排程儲存體的快照集最為合適。 請針對所有備份和災害復原需求使用相同的指令碼，以及修改指令碼輸入以符合各種要求的備份時間。 這些快照集在 cron 中都會根據其執行時間以不同方式排定：每小時、12 小時、每天或每週。 

下列是 /etc/crontab 中的 cron 排程範例：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在上述範例中，沒有每小時的合併快照集會涵蓋包含/hana/data 磁碟區和 /hana/shared/SID （包括 usr/sap） 的位置。 使用此類快照集以加快過去兩天內的時間點復原速度。 此外，這些磁碟區會有每日快照集。 因此，您會獲得涵蓋兩天的每小時快照集，以及涵蓋四週的每日快照集。 此外，每天會備份一次交易記錄備份磁碟區。 這些備份也會保存四週。 正如您在 crontab 第三行所看到的，系統會排定每 5 分鐘執行一次 HANA 交易記錄備份。 執行儲存體快照集的不同 cron 作業開始時間會錯開，以免系統在特定時間點一次執行所有快照集。 

在下列範例中，您會執行合併的快照集會涵蓋包含 /hana/shared/SID （包括 usr/sap） 與/hana/data 磁碟區上每小時的位置。 您會將這些快照集保留兩天。 交易記錄備份磁碟區的快照集會每隔 5 分鐘執行一次，並保留 4 個小時。 和之前一樣，系統會將 HANA 交易記錄檔排定為每 5 分鐘備份一次。 在開始交易記錄備份後，系統會延後 2 分鐘再執行交易記錄備份磁碟區的快照集。 一般情況下，SAP HANA 交易記錄備份應該能在這 2 分鐘的時間內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週之久。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下圖說明上述範例的順序，但不包括開機 LUN：

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 SAP HANA 交易記錄備份會依照 crontab 的指定每 5 分鐘執行一次。 您也可以看到，SAP HANA 快照集每小時執行 /hana/data 和 /hana/shared/SID 磁碟區觸發合併的儲存體快照集。 HANA 快照集成功之後，就會執行合併的儲存體快照集。 在 HANA 交易記錄備份完成大約 2 分鐘後，系統會依照 crontab 的指示，每隔 5 分鐘在 /hana/logbackup 磁碟區上執行一次儲存體快照集。

> 

>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要涵蓋儲存體快照集之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 在極端的情況下，存取針對 /hana/data 和 /hana/shared/SID 30 天的舊快照集的合併儲存體。
- 擁有連續交易記錄備份，其涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 如果您將交易記錄備份複製到位於 Azure 儲存體上的另一個 NFS 共用，情況並非如此。 在這種情況下，您可以從該 NFS 共用提取舊的交易記錄備份。

若要受惠於儲存體快照集並最終受益於交易記錄備份的儲存體複寫，您必須變更 SAP HANA 用來寫入交易記錄備份的目的地位置。 您可以在 HANA Studio 中進行這項變更。 雖然 SAP HANA 會自動備份完整的記錄區段，但您應該指定記錄備份間隔來加以確定。 使用災害復原選項時尤其如此，因為您通常會想要以確定的期間來執行記錄備份。 在下列案例中，採用 15 分鐘的記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您也可以選擇比每隔 15 分鐘更頻繁的備份。 頻率較高的設定經常與 HANA 大型執行個體的災害復原功能搭配使用。 有些客戶會每隔 5 分鐘執行一次交易記錄備份。  

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


在執行您的第一個成功的儲存體快照集之後，您要刪除步驟 6 所執行的測試快照集。 讀取 **'移除測試快照集 removeTestStorageSnapshot'** 文件中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)如需詳細資訊。 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁碟區上的快照集數目和大小

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 不過，Linux 作業系統命令 `du` 會顯示這些儲存體快照集的詳細資料，因為它們儲存在相同的磁碟區上。 此命令可與下列選項搭配使用：

- `du –sh .snapshot`:此選項提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1`:此選項會列出所有儲存在 **.snapshot** 資料夾中的快照集及每個快照集的大小。
- `du –hc`:此選項會提供所有快照集使用的總大小。

您可以使用這些命令來確定所建立和儲存的快照集並未耗用磁碟區上的所有儲存體。

>[!NOTE]
>使用上述命令無法看到開機 LUN 的快照集。

### <a name="getting-details-of-snapshots"></a>取得快照集的詳細資料
若要取得更詳細的快照集資料，您也可以使用 `azure_hana_snapshot_details` 指令碼。 如果災害復原位置有作用中的伺服器，則可以在任一位置執行此指令碼。 此指令碼提供下列輸出，這些輸出會依包含快照集的每個磁碟區細分： 
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)

如需命令和輸出檢查語法 **'列出快照集-azure_hana_snapshot_details'** 文件中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>縮減伺服器上的快照數目

如先前所述，您可以針對您儲存的快照集，減少某些標籤的數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在前一個範例中，快照集標籤是 **dailyhana**，而此標籤內要保留的快照集數目則是 **28**。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 若要將快照集數目減少為 15 (舉例來說)，有個簡單的方法是將最後一個參數設定為 **15** 來執行指令碼：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果您以此設定執行指令碼，快照集數目 (包括新的儲存體快照集) 會是 15。 15 個最新的快照集會保留下來，然後會刪除 15 個較舊的快照集。

 >[!NOTE]
 > 有存在時間超過 1 小時的快照集時，此指令碼才會減少快照集數目。 指令碼不會刪除過去 1 小時內的快照集。 這些限制與所提供的選擇性災害復原功能有關。

如果您不想再維護一組含有備份的前置詞的快照集**dailyhana**在語法範例中，您可以執行的指令碼**0**作為保留數字。 然後，所有符合該標籤的快照集就會遭到移除。 不過，移除所有快照集可能會影響 HANA 大型執行個體災害復原的功能。

若要刪除特定快照集，第二種選項是使用 `azure_hana_snapshot_delete` 指令碼。 此指令碼的用途在於使用在 HANA Studio 中找到的 HANA 備份識別碼或透過快照集名稱本身，來刪除單一快照集或一組快照集。 備份識別碼目前只會與針對 **hana** 快照集類型所建立的快照集繫結。 **記錄**和**開機**類型的快照集備份不會執行 SAP HANA 快照集，因此找不到這些快照集的備份識別碼。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

如需指令碼的詳細資訊，請參閱 **'刪除快照集-azure_hana_snapshot_delete'** 文件中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

您必須以 **root** 使用者身分執行指令碼。

>[!IMPORTANT]
>如果有資料只存在於您要刪除的快照集上，刪除快照集後，該資料就會永久遺失。

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>從儲存體快照集進行檔案層級還原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
針對快照集類型 **hana** 和 **logs**，您可以直接在 **.snapshot** 目錄中的磁碟區上存取快照集。 每個快照集都有一個子目錄。 您可以將處於快照集製作時間點狀態的每個檔案，從該子目錄複製到實際的目錄結構中。 在指令碼的最新版本，沒有**NO**還原 （雖然可以執行快照集還原，如自助 DR 的一部分指令碼在 DR 站台容錯移轉期間），以自行提供快照集還原指令碼。 您必須藉由開啟服務要求來連絡 Microsoft 作業小組，以從現有可用的快照集還原所需的快照集。

>[!NOTE]
>單一檔案還原不適用於與 HANA 大型執行個體單位類型無關的開機 LUN 快照集。 **.snapshot** 目錄不會在開機 LUN 中顯示出來。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

若遇到生產環境停止運作的情況，可以向「Microsoft Azure 支援服務」以客戶事件的形式起始從儲存體快照集復原的程序。 如果生產系統中的資料遭到刪除，而挽救資料的唯一方法是還原生產資料庫，則為高緊急性的情況。

若是其他情況，則時間點復原可能為低緊急性，可提前幾天規劃。 您可以在 Azure 上規劃 SAP HANA 使用此復原，而非引發高優先旗標。 例如，您可能打算藉由套用新的增強套件來升級 SAP 軟體。 然後，您必須還原為代表增強套件升級前狀態的快照集。

傳送要求之前，您必須做準備。 SAP HANA on Azure 團隊可以再處理要求，並提供已還原的磁碟區。 之後，您會根據快照集來還原 HANA 資料庫。

一節中記載的可能性，取得快照集還原，並使用新的工具組 **「 如何還原快照集 」** 文件[手動復原指南的 SAP HANA on Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

以下將說明您如何為要求做準備：

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

1. 關閉 HANA 執行個體。

   ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。
   ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 提出 Azure 支援要求，以及包含還原特定快照集的指示。

   - 還原期間︰SAP HANA on Azure 可能會要求您參加電話會議，以確保協調、 驗證和確認正確的儲存體快照集還原。 

   - 還原之後：已還原的儲存體快照集時，Azure 服務上的 SAP HANA 會通知您。

1. 在還原程序完成後，重新掛接所有資料磁碟區。

   ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



如需入門，比方說，從儲存體快照集復原的 SAP HANA 資料檔案的另一個可能性記載於文件的步驟 7[手動復原指南的 SAP HANA on Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

文件[手動復原指南的 SAP HANA on Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)說明從快照集備份的還原順序。 用於還原的執行中的文件。 

>[!Note]
>找不到執行，如果您有 Microsoft operations 還原快照集時所需的步驟 7。


### <a name="recover-to-another-point-in-time"></a>復原到另一個時間點
文件[手動復原指南的 SAP HANA on Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)一節中說明的時間的某個時點的還原順序 **' 將資料庫復原到下列點時間'**. 用於還原至某個點時間的執行中的文件。 


## <a name="next-steps"></a>後續步驟
- 請參閱[災害復原原則和準備](hana-concept-preparation.md)。
