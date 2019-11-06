---
title: 管理 & 監視器 Microsoft Azure 復原服務代理程式備份
description: 瞭解如何使用 Azure 備份服務來管理和監視 Microsoft Azure 復原服務代理程式備份。
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: dacurwin
ms.openlocfilehash: 68c991396c92e6f288d1cc17d6b7dcf1ec79343f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165481"
---
# <a name="manage-microsoft-azure-recovery-services-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 備份服務管理 Microsoft Azure 復原服務代理程式備份

本文說明如何管理使用 Microsoft Azure 復原服務代理程式備份的檔案和資料夾。

## <a name="create-a-backup-policy"></a>建立備份原則

備份原則會指定何時製作資料的快照集，以建立復原點，以及保留復原點的時間長度。 您可以使用 MARS 代理程式來設定備份原則。

建立原則，如下所示：

1. 下載並註冊 MARS 代理程式之後，請啟動代理程式主控台。 您可以透過在您的電腦中搜尋 **Microsoft Azure 備份**來找出備份。  
2. 在 [**動作**] 中，按一下 [**排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)
3. 在 排程備份 中 >**開始**使用，按**下一步**。
4. 在 [**選取要備份的專案**] 中，按一下 [**新增專案**]。

    ![選取要備份的專案](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. 在 [**選取專案**] 中，選取您想要備份的內容，然後按一下 **[確定]** 。

    ![選取要備份的專案](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. 在 [**選取要備份的專案**] 頁面中，按 **[下一步]** 。
7. 在 [**指定備份排程**] 頁面中，指定您要進行每日或每週備份的時間。 然後按 [下一步]。

    - 執行備份時，會建立復原點。
    - 在您的環境中建立的復原點數目取決於您的備份排程。

8. 您可以排程每日備份（一天最多三次）。 例如，螢幕擷取畫面顯示兩個每日備份，一個在午夜，另一個在下午6:00。

    ![每日排程](./media/backup-configure-vault/day-schedule.png)

9. 您也可以執行每週備份。 例如，螢幕擷取畫面顯示每個替代星期日 & 星期三上午9:30 和 1:00 AM 進行的備份。

    ![每週排程](./media/backup-configure-vault/week-schedule.png)

10. 在 [**選取保留原則**] 頁面上，指定儲存資料歷程記錄複本的方式。 然後按 [下一步]。

    - 保留設定會指定應該儲存的復原點，以及儲存的時間長度。
    - 例如，當您設定每日保留設定時，您會指出在指定的每日保留期間，最新的復原點會保留指定的天數。 或者，另一個範例是，您可以指定每月的保留原則，表示每個月30日所建立的復原點應該儲存12個月。
    - 每日和每週復原點保留通常會符合備份排程。 這表示當根據排程觸發備份時，備份所建立的復原點會儲存在每日或每週保留原則中指出的持續時間。
    - 例如，在下列螢幕擷取畫面中：-午夜和下午6:00 的每日備份會保留七天。
            -在星期六午夜和下午6:00 所進行的備份會保留四周。
            -在每個月午夜和下午6:00 的星期六進行的備份會保留12個月。
            -3 月最後一周的星期六所進行的備份會保留10年。

           ![Retention example](./media/backup-configure-vault/retention-example.png)

11. 在 **[選擇初始備份類型**] 中，決定您是否要透過網路進行初始備份，或使用離線備份（如需離線備份的詳細資訊，請參閱這[篇文章](backup-azure-backup-import-export.md)）。 若要透過網路進行初始備份，請選取 **[自動透過網路**]，然後按 **[下一步]** 。

    ![初始備份類型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. 在 [**確認**] 中，檢查資訊，然後按一下 **[完成]** 。
    ![確認備份類型](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. 當精靈建立好備份排程時，請按一下 [關閉]。
  ![確認修改備份進程](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

您必須在安裝代理程式的每部電腦上建立原則。

## <a name="modify-a-backup-policy"></a>修改備份原則

當您修改備份原則時，可以加入新的專案、從備份中移除現有的專案，或排除檔案，使其無法使用排除設定進行備份。

- [**新增專案**] 僅使用此選項來新增要備份的新專案。 若要移除現有的專案，請使用 [**移除專案**] 或 [**排除設定**] 選項。  
- [**移除專案**] 使用此選項可移除要備份的專案。
  - 使用**排除設定**來移除磁片區中的所有專案，而不是**移除專案**。
  - 清除磁片區中的所有選取專案時，會在最後一次備份時，根據保留設定保留舊的專案備份，而不需要修改範圍。
  - 重新選取這些專案，會導致第一次完整備份，而新的原則變更不會套用到舊的備份。
  - 取消取消全磁片區會保留過去的備份，而不需要任何範圍來修改保留原則。
- **排除設定**使用此選項可排除要備份的特定專案。
  
### <a name="add-new-items-to-existing-policy"></a>將新專案新增至現有的原則

1. 在 [**動作**] 中，按一下 [**排程備份**]。

    ![Windows Server 備份排程](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 [**選取原則專案**] 索引標籤中，選取 [**修改檔案和資料夾的備份排程**] 並按 **[下一步]** 。

    ![選取原則專案](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 [**修改或停止排程備份**] 索引標籤中選取 [**變更備份專案或時間**]，然後按 **[下一步]** 。

    ![修改或排程備份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 [**選取要備份的專案**] 索引標籤中，按一下 [**新增專案**] 以新增您要備份的專案。

    ![修改或排程備份新增專案](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 [**選取專案**] 視窗中，選取要新增的 [飛出] 或 [資料夾]，然後按一下 **[確定]** 。

    ![選取專案](./media/backup-azure-manage-mars/select-item.png)

6. 完成後續步驟，然後按一下 **[完成]** 以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>將排除規則新增至現有的原則

您可以新增排除規則，以略過您不想要備份的檔案和資料夾。 在定義新原則或修改現有原則時，您可以執行此動作。

1. 按一下 [動作] 窗格中的 [**排程備份**]。 移至 [**選取要備份的專案**]，然後按一下 [**排除設定**]。

    ![選取專案](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在 [**排除設定**] 中，按一下 [**新增排除**]。

    ![選取專案](./media/backup-azure-manage-mars/add-exclusion.png)

3. 從 [**選取要排除的專案**] 中，流覽檔案和資料夾，並選取您要排除的專案，然後按一下 **[確定]** 。

    ![選取專案](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 預設會排除所選資料夾內的所有**子資料夾**。 您可以選取 **[是]** 或 [**否**] 加以變更。 您可以編輯並指定要排除的檔案類型，如下所示：

    ![選取專案](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成後續步驟，然後按一下 **[完成]** 以完成操作。

### <a name="remove-items-from-existing-policy"></a>從現有的原則移除專案

1. 按一下 [動作] 窗格中的 [**排程備份**]。 移至 [**選取要備份的專案**]。 從清單中，選取您想要從備份排程中移除的檔案和資料夾，然後按一下 [**移除專案**]。

    ![選取專案](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 當您從原則中完全移除磁片區時，請務必小心。  如果您需要再次新增，則會將它視為新的磁片區。 下一個排定的備份將會執行初始備份（完整備份），而不是增量備份。 如果您稍後需要暫時移除並新增專案，建議使用**排除設定**而不是**移除專案**，以確保增量備份，而不是完整備份。

2. 完成後續步驟，然後按一下 **[完成]** 以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保護檔案和資料夾備份

有兩種方式可停止保護檔案和資料夾備份：

- **停止保護並保留備份資料**。
  - 此選項會停止所有未來的備份作業，使其無法進行保護。
  - Azure 備份服務會保留已根據保留原則備份的復原點。
  - 您將能夠還原未過期復原點的已備份資料。
  - 如果您決定繼續保護，則可以使用 [*重新啟用備份排程*] 選項。 之後，資料會根據新的保留原則加以保留。
- **停止保護並刪除備份資料**。
  - 此選項會停止所有未來的備份工作，保護您的資料並刪除所有復原點。
  - 您將會收到一封「刪除備份資料警示」電子郵件，其中包含已*刪除此備份專案的資料。此資料將暫時可供使用14天，之後將會永久刪除*，並在*14 天內重新保護備份專案，以復原您的資料。*
  - 若要繼續保護，請在14天內從刪除作業進行重新保護。

### <a name="stop-protection-and-retain-backup-data"></a>停止保護並保留備份資料

1. 開啟 MARS 管理主控台，移至 [**動作] 窗格**，然後**選取 [排程備份**]。
    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 [**選取原則專案**] 頁面中，選取 [**修改檔案和資料夾的備份排程** **] 按 [下一步]** 。
    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 從 [**修改或停止排定的備份**] 頁面，選取 [**停止使用此備份排程]，但保留已儲存的備份，直到排程重新開機為止**。 然後，選取 [下一步]。  
    ![修改或停止排定的備份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在 [**暫停排定的備份**] 中，按一下 **[完成]** ![修改或停止排定的備份中的資訊。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 [**修改備份**程式] 中，檢查您的排程備份暫停狀態成功，然後按一下 [**關閉**] 以完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保護並刪除備份資料

1. 開啟 MARS 管理主控台，移至 [**動作**] 窗格，然後選取 [**排程備份**]。
2. 從 [**修改或停止排程備份**] 頁面，選取 [**停止使用此備份排程] 並刪除所有儲存的備份**。 然後，選取 [下一步]。

    ![修改或停止排定的備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 從 [**停止排程的備份**] 頁面，選取 **[完成]** 。

    ![停止排程的備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統會提示您輸入您必須手動產生的安全性 PIN 碼（個人識別碼）。 若要這麼做，請先登入 Azure 入口網站。
5. 移至 復原**服務保存庫** > **設定** > **屬性**。
6. 在 [**安全性 PIN**] 底下，選取 [**產生**]。 複製此 PIN。 PIN 僅適用于五分鐘。
7. 在管理主控台中，貼上 PIN，然後選取 **[確定]** 。

    ![產生安全性 PIN 碼。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 [**修改備份進度**] 頁面中，會出現下列訊息：*已刪除的備份資料將會保留14天。在這段時間之後，備份資料將會永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

## <a name="re-enable-protection"></a>重新啟用保護

如果您在保留資料時停止保護，並決定繼續保護，則可以使用 [修改備份原則] 重新啟用備份排程。

1. 在 [**動作**] 選取 [**排程備份**]。
1. 選取 [**重新啟用備份排程]。您也可以修改備份專案或 tines** ，然後按 **[下一步]** 。
    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 [**選取要備份的專案**] 中，按 **[下一步]** 。
    ![刪除備份基礎結構。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 [**指定備份排程**] 中，指定備份排程，然後按 **[下一步]** 。
1. 在 [**選取保留原則**] 中，指定保留期間，然後按 **[下一步]** 。
1. 最後，在 [**確認**] 畫面中，檢查原則詳細資料，然後按一下 **[完成]** 。

## <a name="next-steps"></a>後續步驟

- 如需有關支援的案例和限制的詳細資訊，請參閱[MARS 的支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 深入瞭解[特定備份原則保留行為](https://docs.microsoft.com/azure/backup/backup-configure-vault#ad-hoc-backup-policy-retention-behavior.md)。
