---
title: 使用 Azure Site Recovery 服務將 Azure IaaS VM 移到其他 Azure 區域 | Microsoft Docs
description: 使用 Azure Site Recovery，將 Azure IaaS VM 從一個 Azure 區域移至另一個區域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824453"
---
# <a name="move-azure-vms-to-another-region"></a>將 Azure VM 移動到另一個區域

在許多情況下，您都會想要在不同區域間移動現有的 Azure IaaS 虛擬機器，以改善現有 VM 的可靠性和可用性、改善管理能力，或因應控管的需求等等。 

本教學課程說明如何使用 Azure Site Recovery 將 Azure VM 移至其區域。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * [驗證必要條件](#verify-prerequisites)
> * [準備來源 VM](#prepare-the-source-vms)
> * [準備目標區域](#prepare-the-target-region)
> * [將資料複製到目標區域](#copy-data-to-the-target-region)
> * [測試組態](#test-the-configuration)
> * [執行移動](#perform-the-move-to-the-target-region-and-confirm)
> * [捨棄來源區域中的資源](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> 本文件將引導您在不同區域間依現狀移動 Azure VM；如果您的需求是要將 VM 移至可用性區域，藉以改善基礎結構的可用性，請參閱這裡的教學課程。

## <a name="verify-prerequisites"></a>驗證必要條件

- 確定要移出 VM 的來源 Azure 區域中有 Azure VM。
- 確認您選擇的[來源區域-目標區域組合受到支援](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)，並在目標區域做出明智的決策。
- 請確定您了解[情節架構和元件](azure-to-azure-architecture.md)。
- 檢閱[支援限制和需求](azure-to-azure-support-matrix.md)。
- 驗證帳戶權限：如果您剛建立免費的 Azure 帳戶，則您會是訂用帳戶的系統管理員。 如果您不是訂用帳戶系統管理員，請與系統管理員合作以指派您所需的權限。 若要為 VM 啟用複寫，繼而使用 Azure Site Recovery 將資料複製到目標，您必須具備：

    1. 在 Azure 資源中建立 VM 的權限。 「虛擬機器參與者」內建角色具有這些權限，包含：
        - 在所選取資源群組中建立 VM 的權限
        - 在所選取虛擬網路中建立 VM 的權限
        - 寫入所選取儲存體帳戶的權限

    2. 您也需要管理 Azure Site Recovery 作業的權限。 「Site Recovery 參與者」角色具有在復原服務保存庫中管理 Site Recovery 作業所需的所有權限。

## <a name="prepare-the-source-vms"></a>準備來源 VM

1. 檢查您要移動的 Azure VM 上具有所有最新的根憑證。 如果沒有最新的根憑證，則會因為安全性條件約束而無法啟用將資料複製到目標區域的功能。

    - 若為 Windows VM，請安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。
    - 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。
2. 請確定您未使用驗證 Proxy 來控制所要移動 VM 的網路連線能力。
3. 如果您嘗試移動的 VM 無法存取網際網路，且使用防火牆 Proxy 來控制輸出存取，請檢查[此處](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)的需求。
4. 識別並列出來源網路配置，以及您目前使用的所有資源 - 包括 (但不限於) 要驗證的負載平衡器、NSG、公用 IP 等等。

## <a name="prepare-the-target-region"></a>準備目標區域

1. 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 如有必要，您可以連絡支援人員以啟用所需的配額。

2. 確定您的訂用帳戶具有足夠的資源，可支援大小與來源 VM 相符的 VM。 如果您使用 Site Recovery 將資料複製到目標，它將會為目標 VM 挑選相同的大小或最接近的大小。

3. 務必為來源網路面配置中識別的每個元件建立目標資源。 務必要確定在完全移轉至目標區域後，您的 VM 具備您在來源中擁有的所有功能和特性。

    > [!NOTE]
    > 當您為來源 VM 啟用複寫時，Azure Site Recovery 會自動探索和建立虛擬網路與儲存體帳戶，或者，您也可以在啟用複寫的步驟中預先建立這些資源，並指派給 VM。 但任何其他資源 (如下所述) 都必須在目標區域中手動建立。

     請參閱下列文件，以根據來源 VM 組態建立您最常用的相關網路資源。

    - [網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [負載平衡器](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [公用 IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    如需其他網路元件的資訊，請參閱網路[文件](https://docs.microsoft.com/azure/#pivot=products&panel=network)。 

4. 如果您想要先測試組態再執行對目標區域的最終完全移轉，請在目標區域中手動[建立非生產網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 這對生產環境的干擾最少，建議使用此方法。
    
## <a name="copy-data-to-the-target-region"></a>將資料複製到目標區域
下列步驟將說明如何使用 Azure Site Recovery 將資料複製到目標區域。

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>在來源區域以外的任何區域中建立保存庫。

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]。
2. 按一下 [建立資源] > [管理工具] > [備份和 Site Recovery]。
3. 在 [名稱] 中，指定易記名稱 [ContosoVMVault]。 如果您有多個 訂用帳戶，請選取適當的訂用帳戶。
4. 建立資源群組 **ContosoRG**。
5. 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 在復原服務保存庫中，按一下 [概觀] > [ConsotoVMVault] > [+複寫]。
7. 在 [來源] 中，選取 [Azure]。
8. 在 [來源位置] 中，選取 VM 目前執行所在的來源 Azure 區域。
9. 選取 Resource Manager 部署模型。 然後，選取 [來源訂用帳戶] 和 [來源資源群組]。
10. 按一下 [確定]  來儲存設定。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>為 Azure VM 啟用複寫，並開始複製資料。

Site Recovery 會擷取與訂用帳戶和資源群組建立關聯的 VM 清單。

1. 在下一個步驟中： 選取您要移動的 VM。 然後按一下 [確定] 。
3. 在 [設定] 中，按一下 [災害復原]。
4. 在 [設定災害復原] >  [目標區域] 中，選取您要複寫至的目標區域。
5. 您可以選擇使用預設目標資源，或使用您預先建立的資源。
6. 按一下 [啟用複寫]。 這會開始一項作業來啟用 VM 的複寫。

    ![啟用複寫](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>測試組態


1. 瀏覽至保存庫，在 [設定] > [複寫的項目] 中按一下您要移至目標區域的虛擬機器，然後按一下 [+測試容錯移轉] 圖示。
2. 在 [測試容錯移轉] 中，選取要用於容錯移轉的復原點：

   - **最近處理**：將 VM 容錯移轉到 Site Recovery 服務所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
   - **最新應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - **自訂**：選取任何復原點。

3. 選取您要移入 Azure VM 的目標 Azure 虛擬網路，以測試組態。 

> [!IMPORTANT]
> 建議您使用個別的 Azure VM 網路來測試失敗，而不要使用您最終要移入 VM 的目標區域所採用的生產網路。

4. 若要開始測試移動，請按一下 [確定]。 若要追蹤進度，請按一下 VM 開啟其內容。 或者，您也可以按一下保存庫名稱中的 [測試容錯移轉] 作業 > [設定] > [作業] > [Site Recovery 作業]。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器] 中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
6. 如果您想要刪除測試移動過程中建立的 VM，請在複寫的項目上按一下 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存與測試相關聯的任何觀察。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>執行移至目標區域的作業，並加以確認。

1.  瀏覽至保存庫，在 [設定] > [複寫的項目] 中按一下虛擬機器，然後按一下 [容錯移轉]。
2. 在 [容錯移轉] 中，選取 [最新]。 
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)。 Site Recovery 嘗試先關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。 
4. 作業完成後，請確認 VM 如預期出現在目標 Azure 區域中。
5. 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM > [認可]。 如此，移至目標區域的程序即告完成。 等待認可作業完成。

## <a name="discard-the-resource-in-the-source-region"></a>捨棄來源區域中的資源 

1. 瀏覽至 VM。  按一下 [停用複寫]。  這會停止複製 VM 資料的程序。  

> [!IMPORTANT]
> 請務必執行上述步驟，以避免在移動之後產生 Site Recovery 複寫費用。

如果您不打算重複使用任何來源資源，請繼續進行下一組步驟。

1. 繼續刪除您在[準備來源 VM](#prepare-the-source-vms) 的步驟 4 中列出的來源區域中包含的所有相關網路資源 
2. 刪除來源區域中對應的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure VM 移到不同的 Azure 區域。 您現在可以設定已移動 VM 的災害復原。

> [!div class="nextstepaction"]
> [在移轉之後設定災害復原](azure-to-azure-quickstart.md)

