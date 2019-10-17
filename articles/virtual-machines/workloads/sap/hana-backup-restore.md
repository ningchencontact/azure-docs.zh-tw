---
title: 在 SAP HANA on Azure (大型執行個體) 上進行 HANA 備份和還原 | Microsoft Docs
description: 如何在 SAP HANA on Azure (大型執行個體) 上執行 HANA 備份和還原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430086"
---
# <a name="backup-and-restore"></a>備份及還原

>[!IMPORTANT]
>本文不是 SAP Hana 系統管理檔或 SAP Notes 的替代方案。 我們預期您對 SAP Hana 系統管理和作業有深刻的瞭解和專長，特別是針對備份、還原、高可用性和嚴重損壞修復。 在本文中，會顯示 SAP Hana Studio 的螢幕擷取畫面。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

請務必使用您的 HANA 版本和版次，在您的環境中執行步驟和程序。 本文中所述的某些程式已簡化，以提供更佳的一般瞭解。 它們不是用來做為最終作業手冊的詳細步驟。 如果您想要為您的設定建立作業手冊，請測試並執行您的程式，並記錄與您的特定設定相關的進程。 

運算元據庫的其中一個最重要層面，就是保護它們免于發生嚴重事件。 這些事件可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫時，能夠將它還原到任何時間點（例如在某人刪除重要資料之前），可以讓還原到盡可能接近中斷之前的狀態。

您必須執行兩種類型的備份，才能達成還原功能：

- 資料庫備份：完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過儲存體快照集執行備份。 儲存體快照集不會取代交易記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 儲存體快照集可以快速提供資料庫的向前復原映射來加速復原。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- **自己動手做（DIY）。** 確定有足夠的磁碟空間之後，請使用下列其中一種磁片備份方法來執行完整資料庫和記錄備份。 您可以直接備份到連結至 HANA 大型實例單位的磁片區，或備份到 Azure 虛擬機器（VM）中所設定的 NFS 共用。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果您對直接附加至 HANA 大型實例單位的磁片區執行備份，請將備份複製到 Azure 儲存體帳戶。 設定 Azure VM 以匯出以 Azure 儲存體為基礎的 NFS 共用之後，請執行此動作。 您也可以使用 Azure 備份保存庫或 Azure 冷儲存體。 

   另一個選項是在將備份複製到 Azure 儲存體帳戶之後，使用協力廠商資料保護工具來儲存它們。 對於需要儲存長時間進行合規性與審核的資料，也可能需要 DIY 備份選項。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。

- **基礎結構備份和還原功能。** 您也可以使用 Azure （大型實例）上 SAP Hana 基礎結構所提供的備份和還原功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。 本節也涵蓋備份和還原對於 HANA 大型實例所提供之嚴重損壞修復功能的關聯性。

> [!NOTE]
>   HANA 大型實例基礎結構所使用的快照集技術相依于 SAP Hana 快照集。 此時，SAP Hana 快照集無法搭配 SAP Hana 多租使用者資料庫容器的多個租使用者一起使用。 如果只部署一個租使用者，SAP Hana 快照集就會正常執行，而且您可以使用這個方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>在 Azure （大型實例）上使用 SAP Hana 的儲存體快照集

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區的儲存體快照集。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 在/hana/data 和/hana/shared （包括/usr/sap、磁片區）觸發快照集時，快照集技術會在執行儲存體快照集之前，先起始 SAP Hana 快照集。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。 若要讓 HANA 快照集成功，您需要使用中的 HANA 實例。 在 HSR 案例中，無法執行 HANA 快照集的目前次要節點上不支援儲存體快照集。
- 儲存體快照集執行成功之後，就會刪除 SAP Hana 快照集。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區，對它個別擷取快照集。 在此情況下，您不需要執行 HANA 快照集。
- 如果您必須將資料庫還原到特定時間點，針對生產環境中斷，請要求在 Azure 上 Microsoft Azure 支援或 SAP Hana 還原至特定的儲存體快照集。 例如，將沙箱系統還原到其原始狀態的計劃性還原。
- 儲存體快照集所包含的 SAP Hana 快照集是一個位移點，用來套用執行的交易記錄備份，並在建立儲存體快照集之後儲存。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

您可以執行以三個磁片區類別為目標的儲存體快照集：

- 結合了/hana/data 和/hana/shared 的快照集，其中包括/usr/sap 之下 此快照集需要建立 SAP HANA 快照集，作為儲存體快照集的準備工作。 SAP Hana 快照集可確保資料庫在儲存體的觀點之下處于一致的狀態。 在還原程式中，這是在上設定的重點。
- 針對 /hana/logbackups 的個別快照集。
- 作業系統分割區。

若要取得最新的快照集腳本和檔，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 當您從[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)下載快照集腳本套件時，您會取得三個檔案。 其中一個檔案會記載在 PDF 中，以取得所提供的功能。 下載工具組之後，請依照「取得快照集工具」中的指示進行。

## <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>儲存體快照集會耗用配置給 HANA 大型實例單位的儲存空間。 請考慮排程儲存體快照集的下列層面，以及要保留多少儲存體快照集。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 特定的儲存體快照集在取得時間點時，會耗用少量的儲存空間。
- 當資料內容變更以及 SAP Hana 資料檔案中的內容變更時，快照集需要儲存原始區塊內容和資料變更。
- 因此，儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 您必須：

- 決定排程儲存體快照集的時機。
- 監視存放磁片區的空間耗用量。 
- 管理您儲存的快照集數目。 

您可以在匯入大量資料或對 HANA 資料庫執行其他重大變更時，停用儲存體快照集。 


下列各節提供執行這些快照集和包含一般建議的資訊：

- 雖然硬體可以承受每個磁片區的255快照集，但您想要保持在這個數位以下。 建議為250或更少。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可以降低保留的快照集數目，也可以擴充磁碟區。 您可以訂購額外的儲存空間，以 1 TB 為單位。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，請在 /hana/data 磁碟區上停用儲存體快照集。 
- 在較大的 SAP Hana 資料表重組期間，盡可能避免儲存體快照集。
- 儲存體快照集也是利用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服務儲存體快照集的必要條件

若要確保快照集腳本執行成功，請確定 HANA 大型實例伺服器上的 Linux 作業系統上已安裝 Perl。 Perl 已預先安裝在您的 HANA 大型實例單位上。 若要檢查 Perl 版本，請使用下列命令：

`perl -v`

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>設定儲存體快照集

若要使用 HANA 大型實例來設定儲存體快照集，請遵循下列步驟。
1. 確定 HANA (大型執行個體) 伺服器的 Linux 作業系統上已安裝 Perl。
1. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
1. 針對您執行的每個 SAP Hana 實例，在主要節點上建立 SAP Hana 備份使用者帳戶（如果適用）。
1. 在所有 SAP HANA (大型執行個體) 伺服器上安裝 SAP HANA HDB 用戶端。
1. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上，建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
1. 將指令碼和組態檔從 [ GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 複製到 SAP HANA 安裝中的 **hdbsql** 位置。
1. 視需要修改 HANABackupDetails.txt 檔案以符合適當的客戶規格。

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 取得最新快照集指令碼和文件。 如需先前所列的步驟，請參閱[Azure 上適用于 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 案例的考量
如果您在一個 HANA 大型實例單位上執行具有多個 SAP Hana 實例的[MCOD 案例](https://launchpad.support.sap.com/#/notes/1681092)，則會為每個 SAP Hana 實例布建個別的存放磁片區。 如需有關 MDC 和其他考慮的詳細資訊，請參閱[Azure 上的 Microsoft snapshot tools for SAP Hana](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「要記住的重要事項」。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步驟 1：安裝 SAP HANA HDB 用戶端

安裝在 Azure 上 SAP Hana （大型實例）上的 Linux 作業系統包含針對備份和災難復原目的執行 SAP Hana 儲存體快照集所需的資料夾和腳本。 檢查 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 中是否有更近期的版本。 

當您安裝 SAP Hana 時，您必須負責在 HANA 大型實例單位上安裝 SAP Hana HDB 用戶端。

### <a name="step-2-change-the-etcsshssh_config"></a>步驟 2：變更 /etc/ssh/ssh\_config

在[適用于 Azure 的 Microsoft 快照集工具中 SAP Hana 的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)「啟用與儲存體的通訊」中，會說明此步驟。


### <a name="step-3-create-a-public-key"></a>步驟 3︰建立公開金鑰

若要存取 HANA 大型實例租使用者的儲存體快照集介面，請透過公開金鑰建立登入程式。 

在租使用者中 Azure （大型實例）伺服器上的第一個 SAP Hana 上，建立公開金鑰以存取儲存體基礎結構。 使用公開金鑰時，不需要密碼就能登入儲存體快照集介面。 您也不需要使用公開金鑰來維護密碼認證。 

若要產生公開金鑰，請參閱在[Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「啟用與存放裝置的通訊」。


### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

若要開始建立 SAP Hana 快照集，請在儲存體快照集腳本可以使用的 SAP Hana 中建立使用者帳戶。 為此目的，在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 使用者必須建立在 SYSTEMDB 底下，而*不*是在適用于 MDC 的 SID 資料庫之下。 在單一容器環境中，會在租使用者資料庫中建立使用者。 此帳戶必須具有「**備份管理員**」和「**目錄讀取**」許可權。 

若要設定和使用使用者帳戶，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「啟用與 SAP Hana 的通訊」。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您會授權所建立的 SAP Hana 使用者帳戶，讓腳本不需要在執行時間提交密碼。 SAP Hana 命令 `hdbuserstore` 可讓您建立 SAP Hana 的使用者金鑰。 金鑰會儲存在一或多個 SAP Hana 節點上。 使用者金鑰可讓使用者存取 SAP HANA，而不需要在指令碼程序內管理密碼。 本文稍後會討論指令碼處理。

>[!IMPORTANT]
>使用快照集命令執行所在的相同使用者內容來執行這些設定命令。 否則，快照集命令將無法正常運作。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) 下載最新版本的指令碼。 腳本的安裝方式隨著腳本的版本4.1 而變更。 如需詳細資訊，請參閱[Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「啟用與 SAP Hana 的通訊」。

如需確切的命令順序，請參閱[Azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「輕鬆安裝快照集工具（預設）」。 我們建議使用預設安裝。 

若要從版本3.x 升級至4.1，請參閱[Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「升級現有的安裝」。 若要卸載4.1 工具組，請參閱[Microsoft snapshot tools for SAP Hana On Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的

別忘了在[Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中，執行「完成快照集工具的完整設定」中所述的步驟。

「這些快照集工具是什麼？」中會說明安裝的腳本和檔案的用途。 在[適用于 Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中。

設定快照集工具之前，請確定您也已正確設定 HANA 備份位置和設定。 如需詳細資訊，請參閱[Azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「SAP Hana 設定」。

在[Azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的「設定檔-hanabackupcustomerdetails.txt .txt」中會描述快照集工具集的設定。

#### <a name="test-connectivity-with-sap-hana"></a>測試與 SAP Hana 的連線能力

將所有組態資料放入 HANABackupCustomerDetails.txt 檔案後，請檢查 HANA 執行個體資料的組態是否正確。 使用指令碼 `testHANAConnection`，其與 SAP HANA 相應增加或相應放大組態無關。

如需詳細資訊，請參閱[Azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的

#### <a name="test-storage-connectivity"></a>測試儲存體連線能力

下一個測試步驟是根據您放入*hanabackupcustomerdetails.txt*設定檔中的資料，檢查存放裝置的連線能力。 然後執行測試快照集。 執行 `azure_hana_backup` 命令之前，您必須執行此測試。 如需這項測試的命令順序，請參閱[Azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 2 並建立測試快照集。 這裡會顯示輸出，其中包含三個節點的 SAP Hana 相應放大設定。

如果使用腳本成功執行測試快照集，您可以排程實際的儲存體快照集。 如果不成功，請先調查問題，再繼續進行。 測試快照集應先留著，直到完成第一個實際快照集為止。


### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

當準備步驟完成時，您可以開始設定和排程實際的儲存體快照集。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 如需定期或正常執行備份指令碼，請透過使用 cron 公用程式排程指令碼。 

如需確切的命令語法和功能，請參閱[azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 

當腳本 `azure_hana_backup` 執行時，它會在下列三個階段建立儲存體快照集：

1. 它會執行 SAP Hana 快照集。
1. 它會執行儲存體快照集。
1. 它會移除在儲存體快照集執行之前所建立的 SAP Hana 快照集。

若要執行腳本，請從複製它的 HDB 可執行檔資料夾中呼叫它。 

當您執行腳本時，會使用提交為參數的快照集數目來管理保留期間。 儲存體快照集所涵蓋的時間量是執行期間的函式，以及腳本執行時提交作為參數的快照集數目。 

如果所保留的快照集數目超出腳本呼叫中以參數命名的數目，則會先刪除相同標籤的最舊儲存體快照集，然後再執行新的快照集。 您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 使用此數位，您也可以間接控制快照集所使用的磁碟空間。 


## <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 此功能依賴儲存快照集，其可能需要有關儲存體快照集執行頻率和期間的特殊建議。 

在下面的考量和建議中，假設您「不」使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集來建立備份，且您能夠提供過去 30 天的時間點復原。 假設快照集和空間的數目有所限制，請考慮下列需求：

- 時間點復原的復原時間。
- 使用的空間。
- 從災害潛在復原的復原點和復原時間目標。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 **backint** 介面時，執行儲存體快照集都會失敗。 如果您打算在儲存體快照集上執行完整資料庫備份，請確定在這段期間已停用儲存體快照集的執行。
- 每個磁片區的快照集數目，限制為250。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果您未使用 HANA 大型實例的嚴重損壞修復功能，則快照集期間較不頻繁。 在這種情況下，請在/hana/data 和/hana/shared 上執行合併的快照集，其中包括/usr/sap、12小時或24小時的期間。 將快照集保留一個月。 記錄備份磁片區的快照集也是如此。 針對記錄備份磁片區執行 SAP Hana 交易記錄備份，會在5分鐘到15分鐘的期間內發生。

使用 cron 來執行排程儲存體的快照集最為合適。 針對所有備份和嚴重損壞修復需求使用相同的腳本。 修改指令碼輸入，以符合各種要求的備份時間。 這些快照集會根據執行時間以不同的方式排定在 cron 中。 它可以是每小時、每12小時、每日或每週。 

下列範例顯示/etc/crontab 中的 cron 排程：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在上述範例中，每小時合併的快照集涵蓋包含/hana/data 和/hana/shared/SID 的磁片區，其中包括/usr/sap、位置。 使用此類快照集以加快過去兩天內的時間點復原速度。 在這些磁片區上也有每日快照集。 因此，每小時快照集有兩天的涵蓋範圍，再加上每日快照集四周的涵蓋範圍。 交易記錄備份磁片區也會每天備份。 這些備份會保留四周。 

如您在 crontab 第三行所看到的，HANA 交易記錄檔的備份排程為每5分鐘執行一次。 執行儲存體快照集的不同 cron 作業的開始時間會錯開。 如此一來，快照集就不會在特定時間點一次執行。 

在下列範例中，您會執行合併的快照集，其中涵蓋包含/hana/data 和/hana/shared/SID 的磁片區，其中包含/usr/sap 的位置（以小時為單位）。 您會將這些快照集保留兩天。 交易記錄備份磁片區的快照集會每5分鐘執行一次，並保留四個小時。 就像之前一樣，HANA 交易記錄檔的備份已排程為每5分鐘執行一次。 

在開始交易記錄備份後，系統會延後 2 分鐘再執行交易記錄備份磁碟區的快照集。 在正常情況下，SAP Hana 的交易記錄備份會在這2分鐘內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週之久。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下圖說明前一個範例的順序。 已排除開機 LUN。

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 如 crontab 所指定，SAP Hana 交易記錄備份每隔5分鐘執行一次。 

您也會看到 SAP Hana 快照集會每小時執行一次，因為觸發/hana/data 和/hana/shared/SID 磁片區上的合併儲存體快照集。 HANA 快照集成功之後，就會執行合併的儲存體快照集。 如 crontab 中的指示，/hana/logbackup 磁片區上的儲存體快照集會在 HANA 交易記錄備份後的2分鐘內每隔5分鐘執行一次。

> 

>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要涵蓋儲存體快照集之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 在極端情況下，透過/hana/data 和/hana/shared/SID 存取合併的儲存體快照集。 
- 擁有連續交易記錄備份，其涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 如果您將交易記錄備份複製到位於 Azure 儲存體的另一個 NFS 共用，就不會發生這種情況。 在這種情況下，您可以從該 NFS 共用提取舊的交易記錄備份。

若要從儲存體快照集受益于交易記錄備份的最終儲存體複寫，請變更 SAP Hana 寫入交易記錄備份的位置。 您可以在 HANA Studio 中進行這項變更。 

雖然 SAP Hana 會自動備份完整的記錄區段，但請指定要具決定性的記錄備份間隔。 當您使用嚴重損壞修復選項時尤其如此，因為您通常會想要以具決定性的期間來執行記錄備份。 在下列情況下，會將15分鐘設定為記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您也可以選擇比每隔15分鐘更頻繁的備份。 頻率較高的設定經常與 HANA 大型執行個體的災害復原功能搭配使用。 有些客戶會每隔 5 分鐘執行一次交易記錄備份。

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP Hana 無法起始您指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


在您第一次成功執行儲存體快照集之後，請刪除在步驟6中執行的測試快照集。 如需詳細資訊，請參閱[Azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁片區上的快照集數目和大小

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 Linux OS 命令 `du` 會顯示這些儲存體快照集的詳細資料，因為它們儲存在相同的磁片區上。 使用命令搭配下列選項：

- `du –sh .snapshot`：此選項提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1`：此選項會列出所有儲存在 **.snapshot** 資料夾中的快照集及每個快照集的大小。
- `du –hc`：此選項會提供所有快照集使用的總大小。

請使用這些命令來確定所建立和儲存的快照集不會耗用磁片區上的所有存放裝置。

>[!NOTE]
>開機 LUN 的快照集不會顯示在先前的命令中。

### <a name="get-details-of-snapshots"></a>取得快照集的詳細資料
若要取得快照集的詳細資料，請使用腳本 `azure_hana_snapshot_details`。 如果嚴重損壞修復位置中有作用中的伺服器，您可以在任一位置執行此腳本。 此指令碼提供下列輸出，這些輸出會依包含快照集的每個磁碟區細分： 
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)

如需命令和輸出的語法，請參閱[azure 上 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>減少伺服器上的快照集數目

如先前所述，您可以減少所儲存之快照集的特定標籤數目。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在上述範例中，快照集標籤為**dailyhana**。 此標籤所要保留的快照集數目為**28**。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 例如，將快照集數目減少為15個簡單的方法，就是使用最後一個參數設定為**15**來執行腳本：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果您使用此設定來執行腳本，快照集數目（包括新的儲存體快照集）為15。 15 個最新的快照集會保留下來，然後會刪除 15 個較舊的快照集。

 >[!NOTE]
 > 只有當快照集的時間超過一小時，此腳本才會減少快照集數目。 腳本不會刪除少於一小時的快照集。 這些限制與所提供的選擇性災害復原功能有關。

如果您不想再使用語法範例中的備份前置詞**dailyhana**來維護一組快照集，請以**0**做為保留數目來執行腳本。 然後會移除所有符合該標籤的快照集。 移除所有快照集可能會影響 HANA 大型實例嚴重損壞修復功能的功能。

若要刪除特定快照集，第二種選項是使用 `azure_hana_snapshot_delete` 指令碼。 此指令碼的用途在於使用在 HANA Studio 中找到的 HANA 備份識別碼或透過快照集名稱本身，來刪除單一快照集或一組快照集。 備份識別碼目前只會與針對 **hana** 快照集類型所建立的快照集繫結。 **記錄**和**開機**類型的快照集備份不會執行 SAP Hana 快照集，因此不會針對這些快照集找到任何備份識別碼。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

如需腳本的詳細資訊，請參閱[azure 上的 SAP Hana 的 Microsoft 快照集工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的

以使用者**根目錄**的形式執行腳本。

>[!IMPORTANT]
>如果資料只存在於您打算刪除的快照集上，則在刪除快照集之後，該資料會永遠遺失。


## <a name="file-level-restore-from-a-storage-snapshot"></a>從儲存體快照集進行檔案層級還原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
針對快照集類型 **hana** 和 **logs**，您可以直接在 **.snapshot** 目錄中的磁碟區上存取快照集。 每個快照集都有一個子目錄。 將位於快照集點的每個檔案，從該子目錄複寫到實際的目錄結構中。 

在目前的腳本版本中，*沒有*針對快照集還原為自助提供的還原腳本。 在容錯移轉期間，您可以在嚴重損壞修復網站上執行「快照集還原」做為自助嚴重損壞修復腳本的一部分。 若要從現有的可用快照集還原所需的快照集，您必須開啟服務要求來聯絡 Microsoft 作業小組。

>[!NOTE]
>單一檔案還原不適用於啟動 LUN 的快照集，與 HANA 大型實例單位的類型無關。 **快照**集目錄不會在開機 LUN 中公開。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

在生產環境停機的案例中，從儲存體快照集復原的程式可以啟動為具有 Microsoft Azure 支援的客戶事件。 如果生產系統中的資料遭到刪除，而且取得它的唯一方法是還原生產資料庫，這是非常重要的事。

若是其他情況，則時間點復原可能為低緊急性，可提前幾天規劃。 您可以使用 Azure 上的 SAP Hana 來規劃此復原，而不是提高高優先順序的旗標。 例如，您可能會想要套用新的增強套件來升級 SAP 軟體。 然後，您必須還原為代表增強套件升級前狀態的快照集。

傳送要求之前，您必須做準備。 然後，Azure 小組上的 SAP Hana 可以處理要求，並提供已還原的磁片區。 之後，您會根據快照集來還原 HANA 資料庫。

如需使用新的工具集來還原快照集的可能性，請參閱手動復原指南中的「如何還原快照」，這[是 Azure 中從儲存體快照集的 SAP Hana](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

若要為要求做準備，請遵循下列步驟。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

1. 關閉 HANA 執行個體。

   ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。

   ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 開啟 Azure 支援要求，並包含有關還原特定快照集的指示：

   - 在還原期間： Azure 服務上的 SAP Hana 可能會要求您參加會議呼叫，以協調、確認及確認是否還原正確的儲存體快照集。 

   - 還原之後： SAP Hana on Azure 服務會在儲存體快照集還原時通知您。

1. 在還原程序完成後，重新掛接所有資料磁碟區。

   ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



從儲存體快照集復原 SAP Hana 資料檔案的另一種可能性，記載于[Azure 上從儲存體快照集的 SAP Hana 的手動修復指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的步驟7。

若要從快照集備份進行還原，請參閱[從儲存體快照集在 Azure 上 SAP Hana 的手動復原指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 

>[!Note]
>如果您的快照集是由 Microsoft 作業還原，您就不需要執行步驟7。


### <a name="recover-to-another-point-in-time"></a>復原到另一個時間點
若要還原至特定時間點，請參閱手動復原指南中的[從儲存體快照集在 Azure 上進行 SAP Hana](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)的「將資料庫復原到下列時間點」。 


## <a name="next-steps"></a>後續步驟
- 請參閱嚴重損壞[修復原則和準備](hana-concept-preparation.md)。
