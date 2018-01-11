---
title: "針對 Azure 備份伺服器進行疑難排解 | Microsoft Docs"
description: "疑難排解安裝註冊 Azure 備份伺服器和備份和還原應用程式工作負載。"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: e9517672138a4ea7577af1295dea13771733983e
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="troubleshoot-azure-backup-server"></a>針對 Azure 備份伺服器進行疑難排解

使用下表中的資訊來疑難排解您使用 Azure 備份伺服器時所遇到的錯誤。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效 

若要解決此問題，請遵循[下列疑難排解步驟](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues)。

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>代理程式作業失敗，因為通訊發生錯誤，伺服器上的 DPM 代理程式協調器服務 

若要解決此問題，請遵循[下列疑難排解步驟](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues)。 

## <a name="setup-could-not-update-registry-metadata"></a>安裝程式無法更新登錄中繼資料

若要解決此問題，請遵循[下列疑難排解步驟](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues)。




## <a name="installation-issues"></a>安裝問題

| 作業 | 錯誤詳細資料 | 因應措施 |
|-----------|---------------|------------|
|安裝 | 安裝程式無法更新登錄中繼資料。 Overusage 的存放裝置耗用量可能導致更新失敗。 若要避免這個問題，更新的修剪 ReFS 登錄項目。 | 調整登錄機碼**SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**。 將 Dword 值設為 1。 |
|安裝 | 安裝程式無法更新登錄中繼資料。 Overusage 的存放裝置耗用量可能導致更新失敗。 若要避免這個問題，更新磁碟區 SnapOptimization 登錄項目。 | 建立登錄機碼**SOFTWARE\Microsoft 資料保護 Manager\Configuration\VolSnapOptimization\WriteIds**與空字串值。 |
## <a name="registration-and-agent-related-issues"></a>註冊和代理程式相關的問題
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 註冊至保存庫 | 提供的保存庫認證無效。 檔案已損毀或不具有最新相關聯的認證向復原服務。 | 若要修正此錯誤： <ul><li> 從保存庫下載最新的認證檔案重試一次。 <br>(或)</li> <li> 如果上一個動作未解決問題，請嘗試下載至不同的本機目錄的認證，或建立新的保存庫。 <br>(或)</li> <li> 請嘗試更新的日期和時間設定中所述[這篇部落格](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)。 <br>(或)</li> <li> 請檢查 c:\windows\temp 是否具有 65000 以上的檔案。 過時的檔案移至另一個位置，或刪除暫存資料夾中的項目。 <br>(或)</li> <li> 請檢查憑證的狀態。 <br> a. 開啟**管理的電腦憑證**（在 [控制台]）。 <br> b. 展開**個人**節點和它的子節點**憑證**。<br> c.  移除憑證**Windows Azure Tools**。 <br> d. 重試登錄 Azure 備份用戶端中。 <br> (或) </li> <li> 請檢查是否在發生任何群組原則。 </li></ul> |
| 將代理程式推送至受保護的伺服器 | 代理程式作業失敗，因為 DPM 代理程式協調員服務發生通訊錯誤上\<伺服器名稱 >。 | **如果會顯示在產品中的建議的動作無法運作，請執行下列步驟**: <ul><li> 如果您正在附加來自不受信任網域的電腦，請遵循[步驟](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)。 <br> (或) </li><li> 如果您將連接從受信任網域的電腦時，使用中概述的步驟疑難排解[這篇部落格](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)。 <br>(或)</li><li> 請嘗試停用防毒做為疑難排解步驟。 如果它可以解決此問題，請修改中建議的防毒軟體設定[本文](https://technet.microsoft.com/library/hh757911.aspx)。</li></ul> |
| 將代理程式推送至受保護的伺服器 | 指定給伺服器的認證不正確。 | **如果會顯示在產品中的建議的動作無法運作，請執行下列步驟**: <br> 嘗試中所指定的實際執行伺服器上手動安裝保護代理程式[本文](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)。|
| Azure 備份代理程式無法連線到 Azure 備份服務 (ID：100050) | Azure 備份代理程式無法連線到 Azure 備份服務。 | **如果會顯示在產品中的建議的動作無法運作，請執行下列步驟**: <br>1.從提升權限的提示字元執行下列命令： **psexec-i-s"c:\Program Files\Internet Explorer\iexplore.exe**。 這會開啟 Internet Explorer 視窗。 <br/> 2.移至**工具** > **網際網路選項** > **連線** > **LAN 設定**。 <br/> 3.請確認系統帳戶的 proxy 設定。 設定 Proxy IP 和連接埠。 <br/> 4.關閉 Internet Explorer。|
| Azure 備份代理程式安裝失敗 | Microsoft Azure 復原服務安裝失敗。 Microsoft Azure Recovery Services 安裝程式對系統所做的所有變更都已經都還原。 (ID：4024) | 手動安裝 Azure 代理程式。


## <a name="configuring-protection-group"></a>設定保護群組
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 設定保護群組 | DPM 無法列舉上受保護的應用程式元件的電腦 （受保護的電腦名稱）。 | 選取**重新整理**設定保護群組 UI 畫面，在相關的資料來源/元件層級上。 |
| 設定保護群組 | 無法設定保護 | 如果受保護的伺服器的 SQL server，請確認的 sysadmin 角色權限提供給受保護的電腦上的系統帳戶 ntauthority\system （新增） 中所述[本文](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)。
| 設定保護群組 | 此保護群組的存放集區沒有足夠的可用空間。 | 會新增到存放集區的磁碟[不應包含資料分割](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx)。 刪除磁碟上的任何現有磁碟區。 然後將它們加入到存放集區。|
| 原則變更 |無法修改備份原則。 錯誤：由於發生內部服務錯誤 [0x29834]，導致目前的作業失敗。 請經過一些時間之後, 重試操作。 如果此問題持續發生，請連絡 Microsoft 支援服務。 |**原因：**<br/>這個錯誤，就會發生下列三個條件： 啟用安全性設定，當您嘗試降低保留範圍最小的值之前，指定以下時，要在不支援的版本時。 （不支援的版本是指以下 Microsoft Azure 備份伺服器版本 2.0.9052 和 Azure 備份伺服器更新 1）。 <br/>**建議的動作：**<br/> 若要繼續與原則相關的更新，設定保留期限指定時段的最小保留上方。 （最小保留期限是每日、 四週的每週、 三週七天每月或一年的每年）。 <br><br>（選擇性） 另一個建議的方法是更新的備份代理程式和 Azure 備份伺服器，以利用安全性更新。 |

## <a name="backup"></a>Backup 
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| Backup  | 複本不一致 | 確認已開啟 [保護群組精靈] 中的自動一致性檢查選項。 複本不一致的問題和相關的建議的原因詳細資訊，請參閱 Microsoft TechNet 文章[複本不一致](https://technet.microsoft.com/library/cc161593.aspx)。<br> <ol><li> 如果系統狀態/BMR 備份，請確認 Windows Server Backup 已安裝的受保護的伺服器上。</li><li> 檢查 DPM/Microsoft Azure 備份伺服器上的 DPM 存放集區的空間相關的問題，並將所需的存放裝置配置。</li><li> 檢查受保護的伺服器上的磁碟區陰影複製服務的狀態。 如果是停用狀態，將它設定為手動啟動。 在伺服器上啟動服務。 然後返回 DPM/Microsoft Azure 備份伺服器主控台中，並開始一致性檢查的同步處理。</li></ol>|
| Backup  | 執行作業時發生未預期的錯誤。 裝置尚未就緒。 | **如果會顯示在產品中的建議的動作無法運作，請採取下列步驟：** <br> <ul><li>陰影複製儲存空間設定為無限制的保護群組中的項目，然後執行一致性檢查。<br></li> (或) <li>請嘗試刪除現有的保護群組，並建立多個新的群組。 每個新的保護群組中應有的個別項目。</li></ul> |
| Backup  | 如果您要備份系統狀態，請確認儲存系統狀態備份受保護的電腦上有足夠的可用空間。 | <ol><li>確認 Windows Server Backup 已安裝在受保護的電腦上。</li><li>確認系統狀態的受保護的電腦上有足夠的空間。 最簡單的方式，以確認這是移至受保護的電腦，開啟 Windows Server Backup，透過選項] 中按一下，然後選取 [BMR。 UI 則會告訴您需要多少空間時。 開啟**WSB** > **本機備份** > **備份排程** > **選取備份設定**  > **完整伺服器**（顯示大小）。 使用此大小進行驗證。</li></ol>
| Backup  | 線上復原點建立失敗 | 如果錯誤訊息指出 「 Windows Azure Backup Agent 無法建立所選的磁碟區的快照集 」 嘗試增加複本和復原點磁碟區的空間。
| Backup  | 線上復原點建立失敗 | 如果錯誤訊息指出 「 Windows Azure Backup Agent 無法連線到 OBEngine 服務 」，請確認 OBEngine 存在於清單中的電腦上執行服務。 如果 OBEngine 服務不在執行中，使用"net start OBEngine"命令來啟動 OBEngine 服務。
| Backup  | 線上復原點建立失敗 | 如果錯誤訊息指出「未設定此伺服器的加密複雜密碼。 請設定加密複雜密碼 」，請嘗試設定加密複雜密碼。 如果失敗，請採取下列步驟： <br> <ol><li>請確認有可用的位置。 這是在登錄中提及的位置**HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**，具有名稱**ScratchLocation**應存在。</li><li> 如果臨時位置存在，請嘗試重新註冊使用舊的複雜密碼。 *每當您設定加密複雜密碼，請將它儲存在安全的位置。*</li><ol>
| Backup  | BMR 的備份失敗 | 如果 BMR 大小很大，將某些應用程式檔案移到作業系統磁碟機，然後重試。 |
| Backup  | 重新保護新的 Microsoft Azure 備份伺服器上的 VMware VM 選項不會顯示為可用來加入。 | VMware 屬性會指向 Microsoft Azure 備份伺服器的舊、 已停用執行個體。 若要解決此問題︰<br><ol><li>在 VCenter （SC VMM 對等項目），請移至**摘要** 索引標籤，然後**自訂屬性**。</li>  <li>刪除舊的 Microsoft Azure 備份伺服器名稱，從**DPMServer**值。</li>  <li>返回新的 Microsoft Azure 備份伺服器，並修改的 pg。  選取後**重新整理** 按鈕，VM 會顯示為可用來加入至 保護 核取方塊。</li></ol> |
| Backup  | 存取檔案/共用資料夾時發生錯誤 | 請嘗試修改 TechNet 文章中建議的防毒軟體設定[DPM 伺服器上執行防毒軟體](https://technet.microsoft.com/library/hh757911.aspx)。|
| Backup  | VMware VM 的線上復原點建立工作失敗。 DPM 嘗試取得變更追蹤資訊時發生錯誤從 VMware。 ErrorCode - FileFaultFault (ID 33621 ) |  <ol><li> 在 VMware 上的 CTK 重設受影響的 Vm。</li> <li>請檢查該獨立的磁碟不在 VMware 上的位置。</li> <li>停止保護受影響的 Vm，然後重新保護與**重新整理** 按鈕。 </li><li>對於受影響的 Vm 執行的副本。</li></ol>|


## <a name="change-passphrase"></a>變更複雜密碼
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 變更複雜密碼 |安全性已輸入的 PIN 不正確。 請提供正確的安全性 pin 碼才能完成這項作業。 |**原因：**<br/> 這個錯誤發生於輸入無效或過期的安全性 pin 碼，而您執行重要作業 （例如變更複雜密碼）。 <br/>**建議的動作：**<br/> 若要完成此作業，您必須輸入有效的安全性 pin 碼。 若要取得 PIN，登入 Azure 入口網站，請移至 復原服務保存庫。 然後移至**設定** > **屬性** > **產生安全性 pin 碼**。 使用這個 PIN 變更的複雜密碼。 |
| 變更複雜密碼 |作業失敗。 識別碼：120002 |**原因：**<br/>當安全性設定已啟用，或當您嘗試變更的複雜密碼，當您使用不支援的版本時，就會發生此錯誤。<br/>**建議的動作：**<br/> 若要變更的複雜密碼，您必須先備份代理程式更新為最小版本，也就是 2.0.9052。 您也需要以 Azure 備份伺服器更新為最小值的 update 1，然後輸入有效的安全性 pin 碼。 若要取得 PIN，登入 Azure 入口網站，請移至 復原服務保存庫。 然後移至**設定** > **屬性** > **產生安全性 pin 碼**。 使用這個 PIN 變更的複雜密碼。 |


## <a name="configure-email-notifications"></a>設定電子郵件通知
| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 使用 Office 365 帳戶的電子郵件通知設定 |錯誤 ID: 2013年| **原因：**<br> 嘗試使用 Office 365 帳戶 <br>**建議的動作：**<ol><li> 首先要確定是在 Exchange 上 「 允許匿名轉送上接收連接器 」 為您的 DPM 伺服器已設定。 如需如何加以設定的詳細資訊，請參閱[接收連接器上允許匿名轉送](http://technet.microsoft.com/en-us/library/bb232021.aspx)TechNet 上。</li> <li> 如果您無法使用內部的 SMTP 轉送，而且需要使用 Office 365 伺服器設定，您可以設定為轉送 IIS。 將 DPM 伺服器設定為[轉送 office 365 使用 IIS SMTP](https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx)。<br><br> **重要事項：**務必使用user@domain.com格式和*不*網域 \ 使用者。<br><br><li>點因為 SMTP 伺服器使用本機伺服器名稱，則 DPM 連接埠 587。 然後指向它的電子郵件應該來自於使用者電子郵件。<li> 使用者名稱和密碼，在 [DPM SMTP 設定] 頁面上的應該是網域帳戶在網域上的 DPM。 </li><br> **請注意**： 當您要變更 SMTP 伺服器位址時，到新的設定進行變更，關閉 [設定] 方塊中，然後再重新開啟它以確保它會反映新的值。  只要變更，並測試可能不一定會導致新設定才會生效，因此測試這種方式是最佳的作法。<br><br>在此程序期間的任何時間，您可以清除這些設定關閉 DPM 主控台，並編輯下列登錄機碼： **HKLM\SOFTWARE\Microsoft\Microsoft 資料保護 Manager\Notification\<br/>刪除 SMTPPassword 和SMTPUserName 金鑰**。 您可以將它們加回 UI 時再次啟動。
