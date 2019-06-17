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
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64987905"
---
# <a name="backup-and-restore"></a>備份與還原

>[!IMPORTANT]
>這篇文章不是取代的 SAP HANA 管理說明文件 」 或 「 SAP 附註。 我們希望您擁有深厚的知識和專業知識，在 SAP HANA 管理和作業，特別是針對備份、 還原、 高可用性和災害復原。 在本文中，會顯示 SAP HANA Studio 的螢幕擷取畫面。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

請務必使用您的 HANA 版本和版次，在您的環境中執行步驟和程序。 這篇文章中所述的某些處理程序已簡化的較佳的一般知識。 它們不是用來做最終操作手冊的詳細步驟。 如果您想要為您的組態建立操作手冊，測試及執行程序，並記錄您的特定設定相關處理序。 

操作資料庫最重要的層面之一是保護它們免於發生災難事件。 這些事件可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

將資料庫備份，並能夠還原到任何時間點的時間，例如之前某人刪除重要資料，可讓還原到狀態，則為盡可能接近發生中斷之前的方式。

必須執行兩種類型的備份，以達到還原的功能：

- 資料庫備份：完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過儲存體快照集執行備份。 儲存體快照集不會取代交易記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 儲存體快照集，可以加快復原速度可以快速地提供資料庫的向前復原映像。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- **這麼做 (DIY) 自己。** 您確定沒有足夠的磁碟空間之後，執行完整資料庫和記錄備份使用其中一個下列的磁碟備份方法。 您可以將備份直接對連結至 HANA 大型執行個體單位，或在 Azure 虛擬機器 (VM) 中設定的 NFS 共用的磁碟區。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果您執行對直接連結至 HANA 大型執行個體單位的磁碟區備份，請將備份複製到 Azure 儲存體帳戶。 設定匯出 NFS 共用以 Azure 儲存體為基礎的 Azure VM 之後，請執行這項操作。 您也可以使用 Azure 備份保存庫 」 或 「 Azure 冷儲存體。 

   另一個選項是使用協力廠商資料保護工具，它們複製到 Azure 儲存體帳戶之後，儲存的備份。 DIY 備份選項也可能需要針對您需要較長的合規性和稽核目的而儲存的資料。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。

- **基礎結構備份和還原功能。** 您也可以使用備份和還原 SAP HANA on Azure （大型執行個體） 的底層基礎結構所提供的功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。 本節也涵蓋備份和還原也不必在損毀的關聯性的 HANA 大型執行個體提供復原功能。

> [!NOTE]
>   使用 HANA 大型執行個體的底層基礎結構的快照集技術會具有相依於 SAP HANA 快照集。 此時，SAP HANA 快照集無法搭配 SAP HANA 多租用戶資料庫容器的多個租用戶。 如果只部署一個租用戶時，SAP HANA 快照集，您可以使用這個方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用儲存體快照集的 SAP HANA on Azure （大型執行個體）

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區的儲存體快照集。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 當快照集，就會觸發對 /hana/data 和 /hana/shared，其中包含 /usr/sap 之下，磁碟區的快照集技術先起始 SAP HANA 快照集之前它會執行儲存體快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。 若要成功的 HANA 快照集，您需要作用中的 HANA 執行個體。 在 HSR 案例中，儲存體快照集不支援目前的次要節點，不能在其中執行的 HANA 快照集。
- 儲存體快照集成功執行之後，會刪除 SAP HANA 快照集。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區，對它個別擷取快照集。 在此情況下，您不需要執行 HANA 快照集。
- 如果您必須還原資料庫至特定點時間，適用於生產環境中斷，要求的 Microsoft Azure 支援服務 」 或 「 SAP HANA 上 Azure 還原到特定的儲存體快照集。 例如，將沙箱系統還原到其原始狀態的計劃性還原。
- 儲存體快照集所包含的 SAP HANA 快照集是一個位移的點，套用交易記錄備份已執行並建立儲存體快照集儲存。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

您可以執行儲存體快照集為目標的磁碟區的三個類別：

- 合併的快照集/hana/data 和 hana/共用，其中包含 /usr/sap 之下。 此快照集需要建立 SAP HANA 快照集，作為儲存體快照集的準備工作。 SAP HANA 快照集可確保資料庫處於一致的狀態，從儲存體觀點來看。 還原程序中，這是要設定上的點。
- 針對 /hana/logbackups 的個別快照集。
- 作業系統分割區。

若要取得最新的快照集指令碼和文件，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)。 當您下載快照集指令碼套件，從[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)，取得三個檔案。 其中一個檔案會詳細說明的 PDF，針對提供的功能。 下載此工具集之後，請依照指示在 「 取得快照集工具 」。

## <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集耗用了配置給 HANA 大型執行個體單位的儲存空間。 請考慮排定儲存體快照集和保留多少儲存體快照集的下列層面。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 在建立時的時間點的特定儲存體快照集耗用小小的儲存體。
- 做為資料內容發生變更及 SAP HANA 資料檔案變更存放磁碟區的內容，快照便需要儲存原始區塊內容和資料變更。
- 因此，儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 您必須：

- 決定何時要排定儲存體快照集。
- 監視存放磁碟區的空間耗用量。 
- 管理您儲存的快照集數目。 

您可以在匯入大量資料或對 HANA 資料庫執行其他重大變更時，停用儲存體快照集。 


下列各節提供執行這些快照集的資訊，並包含一般的建議：

- 雖然硬體可支援 255 的快照集，每個磁碟區，您會想要數目保持遠低於這個數字。 建議是 250 個或更少。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可以降低保留的快照集數目，也可以擴充磁碟區。 您可以訂購額外的儲存空間，以 1 TB 為單位。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，請在 /hana/data 磁碟區上停用儲存體快照集。 
- 在較大的 SAP HANA 資料表重組，期間請盡可能避免儲存體快照集。
- 儲存體快照集也是利用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服務儲存體快照集的必要條件

若要確定快照集指令碼順利執行，請確定 HANA 大型執行個體的伺服器上 Linux 作業系統上已安裝 Perl。 Perl 已預先安裝在 HANA 大型執行個體單位上。 若要檢查 Perl 版本，請使用下列命令：

`perl -v`

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>設定儲存體快照集

若要設定與 HANA 大型執行個體的儲存體快照集，請遵循下列步驟。
1. 確定 HANA (大型執行個體) 伺服器的 Linux 作業系統上已安裝 Perl。
1. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
1. 如果適用的話，請在您執行時，每個 SAP HANA 執行個體的主要節點上建立 SAP HANA 備份使用者帳戶。
1. 在所有 SAP HANA (大型執行個體) 伺服器上安裝 SAP HANA HDB 用戶端。
1. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上，建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
1. 將指令碼和組態檔從 [ GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 複製到 SAP HANA 安裝中的 **hdbsql** 位置。
1. 視需要修改 HANABackupDetails.txt  檔案以符合適當的客戶規格。

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 取得最新快照集指令碼和文件。 如先前所列的步驟，請參閱 < [Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 案例的考量
如果您執行[MCOD 案例](https://launchpad.support.sap.com/#/notes/1681092)在某個 HANA 大型執行個體單位上的多個 SAP HANA 執行個體，您可以針對每個 SAP HANA 執行個體佈建的個別存放磁碟區。 如需有關 MDC 和其他考量的詳細資訊，請參閱 「 重要的注意事項 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步驟 1：安裝 SAP HANA HDB 用戶端

安裝 SAP HANA on Azure （大型執行個體） 上的 Linux 作業系統包含的資料夾和執行 SAP HANA 儲存體快照集備份和災害復原用途所需的指令碼。 檢查 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 中是否有更近期的版本。 最新發行版本的指令碼為 4.0。 不同的指令碼在相同的主要版本中可能有不同的次要版本。

您必須負責安裝 SAP HANA 時，在 HANA 大型執行個體單位上安裝 SAP HANA HDB 用戶端。

### <a name="step-2-change-the-etcsshsshconfig"></a>步驟 2：變更 /etc/ssh/ssh\_config

此步驟中所述的 「 啟用通訊使用儲存體 」 [Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。


### <a name="step-3-create-a-public-key"></a>步驟 3：建立公開金鑰

若要存取您的 HANA 大型執行個體租用戶的儲存體快照集介面，請透過公開金鑰建立登入程序。 

在第一次 SAP HANA on Azure （大型執行個體） 伺服器，在您的租用戶中，建立公用金鑰來存取儲存體基礎結構。 使用公開金鑰，不需要密碼才能登入的儲存體快照集介面。 您也不需要具有公開金鑰維護密碼認證。 

若要產生的公開金鑰，請參閱 「 啟用通訊使用儲存體 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。


### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

若要開始建立 SAP HANA 快照集，請在儲存體快照集指令碼可以使用的 SAP HANA 中建立使用者帳戶。 為此目的，在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 必須在 SYSTEMDB 之下建立使用者並*不*MDC 的 SID 資料庫之下。 在單一容器環境中，會建立租用戶資料庫中的使用者。 此帳戶必須具備**Backup Admin**並**目錄讀取**權限。 

若要設定及使用使用者帳戶，請參閱 「 使用 SAP HANA 啟用通訊 」 中[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您必須授權，讓指令碼不需要提交在執行階段的密碼，您所建立的 SAP HANA 使用者帳戶。 SAP HANA 命令`hdbuserstore`可讓您建立的 SAP HANA 使用者金鑰。 金鑰會儲存在一或多個 SAP HANA 節點上。 使用者金鑰可讓使用者存取 SAP HANA，而不需要在指令碼程序內管理密碼。 本文稍後會討論指令碼處理。

>[!IMPORTANT]
>使用相同的使用者內容內執行的快照集命令執行這些組態命令。 否則，就無法正常運作的快照集命令。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 下載最新版本的指令碼。 指令碼的安裝的方式變更版本 4.0 中的指令碼。 如需詳細資訊，請參閱 「 啟用與 SAP HANA < 通訊中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

實際命令順序，請參閱 「 快照集的工具 （預設值） 的簡單安裝 」，在[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 我們建議使用預設安裝。 

若要升級從版本 3.x 至 4.0，請參閱 < 升級現有安裝 > 中的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 若要解除安裝的 4.0 版的工具集，請參閱 「 快照集工具解除安裝作業 」 中的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

別忘了執行 「 完成安裝程式的快照集工具 」 中所述的步驟中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

不同的指令碼和檔案的用途為它們取得已安裝所述為何這些快照集工具嗎？ 」 在  [Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

您設定快照集工具之前，請確定您也設定 HANA 備份的位置和設定正確。 如需詳細資訊，請參閱 「 SAP HANA 組態 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

快照集工具組的組態所述在 「 設定檔-HANABackupCustomerDetails.txt 」 [Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

#### <a name="test-connectivity-with-sap-hana"></a>測試與 SAP HANA 的連線

將所有組態資料放入 HANABackupCustomerDetails.txt  檔案後，請檢查 HANA 執行個體資料的組態是否正確。 使用指令碼 `testHANAConnection`，其與 SAP HANA 相應增加或相應放大組態無關。

如需詳細資訊，請參閱 「 使用 SAP HANA-testHANAConnection 檢查連線 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

#### <a name="test-storage-connectivity"></a>測試儲存體連線能力

下一個測試步驟是檢查根據您放入資料的儲存體的連線*HANABackupCustomerDetails.txt*組態檔。 然後執行測試快照集。 在執行之前`azure_hana_backup`命令時，您必須執行這項測試。 這項測試的命令序列，請參閱 「 請檢查連線與儲存體-testStorageSnapshotConnection 「 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 2 並建立測試快照集。 輸出是這裡顯示的 SAP HANA 的三個節點相應放大組態。

如果測試快照集與指令碼執行成功，您可以排程實際的儲存體快照集。 如果沒有成功，請在您繼續之前調查問題。 測試快照集應先留著，直到完成第一個實際快照集為止。


### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

當準備步驟都完成後時，您可以開始設定及排程實際的儲存體快照集。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 如需定期或正常執行備份指令碼，請透過使用 cron 公用程式排程指令碼。 

確切的命令語法和功能，請參閱 「 執行快照集備份-azure_hana_backup 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 

當指令碼`azure_hana_backup`執行時，它會建立儲存體快照集的下列三個階段：

1. 它會執行 SAP HANA 快照集。
1. 它會執行儲存體快照集。
1. 它會移除儲存體快照集執行前已建立的 SAP HANA 快照集。

若要執行指令碼，請從已複製的 HDB 可執行檔資料夾中呼叫。 

當您執行指令碼時提交做為參數的快照集數目取決於保留期限。 儲存體快照集所涵蓋量是時間的期間執行，以及指令碼執行時以參數送出的快照集數目的函式。 

如果所保留的快照集數目超出指定的名為指令碼的呼叫中的參數數目，新的快照集執行之前刪除相同標籤的最舊儲存體快照集。 您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 使用此號碼，您也可以控制，間接，快照集所使用的磁碟空間。 


## <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 此功能依賴儲存快照集，其可能需要有關儲存體快照集執行頻率和期間的特殊建議。 

在下面的考量和建議中，假設您「不」  使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集來建立備份，且您能夠提供過去 30 天的時間點復原。 指定數目的快照集和空間限制，請考慮下列需求：

- 時間點復原的復原時間。
- 使用的空間。
- 從災害潛在復原的復原點和復原時間目標。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 **backint** 介面時，執行儲存體快照集都會失敗。 如果您打算執行完整資料庫備份儲存體快照集，請務必在這段期間是停用的儲存體快照集執行。
- 每個磁碟區，也就是限制為 250 的快照集數目。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果您不使用 HANA 大型執行個體的災害復原功能，則快照集期間會是較不頻繁。 在這種情況下，執行合併的快照集來對 /hana/data 和 /hana/shared，其中包含 /usr/sap 之下，以 12 小時制或 24 小時的期間。 一個月中保留這些快照集。 這也適用於記錄備份磁碟區的快照集。 SAP HANA 交易記錄備份，記錄備份磁碟區的執行就會發生在 5 分鐘到 15 分鐘的期間。

使用 cron 來執行排程儲存體的快照集最為合適。 針對所有備份和災害復原需求使用相同的指令碼。 修改指令碼輸入，以符合各種要求的備份時間。 這些快照集是所有以不同方式排定在 cron 根據其執行時間。 它可以是每小時、 每個 12 小時、 每天或每週。 

下列範例會示範 /etc/crontab 中的 cron 排程：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在上述範例中，每小時的合併快照集會涵蓋包含 /hana/data 和 /hana/shared/SID，其中包含位置的 /hana/shared 磁碟區。 使用此類快照集以加快過去兩天內的時間點復原速度。 另外還有每日快照集磁碟區上。 因此，您必須涵蓋兩天的每小時的快照集以及涵蓋四週的每日快照集。 交易記錄備份磁碟區也會備份每日。 這些備份會保留四週。 

您在 crontab 第三行中看到時，HANA 交易記錄備份被排定為每 5 分鐘執行。 執行儲存體快照集的不同 cron 作業的開始時間會錯開系統。 如此一來，快照集不執行一次在某個時點的時間。 

在下列範例中，您可以執行合併的快照集會涵蓋含有 /hana/data 和 /hana/shared/SID (包括 /usr/sap） 位置，以小時為基礎的磁碟區。 您會將這些快照集保留兩天。 交易記錄備份磁碟區的快照集執行 5 分鐘，並保留四個小時。 之前一樣，HANA 交易記錄檔的備份被排定為每隔 5 分鐘執行一次。 

在開始交易記錄備份後，系統會延後 2 分鐘再執行交易記錄備份磁碟區的快照集。 在正常情況下，SAP HANA 交易記錄備份會在這 2 分鐘的時間內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週之久。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下圖說明上述範例的序列。 開機 LUN 會被排除。

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 依照 crontab 的指定 SAP HANA 交易記錄備份會執行每隔 5 分鐘。 

您也可以看到 SAP HANA 快照集，會因為觸發合併的儲存體，所有針對 /hana/data 和 /hana/shared/SID 的磁碟區快照集，而每小時執行。 HANA 快照集成功之後，合併的儲存體快照集執行。 依照 crontab 的指示，每隔 5 分鐘，大約 2 分鐘在 HANA 交易記錄備份之後，會執行在 /hana/logbackup 磁碟區上的儲存體快照集。

> 

>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要涵蓋儲存體快照集之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 針對 /hana/data 和 /hana/shared/SID 30 天之久，在極端情況下，存取合併儲存體快照集。 
- 擁有連續交易記錄備份，其涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 如果您將交易記錄備份複製到位於 Azure 儲存體的另一個 NFS 共用，這並不是這樣。 在這種情況下，您可以從該 NFS 共用提取舊的交易記錄備份。

若要受惠於儲存體快照集和最終儲存體複寫的交易記錄備份，將變更的 SAP HANA 用來寫入交易記錄備份的位置。 您可以在 HANA Studio 中進行這項變更。 

雖然 SAP HANA 會自動備份完整的記錄區段，指定記錄備份間隔來加以確定。 當您使用災害復原選項，因為您通常想要確定的期間執行記錄備份時，這是特別有用。 在下列案例中，15 分鐘的時間會設定為記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您也可以選擇比每隔 15 分鐘更頻繁的備份。 頻率較高的設定經常與 HANA 大型執行個體的災害復原功能搭配使用。 有些客戶會每隔 5 分鐘執行一次交易記錄備份。

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


執行您的第一個成功的儲存體快照集之後，刪除在步驟 6 中執行的測試快照集。 如需詳細資訊，請參閱 「 移除測試快照集-removeTestStorageSnapshot 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁碟區上的快照集的大小與數量

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 Linux OS 命令`du`顯示這些儲存體快照集的詳細資料，因為它們儲存在相同的磁碟區上。 您可以使用命令搭配下列選項：

- `du –sh .snapshot`:此選項提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1`:此選項會列出所有儲存在 **.snapshot** 資料夾中的快照集及每個快照集的大小。
- `du –hc`:此選項會提供所有快照集使用的總大小。

您可以使用下列命令來確定所建立和儲存的快照集不會使用磁碟區上的所有儲存體。

>[!NOTE]
>使用上述命令看不到 開機 LUN 的快照集。

### <a name="get-details-of-snapshots"></a>取得快照集的詳細資料
若要取得快照集的詳細資訊，請使用 指令碼`azure_hana_snapshot_details`。 如果災害復原位置中沒有作用中的伺服器，您可以在任一位置執行此指令碼。 此指令碼提供下列輸出，這些輸出會依包含快照集的每個磁碟區細分： 
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)

命令和輸出的語法，請參閱 「 列出快照集-azure_hana_snapshot_details 」，在[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>降低在伺服器上的快照集數目

如先前所述，您可以減少某些標籤，您將儲存的快照集的數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在上述範例中，快照集標籤是**dailyhana**。 此標籤內要保留的快照集數目是**28**。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 快照集數目減少為 15，比方說，簡單方法是設定為將最後一個參數執行指令碼**15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果您執行指令碼使用這項設定時，快照集數目，其中包含新的儲存體快照集，就會是 15。 15 個最新的快照集會保留下來，然後會刪除 15 個較舊的快照集。

 >[!NOTE]
 > 只有當有超過一個小時的快照集，此指令碼會減少快照集數目。 指令碼並不會刪除小於一小時的快照集。 這些限制與所提供的選擇性災害復原功能有關。

如果您不想再維護一組含有備份的前置詞的快照集**dailyhana**在語法範例中，執行具有指令碼**0**作為保留數字。 然後會移除所有符合該標籤的快照集。 移除所有快照集可能會影響 HANA 大型執行個體的災害復原功能的功能。

若要刪除特定快照集，第二種選項是使用 `azure_hana_snapshot_delete` 指令碼。 此指令碼的用途在於使用在 HANA Studio 中找到的 HANA 備份識別碼或透過快照集名稱本身，來刪除單一快照集或一組快照集。 備份識別碼目前只會與針對 **hana** 快照集類型所建立的快照集繫結。 類型的快照集備份**記錄檔**並**開機**不執行 SAP HANA 快照集，因此沒有要尋找這些快照集備份識別碼。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

如需有關此指令碼的詳細資訊，請參閱 「 刪除的快照集-azure_hana_snapshot_delete 」，在[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

以使用者身分執行指令碼**根**。

>[!IMPORTANT]
>如果有資料只存在於快照集，想要刪除，刪除快照集之後，資料會永久遺失。


## <a name="file-level-restore-from-a-storage-snapshot"></a>從儲存體快照集進行檔案層級還原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
針對快照集類型 **hana** 和 **logs**，您可以直接在 **.snapshot** 目錄中的磁碟區上存取快照集。 沒有快照集的每個子目錄。 將每個檔案複製在製作快照集從該子目錄到實際的目錄結構的時間點的狀態。 

在目前版本中的指令碼，還有*沒有*還原為快照集還原，以自行提供的指令碼。 在容錯移轉期間，可以自助災害復原指令碼，在災害復原站台的一部分執行快照集還原。 若要從現有可用的快照集還原所需的快照集，您必須連絡 Microsoft 營運小組開啟服務要求。

>[!NOTE]
>單一檔案還原不適用於開機 LUN 的 HANA 大型執行個體單位類型無關的快照集。 **.Snapshot**目錄不會在開機 LUN 中。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

在生產環境停止運作的情況中，從儲存體快照集復原的程序可以與 Microsoft Azure 支援服務以客戶事件的形式啟動。 如果生產系統中的資料遭到刪除，並擷取它的唯一方法是還原生產資料庫，它是高急迫性問題。

若是其他情況，則時間點復原可能為低緊急性，可提前幾天規劃。 您可以在 Azure 上規劃 SAP HANA 使用此復原，而非引發高優先旗標。 例如，您可能打算套用新的增強套件升級 SAP 軟體。 然後，您必須還原為代表增強套件升級前狀態的快照集。

傳送要求之前，您必須做準備。 SAP HANA on Azure 團隊可以再處理要求，並提供已還原的磁碟區。 之後，您會根據快照集來還原 HANA 資料庫。

如取得新的工具組，使用還原的快照集作業的可能性，請參閱 「 如何還原快照集 」 中[手動復原的 SAP HANA 指南上 Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

若要準備要求，請遵循下列步驟。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

1. 關閉 HANA 執行個體。

   ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。

   ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 開啟 Azure 支援要求，並包含還原特定快照集的相關指示：

   - 還原期間︰SAP HANA on Azure 服務可能會要求您參加電話會議，以協調、 驗證及確認正確的儲存體快照集還原。 

   - 還原之後：還原儲存體快照集時，Azure 服務上的 SAP HANA 會通知您。

1. 在還原程序完成後，重新掛接所有資料磁碟區。

   ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



如需入門，比方說，從儲存體快照集復原的 SAP HANA 資料檔案的另一個可能性記載於中的步驟 7[手動復原的 SAP HANA 指南上 Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

若要從快照集備份還原，請參閱[手動復原的 SAP HANA 指南上 Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。 

>[!Note]
>如果您的快照集還原由 Microsoft 作業，您不需要執行步驟 7。


### <a name="recover-to-another-point-in-time"></a>復原到另一個時間點
若要還原到某個點時間，請參閱 「 將資料庫復原到下列點的時間 」，在[手動復原的 SAP HANA 指南上 Azure 儲存體快照集從](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。 


## <a name="next-steps"></a>後續步驟
- 請參閱[災害復原原則和準備](hana-concept-preparation.md)。
