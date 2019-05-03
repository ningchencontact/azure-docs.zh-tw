---
title: SAP HANA on Azure （大型執行個體） 的嚴重損壞網站 HANA 容錯移轉程序 |Microsoft Docs
description: 如何執行容錯移轉至災害復原站台的 SAP HANA on Azure （大型執行個體）
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
ms.openlocfilehash: f7d4f6216b4a57796ab5c0296713316dd97c47a8
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987896"
---
# <a name="disaster-recovery-failover-procedure"></a>災害復原容錯移轉程序


>[!IMPORTANT]
>這篇文章不是取代的 SAP HANA 管理說明文件 」 或 「 SAP 附註。 我們希望您擁有深厚的知識和專業知識，在 SAP HANA 管理和作業，特別是針對備份、 還原、 高可用性和災害復原 (DR)。 在本文中，會顯示 SAP HANA Studio 的螢幕擷取畫面。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

有兩種情況，考量當您容錯移轉至 DR 網站：

- 您需要將 SAP HANA 資料庫恢復到最新的資料狀態。 在此情況下，沒有用，您可以執行容錯移轉，而不需要連絡 Microsoft 的自助服務指令碼。 容錯回復，您需要與 Microsoft 合作。
- 您想要還原到儲存體快照集不是最新複寫快照集。 在此情況下，您需要與 Microsoft 合作。 

>[!NOTE]
>在代表 DR 單位的 HANA 大型執行個體單位上，必須完成下列步驟。 
 
若要還原到最新複寫的儲存體快照集，請遵循中 「 執行完整的災害復原容錯移轉-azure_hana_dr_failover 」 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 

如果您想要有多個容錯移轉的 SAP HANA 執行個體，執行數次 azure_hana_dr_failover 命令。 要求時，輸入您想要容錯移轉及還原之 SAP HANA SID。 


您可以也測試災害復原容錯移轉，而不會影響實際的複寫關聯性。 若要執行測試容錯移轉，請遵循 「 執行測試 DR 容錯移轉-azure_hana_test_dr_failover 」 中的步驟中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 

>[!IMPORTANT]
>請勿*未*在您完成程序在 DR 網站中建立的執行個體上執行任何生產異動**測試容錯移轉**。 命令 azure_hana_test_dr_failover 會建立一組主要站台沒有任何關聯的磁碟區。 如此一來，「不」可能同步回主要站台。 

如果您想要有多個 SAP HANA 執行個體，若要測試，執行指令碼數次。 要求時，輸入您想要測試容錯移轉的執行個體 SAP HANA SID。 

>[!NOTE]
>如果您需要容錯移轉至 DR 網站以救援小時前已刪除某些資料，而且需要將 DR 磁碟區設定為較早的快照，適用於此程序。 

1. 關閉 HANA 您正在執行的 HANA 大型執行個體的災害復原單位上的非生產執行個體。 已預先安裝休眠的 HANA 生產執行個體。
1. 確定沒有任何 SAP HANA 程序處於執行狀態。 使用下列命令進行這項檢查：

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 。

      您應該會從輸出中看到 **hdbdaemon** 程序處於已停止狀態，而且已沒有其他 HANA 程序處於執行中或已啟動狀態。
1. 決定要讓災害復原網站還原到哪個快照集名稱或 SAP HANA 備份識別碼。 在真實的災害復原案例中，此快照集通常會是最新的快照集。 如果您需要復原遺失的資料，請挑選較早的快照集。
1. 透過高優先順序的支援要求連絡 Azure 支援。 要求的名稱與該快照集還原的日期和快照式或在 DR 網站上的 HANA 備份識別碼。 預設值是作業端只會還原 /hana/data 磁碟區。 如果您想要也有/hana/logbackups 磁碟區，您需要特別指明。 請勿還原 /hana/shared 磁碟區。 相反地，選擇特定的檔案，例如 global.ini **.snapshot**目錄和子目錄重新掛接 /hana/shared 之後共用 PRD 磁碟區。 

   在作業端，系統將會進行下列步驟：

   a. 停止將快照集從生產磁碟區複寫至災害復原磁碟區。 如果生產網站中斷是您需要執行災害復原程序的原因，則可能已經發生中斷。
   
   b. 此儲存體快照集名稱或具有您所選備份識別碼的快照集會在災害復原磁碟區上還原。
   
   c. 還原後，災害復原磁碟區即可供掛接到災害復原區域中的 HANA 大型執行個體單位。
      
1. 將災害復原磁碟區掛接至災害復原網站中的 HANA 大型執行個體單位。 
1. 啟動休眠的 SAP HANA 生產執行個體。
1. 如果您選擇複製交易記錄備份的記錄以降低 RPO 時間，交易記錄備份合併到新掛接 DR/hana 的 /hana/logbackups 目錄。 請勿覆寫現有的備份。 複製未與儲存體快照集的最新的複寫進行複寫的較新備份。
1. 您也可以還原沒有複寫到 DR Azure 區域中 /hana/shared/PRD 磁碟區的快照集的單一檔案。

下列步驟示範如何復原已還原的儲存體快照集和交易記錄備份，可為基礎的 SAP HANA 生產執行個體。

1. 使用 SAP HANA Studio，將備份位置變更為 **/hana/logbackups**。

   ![變更 DR 復原的備份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 會掃描所有備份檔案位置，並建議可作為還原目的地的最新交易記錄備份。 掃描可能需要幾分鐘的時間之前的畫面顯示下列內容：

   ![清單中的 DR 復原的交易記錄備份](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 調整某些預設設定：

      - 清除 [使用差異備份]。
      - 選取 [初始化記錄區域]。

   ![設定初始化記錄區域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 選取 [完成]。

   ![完成 DR 還原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

此時應該會出現進度視窗，如下所示。 請記住，這是具有 3 節點相應放大 SAP HANA 組態的災害復原還原範例。

![還原進度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果還原在停止回應**完成**畫面上並不會顯示進度畫面，確認背景工作節點上的所有 SAP HANA 執行個體正在都執行。 如有必要，請手動啟動 SAP HANA 執行個體。


## <a name="failback-from-a-dr-to-a-production-site"></a>從 DR 容錯回復到生產網站
您可以從 DR 容錯回復到生產網站。 讓我們看看以下情況：因為生產 Azure 區域發生問題而容錯移轉至災害復原網站，而不是因為您需要復原遺失的資料。 

您已執行您的 SAP 生產工作負載一段時間在災害復原站台。 由於生產網站中的問題已經解決，因此您想容錯回復到生產網站。 因為您不能遺失資料，所以退回生產網站的作業會涉及許多步驟，並且要與 SAP HANA on Azure 營運小組密切合作。 在問題獲得解決之後，由您決定是否觸發營運小組來開始反向同步處理到生產網站的程序。

請遵循下列步驟：

1. SAP HANA on Azure 營運小組取得觸發程序，以從災害復原存放磁碟區 (目前代表生產狀態) 同步處理生產存放磁碟區。 處於此狀態時，生產網站中的 HANA 大型執行個體單位會關閉。
1. SAP HANA on Azure 營運小組監視複寫，並確保，其趕上之前他們就會通知您。
1. 您會關閉使用災害復原網站中生產 HANA 執行個體的應用程式。 接著執行 HANA 交易記錄備份。 接下來，您可以停止正在災害復原網站中的 HANA 大型執行個體單位執行的 HANA 執行個體。
1. 執行災害復原網站中 HANA 大型執行個體單位的 HANA 執行個體關閉之後，operations 小組手動同步處理的磁碟區一次。
1. SAP HANA on Azure 營運小組的 HANA 大型執行個體單位生產網站中重新啟動。 它們它交付給您。 您確定 SAP HANA 執行個體是處於關閉狀態在 HANA 大型執行個體單位的啟動時間。
1. 您執行您先前容錯移轉至災害復原站台時的相同資料庫還原步驟。

## <a name="monitor-disaster-recovery-replication"></a>監視災害復原複寫

若要監視儲存體複寫進度的狀態，請執行指令碼`azure_hana_replication_status`。 此命令必須執行單位執行的災害復原位置，如預期般運作。 此命令適用於無論複寫是否作用中。 命令可以執行您的租用戶，災害復原位置中的每個 HANA 大型執行個體單位。 它不能用來取得開機磁碟區的相關詳細資料。 

如需有關命令和其輸出的詳細資訊，請參閱 「 取得 DR 複寫狀態-azure_hana_replication_status 」，在[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。


## <a name="next-steps"></a>後續步驟
- 請參閱[監視和疑難排解從 HANA 端](hana-monitor-troubleshoot.md)。
