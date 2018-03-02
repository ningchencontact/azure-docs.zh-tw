---
title: " 刪除 Azure 中的復原服務保存庫 | Microsoft Docs "
description: "本文說明如何刪除復原服務保存庫。 本文包含當您嘗試刪除保存庫卻無法做到時的疑難排解步驟。"
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫
本文說明如何在 Azure 入口網站中刪除復原服務保存庫。 您先前擁有的備份保存庫已轉換為復原服務保存庫。   

刪除復原服務保存庫是單一步驟的程序 - 前提是保存庫不包含任何資源 。 您必須先移除或刪除復原服務保存庫中的所有資源，才可以刪除復原服務保存庫。 如果您嘗試刪除包含資源的保存庫，會收到類似下圖的錯誤：

![保存庫刪除錯誤](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

按 [重試]  會再產生相同的錯誤，直到您清除保存庫中的資源為止。 如果您被卡在這則錯誤訊息，請按一下 [取消]，並使用下列步驟刪除保存庫中的資源。

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>移除保護 VM 之保存庫中的項目
如果您已開啟復原服務保存庫，請跳至第 2 步驟。

1. 開啟 Azure 入口網站，從儀表板中開啟您要刪除的保存庫。

   如果儀表板中尚未釘選復原服務保存庫，請按一下 [中樞] 功能表上的 [更多服務]，然後在資源清單中輸入**復原服務**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 按一下 [復原服務保存庫] 。

   ![建立復原服務保存庫的步驟 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   隨即會顯示 [復原服務保存庫] 清單。 從清單中，選取您要刪除的保存庫。

   ![從清單中選擇保存庫](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. 在保存庫檢視中，找到 [基本]  窗格。 若要刪除保存庫，不能有任何受保護的項目。 如果 [備份項目] 或 [備份管理伺服器] 未顯示為零，您就必須移除這些項目。 您無法刪除內含資料的保存庫。

    ![在基本窗格中查看受保護的項目](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    VM 和檔案/資料夾會被視為備份項目，列在 [基本] 窗格的 [備份項目]  區域中。 DPM 伺服器會列在 [基本] 窗格的 [備份管理伺服器]  區域中。  屬於 Azure Site Recovery 服務。
3. 若要開始從保存庫移除受保護的項目，請在保存庫中找到這些項目。 在保存庫儀表板中，按一下 [設定]，然後按一下 [備份項目] 來開啟該功能表。

    ![從清單中選擇保存庫](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    [備份項目] 功能表中有不同的清單，取決於項目類型︰Azure 虛擬機器或檔案-資料夾 (請參閱圖片)。 顯示的預設項目清單類型是 Azure 虛擬機器。 若要檢視保存庫中的檔案資料夾項目清單，請從下拉式選單中選取 [檔案-資料夾]  。
4. 您可以從保護 VM 的保存庫中刪除項目之前，必須先停止該項目的備份作業，並刪除復原點資料。 針對保存庫中的每個項目，執行下列步驟︰

    a. 在 [備份項目] 功能表中，以滑鼠右鍵按一下項目，然後從內容功能表中選取 [停止備份]。

    ![停止備份作業](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    [停止備份] 功能表隨即開啟。

    b. 在 [停止備份] 功能表中，從 [選擇選項] 功能表中選取 [刪除備份資料]，輸入項目的名稱，然後按一下 [停止備份]。

    輸入項目的名稱，以確認您想要刪除它。 一旦您確認項目之後，[停止備份] 按鈕會生效。 如果看不到可輸入備份項目名稱的對話方塊，表示您選擇了 [保留備份資料] 選項。

    ![刪除備份資料](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    或者，您可以提供您為什麼要刪除資料的原因，並加入註解。 按一下 [停止備份] 之後，允許刪除作業完成，然後再嘗試刪除保存庫。 若要確認作業已完成，請檢查 Azure 訊息 ![delete backup data](./media/backup-azure-delete-vault/messages.png)。 <br/>
    作業完成之後，服務會傳送一則訊息：備份程序已停止，並已刪除備份資料。

    c. 刪除清單中的項目後，在 [備份項目] 功能表上按一下 [重新整理]，以查看保存庫中的其餘項目。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)

      當清單中沒有任何項目時，捲動到復原服務保存庫功能表中的 [基本資訊] 窗格。 清單中不應該有任何 [備份項目]、[備份管理伺服器] 或 [複寫的項目]。 如果保存庫中仍有項目，請返回步驟三，並選擇其他項目類型清單。  
5. 當保存庫工具列中沒有其他項目，按一下 [刪除] 。

    ![刪除備份資料](./media/backup-azure-delete-vault/delete-vault.png)
6. 若要確認您想要刪除保存庫，請按一下 [是]。

    保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>如果我停止備份程序但卻保留了資料，會怎麼樣？
如果您停止備份程序，但是不小心「保留」  資料，您必須先刪除備份資料，才能刪除保存庫。 刪除備份資料：

1. 在 [備份項目] 功能表中，以滑鼠右鍵按一下項目，然後在內容功能表中按一下 [刪除備份資料]。

    ![刪除備份資料](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    [刪除備份資料] 功能表隨即開啟。
2. 在 [刪除備份資料] 功能表中，輸入項目的名稱，然後按一下 [刪除]。

    ![刪除備份資料](./media/backup-azure-delete-vault/delete-retained-vault.png)

    刪除資料之後，請返回步驟 4c，並繼續進行程序。

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>刪除用來保護 DPM 伺服器的保存庫
在刪除用來保護 DPM 伺服器的保存庫之前，您必須先清除所有已建立的復原點，然後從保存庫取消註冊該伺服器。

刪除與保護群組相關聯的資料︰

1. 在 DPM 管理主控台中，按一下 [保護] > 選取保護群組 > 選取 [保護群組成員]，然後按一下工具功能區中的 [移除]。

  選取 [保護群組成員]，以啟用工具功能區中的 [移除] 按鈕。 在範例中，成員是 **dummyvm9**。 若要選取保護群組中的多個成員，請按住 Ctrl 鍵並在成員上按一下。

    ![刪除備份資料](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    [停止保護]  對話方塊隨即開啟。
2. 在 [停止保護] 對話方塊中，選取 [刪除受保護的資料]，然後按一下 [停止保護]。

    ![刪除備份資料](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    若要刪除保存庫，您必須清除 (或刪除) 受保護資料的保存庫。 如果保護群組中有許多復原點和資料，刪除資料可能需要幾分鐘的時間。 作業完成時，便會顯示 [停止保護] 對話方塊。

    ![刪除備份資料](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. 對所有保護群組中的所有成員執行此程序。

    移除所有受保護的資料與保護群組。
4. 刪除保護群組中的所有成員後，移至 Azure 入口網站。 開啟保存庫儀表板，確認已沒有任何 [備份項目]、[備份管理伺服器] 或 [複寫的項目]。 在保存庫工具列上，按一下 [刪除] 。

    ![刪除備份資料](./media/backup-azure-delete-vault/delete-vault.png)

    如果有備份管理伺服器註冊到保存庫，您將無法刪除該保存庫，即使保存庫中沒有資料也一樣。 如果您已經刪除與保存庫相關聯的備份管理伺服器，但 [基本] 窗格中仍列出伺服器，請參閱[尋找註冊到保存庫的備份管理伺服器](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault)。
5. 若要確認您想要刪除保存庫，請按一下 [是]。

    保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>刪除用來保護生產伺服器的保存庫
在刪除用來保護生產伺服器的保存庫之前，您必須先刪除或取消註冊保存庫中的伺服器。

刪除與保存庫相關聯的生產伺服器︰

1. 在 Azure 入口網站中，開啟保存庫儀表板，然後按一下 [設定]  >  > 。

    ![開啟生產伺服器功能表](./media/backup-azure-delete-vault/delete-production-server.png)

    [生產伺服器] 功能表隨即開啟，並列出保存庫中所有的生產伺服器。

    ![生產伺服器的清單](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. 在 [生產伺服器] 功能表中，以滑鼠右鍵按一下伺服器，然後按一下 [刪除]。

    ![刪除生產伺服器 ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    [刪除] 功能表隨即開啟。

    ![刪除生產伺服器 ](./media/backup-azure-delete-vault/delete-blade.png)
3. 在 [刪除] 功能表上，確認伺服器名稱，然後按一下 [刪除]。 您必須正確命名伺服器，[刪除] 按鈕才能生效。

    一旦刪除保存庫，您會收到訊息，指出已刪除保存庫。 刪除保存庫中的所有伺服器之後，往回捲動至保存庫儀表板中的 [基本] 窗格。
4. 在保存庫儀表板中，確認已沒有任何 [備份項目]、[備份管理伺服器] 或 [複寫的項目]。 在保存庫工具列上，按一下 [刪除] 。
5. 若要確認您想要刪除保存庫，請按一下 [是]。

    保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>尋找註冊到保存庫的備份管理伺服器
如果您註冊多部伺服器至保存庫，可能很難全部記住。 查看您註冊至保存庫的伺服器，並加以刪除︰

1. 開啟保存庫儀表板。
2. 在 [基本] 窗格中，按一下 [設定] 以開啟該功能表。

    ![開啟設定功能表](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. 在 [設定] 功能表上，按一下 [備份基礎結構]。
4. 在 [備份基礎結構] 功能表中，按一下 [備份管理伺服器]。 [備份管理伺服器] 功能表隨即開啟。

    ![備份管理伺服器的清單](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. 若要刪除清單中的伺服器，以滑鼠右鍵按一下伺服器的名稱，然後按一下 [刪除] 。
    [刪除] 功能表隨即開啟。
6. 在 [刪除]  功能表中，提供伺服器的名稱。 如果名稱很長，您可以從的備份管理伺服器的清單中複製並貼上。 然後按一下 [刪除] 。  
