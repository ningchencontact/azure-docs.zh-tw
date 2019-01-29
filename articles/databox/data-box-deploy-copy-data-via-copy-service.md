---
title: 透過 SMB 將資料複製到您的 Microsoft Azure 資料箱 | Microsoft Docs
description: 了解如何透過資料複製服務將資料複製到 Azure 資料箱
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: a71635abd036bb89546dd3421af97cd9b88f4327
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54439884"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>教學課程：使用資料複製服務直接將資料擷取至 Azure 資料箱 (預覽)

本教學課程說明如何使用資料複製服務擷取資料，而不需要中繼主機。 資料複製服務會在本機資料箱上執行、透過 SMB 連線到您的 NAS 裝置，以及將資料複製到資料箱。

使用資料複製服務：

- 在網路連接儲存裝置 (NAS) 環境中，中繼主機可能無法使用。
- 搭配需要數週時間來擷取和上傳資料的小型檔案。 此服務會大幅改善擷取和上傳時間。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 將資料複製到資料箱
> * 準備寄送資料箱。

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已完成[教學課程：設定 Azure 資料箱](data-box-deploy-set-up.md)。
2. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]。
3. 您有所連線來源 NAS 裝置的認證可進行資料複製。
4. 您已連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，請使用 1 GbE 資料連結，但是複製速度會受到影響。

## <a name="copy-data-to-data-box"></a>將資料複製到資料箱

連線至 NAS 後，下一個步驟是複製資料。 開始複製資料之前，請檢閱下列注意事項：

- 複製資料時，請確定資料大小符合 [Azure 儲存體和資料箱限制](data-box-limits.md)中所述的大小限制。
- 如果由資料箱上傳的資料同時由資料箱以外的其他應用程式上傳，則可能導致上傳作業失敗和資料損毀。
- 如果資料因為資料複製服務正在讀取而變換，您會看到錯誤或資料損毀。

若要使用資料複製服務來複製資料，您需要建立作業。 請遵循下列步驟來建立用以複製資料的作業。

1. 在您資料箱的本機 Web UI 中，移至 [管理] > [複製資料]。
2. 在 [複製資料] 頁面上，按一下 [建立]。

    ![按一下 [複製資料] 頁面上的 [建立]](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. 在 [設定並啟動] 對話方塊中，提供下列輸入。
    
    |欄位                          |值    |
    |-------------------------------|---------|
    |作業名稱                       |作業的唯一名稱超過 230 個字元。 作業名稱中不允許以下字元：\<、\>、\|、\?、\*、\\、\:、\/ 及 \\\.         |
    |來源位置                |以下列格式提供資料來源的 SMB 路徑：`\\<ServerIPAddress>\<ShareName>` 或 `\\<ServerName>\<ShareName>`。        |
    |使用者名稱                       |用以存取資料來源的使用者名稱。        |
    |密碼                       |用以存取資料來源的密碼。           |
    |目的地儲存體帳戶    |從下拉式清單中選取要上傳資料的目標儲存體帳戶。         |
    |目的地儲存體帳戶       |從區塊 Blob、分頁 Blob 或 Azure 檔案儲存體中選取目標儲存體類型。        |
    |目的地容器/共用    |輸入容器或共用名稱，以上傳目的地儲存體帳戶中的資料。 此名稱可以是共用名稱或容器名稱。 例如，`myshare` 或 `mycontainer`。 您也可以使用雲端中的 `sharename\directory_name` 或 `containername\virtual_directory_name` 格式輸入。        |
    |複製符合模式的檔案    | 以下列兩種方式輸入檔案名稱比對模式。<ul><li>**使用萬用字元運算式** 萬用字元運算式僅支援 `*` 和 `?`。 例如，這個運算式 `*.vhd` 會比對具有 .vhd 副檔名的所有檔案。 同樣地，`*.dl?` 會比對其副檔名為 `.dl` 或 `.dll` 的所有檔案。 此外，`*foo` 會比對其名稱結尾為 `foo` 的所有檔案。<br>您可以在欄位中直接輸入萬用字元運算式。 根據預設，在此欄位中輸入的值會被視為萬用字元運算式。</li><li>**使用規則運算式** - 支援 POSIX 型規則運算式。 例如，規則運算式 `.*\.vhd` 會比對具有 `.vhd` 副檔名的所有檔案。 針對規則運算式，直接提供 `<pattern>` 作為 `regex(<pattern>)`。 <li>如需有關規則運算式的詳細資訊，請移至[規則運算式語言 - 快速參考](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)。</li><ul>|
    |檔案最佳化              |若已啟用，檔案就會在擷取時封裝。 這可加快小型檔案的資料複製。        |
 
4. 按一下 [啟動] 。 輸入會經過驗證，如果驗證成功，則會啟動作業。 系統需要花幾分鐘的時間來啟動作業。

    ![從 [設定作業並啟動] 對話方塊啟動作業](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. 使用指定的設定來建立作業。 選取核取方塊，即可暫停和繼續、取消或重新啟動作業。

    ![透過複製資料頁面管理作業](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - 如果作業在尖峰時段影響 NAS 資源，您可以暫停此作業。

        ![暫停作業](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        您可以稍後在非尖峰時段繼續作業。

        ![繼續作業](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - 您可以隨時取消作業。

        ![取消作業](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) 當您取消作業時需要確認。

        ![確認作業取消](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        如果您決定要取消作業，則不會刪除已經複製的資料。 若要刪除您在資料箱上複製的資料，請將裝置重設。

        ![重設裝置](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > 如果您取消或暫停作業，正在複製的大型檔案就可能複製一半。 這些檔案會以相同的狀態上傳至 Azure。 嘗試取消或暫停時，請驗證您的檔案是否正確複製。 若要驗證檔案，請查看 SMB 共用或下載 BOM 檔案。

    - 如果作業因為暫時性錯誤 (例如網路故障) 而突然失敗，您可以重新啟動作業。 如果作業已達終極狀態 (例如順利完成或完成但發生錯誤)，您就無法重新啟動作業。 有可能由於檔案命名或檔案大小問題而失敗。 系統會記錄這些錯誤，但作業一旦完成就無法重新啟動。

        ![重新啟動失敗的作業](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        如果您遇到錯誤且無法重新啟動作業，請下載錯誤記錄並查看記錄檔中的錯誤。 在您更正問題之後，您可以建立新的作業來複製檔案。 您也可以[透過 SMB 複製檔案](data-box-deploy-copy-data.md)。
    
    - 在這個版本中，您無法刪除作業。
    
    - 您可以建立無限制的作業，但是在任何指定的時間最多平行執行 10 個作業。
    - 若已開啟檔案最佳化，小型檔案會在擷取時封裝以改善複製效能。 在這些情況下，您會看到如以下螢幕擷取畫面所示的封裝檔案 (GUID 作為名稱)。

        ![封裝的檔案範例](media/data-box-deploy-copy-data-via-copy-service/packed-file-on-ingest.png)

6. 當作業正在進行時，在 [複製資料] 頁面上：

    - 在 [狀態] 資料行中，您可以檢視複製作業的狀態。 狀態可以是：
        - **執行中**
        - **已失敗**
        - **已成功**
        - **暫停中**
        - **已暫停**
        - **取消中**
        - **Canceled**
        - **已完成但發生錯誤**
    - 在 [檔案] 資料行中，您可以看到正在複製的檔案總數和大小。
    - 在 [已處理] 資料行中，您可以看到已處理的檔案數目和大小。
    - 在 [詳細資料] 資料行中，按一下 [檢視] 以查看作業詳細資料。
    - 如果您在複製過程中遇到任何錯誤 (如 [錯誤數目] 資料行所示)，請移至 [錯誤記錄] 資料行並下載錯誤記錄，以進行疑難排解。

等待複製作業完成。 因為有些錯誤只會記錄在 [連線並複製] 頁面中，請先確定複製作業已完成且未出現任何錯誤，再進行下一個步驟。

![[連線並複製] 頁面上沒有錯誤](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

為了確保資料完整性，在複製資料時會計算內嵌的總和檢查碼。 複製完成後，請確認您的裝置上已使用的空間和可用空間。
    
![確認儀表板上的可用空間和已使用的空間](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

複製作業完成後，您就可以移至 [準備寄送]。

>[!NOTE]
> 複製作業正在進行時，無法執行寄送準備。

## <a name="prepare-to-ship"></a>準備寄送

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * 將資料複製到資料箱
> * 準備寄送資料箱

請繼續進行下一個教學課程，了解如何將資料箱送回給 Microsoft。

> [!div class="nextstepaction"]
> [將您的 Azure 資料箱寄送給 Microsoft](./data-box-deploy-picked-up.md)

