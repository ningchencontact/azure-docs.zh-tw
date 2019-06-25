---
title: Azure 資料箱磁碟疑難排解資料複製問題 |Microsoft Docs
description: 描述如何針對 Azure 資料箱磁碟使用記錄檔中的資料複製期間看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148344"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>針對 Azure 資料箱磁碟中的資料複製問題進行疑難排解

這篇文章適用於 Microsoft Azure 資料箱磁碟，並說明如何將資料複製到磁碟時，您會看到任何問題進行疑難排解。 使用分割複製工具時，本文也涵蓋的問題。


## <a name="data-copy-issues-when-using-a-linux-system"></a>使用 Linux 系統時，資料會複製問題

本節將詳細說明一些使用 Linux 用戶端將資料複製到磁碟時所面臨的常見問題。

### <a name="issue-drive-getting-mounted-as-read-only"></a>問題：磁碟機以唯讀模式掛接
 
**原因** 

原因可能是未整理的檔案系統。

將磁碟機重新掛接為讀寫不適用於資料箱磁碟。 此情節不適用於由 dislocker 解密的磁碟機。 您可能已經使用下列命令成功重新掛接磁碟機：

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

雖然已經成功重新掛接，但資料不會保存。

**解決方案**

執行下列步驟在您的 Linux 系統上：

1. 安裝`ntfsprogs`ntfsfix 公用程式的套件。
2. 取消掛接後解除鎖定工具所提供的磁碟機的掛接點。 磁碟機而異的掛接點數目。

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 執行`ntfsfix`上對應的路徑。 反白顯示的數字應該與步驟 2 相同。

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 執行下列命令來移除可能會造成連接問題的休眠中繼資料。

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. 執行全新的卸載。

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. 執行全新的解除鎖定，並掛接。
7. 寫入檔案，以測試掛接點。
8. 卸載並重新掛接，以驗證檔案持續性。
9. 繼續進行複製的資料。
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>問題：複製之後資料沒有保留的錯誤
 
**原因** 

如果您發現磁碟機卸載之後沒有資料 (雖然資料有複製到其中)，則您可能在磁碟機掛接為唯讀之後，將它重新掛接為讀寫。

**解決方案**
 
如果是此情況，請參閱[磁碟機掛接為唯讀](#issue-drive-getting-mounted-as-read-only)的解決方法。

如果不是此情況，請複製有資料箱磁碟解除鎖定工具的資料夾之中的記錄，並[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。


## <a name="data-box-disk-split-copy-tool-errors"></a>資料箱磁碟分割複製工具錯誤

下表摘要說明使用的分割複製工具，透過多個磁碟分割資料時的問題。

|錯誤訊息/警告 |建議 |
|---------|---------|
|[資訊]擷取磁碟區的 BitLocker 密碼： m <br>[Error]擷取磁碟區 m： 的 BitLocker 金鑰時攔截到例外狀況<br> 序列未包含項目。|如果目的地資料箱磁碟處於離線狀態，就會擲回這個錯誤。 <br> 對線上磁碟使用 `diskmgmt.msc` 工具。|
|[錯誤] 擲回例外狀況：WMI 作業失敗:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=所提供的復原密碼格式無效。 <br>BitLocker 修復原密碼是 48 位數字。 <br>請確認復原密碼的格式正確，然後再試一次。|先使用資料箱磁碟解除鎖定工具將磁碟解除鎖定，然後重試命令。 如需詳細資訊，請移至 <li> [解除鎖定適用於 Windows 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)。 </li><li> [解除鎖定適用於 Linux 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。 </li>|
|[錯誤] 擲回例外狀況：存在於目標磁碟機上的 DriveManifest.xml 檔案。 <br> 這表示目標磁碟機可能已透過不同的日誌檔案來準備。 <br>若要將更多資料新增至相同磁碟機，請使用先前的日誌檔案。 若要刪除現有的資料，並重複使用新的匯入作業的目標磁碟機，請刪除*DriveManifest.xml*磁碟機上。 以新的日誌檔案重新執行此命令。| 當您嘗試對多個匯入工作階段使用同一組磁碟機時，就會收到這個錯誤。 <br> 一組磁碟機僅適用於一個分割及複製工作階段。|
|[錯誤] 擲回例外狀況：CopySessionId importdata-sept-test-1 參考前一個複製工作階段，而且無法用於新的複製工作階段。|如果嘗試使用前一個已成功完成的作業名稱來作為新作業的名稱，就會回報此錯誤。<br> 請為您的新作業指派唯一名稱。|
|[資訊] 目的地檔案或目錄名稱超過 NTFS 長度限制。 |當目的檔案因為檔案路徑太長而重新命名時，就會回報此訊息。<br> 請修改 `config.json` 檔案中的配置選項，以控制此行為。|
|[錯誤] 擲回例外狀況：錯誤的 JSON 逸出序列。 |當 config.json 的格式無效時，就會回報此訊息。 <br> 請在儲存檔案之前，先確認您的 `config.json` 是使用 [JSONlint](https://jsonlint.com/)。|


## <a name="next-steps"></a>後續步驟

- 了解如何[驗證工具進行疑難排解](data-box-disk-troubleshoot.md)。
