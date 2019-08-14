---
title: Azure 儲存體 Blob 的固定儲存體 | Microsoft Docs
description: Azure 儲存體為 Blob 物件儲存體提供 WORM (一次寫入，多次讀取) 支援，可讓使用者在指定的間隔內以不可清除、不可修改的狀態儲存資料。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 06e1d881a14367c579bd58ffae04dc0970eb041a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941942"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>在 Azure Blob 儲存體中儲存業務關鍵資料

Azure Blob 儲存體的固定儲存體可讓使用者以 WORM (一次寫入, 多次讀取) 狀態儲存業務關鍵資料物件。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。 在保留間隔的持續期間內, 可以建立和讀取 Blob 物件, 但不能加以修改或刪除。 在所有 Azure 區域中, 一般用途 v2 和 Blob 儲存體帳戶都會啟用固定儲存體。

## <a name="overview"></a>總覽

固定儲存體可協助醫療保健組織、金融機構和相關產業 (特別是經紀商組織) 安全地儲存資料。 也可以在任何案例中運用它來保護重要資料, 以防止修改或刪除。 

典型應用包括：

- **法規遵循**：Azure Blob 儲存體的固定儲存體可協助組織處理 SEC 17a-4(f)、CFTC 1.31(d)、FINRA 和其他法規。 技術白皮書 Cohasset 將詳細說明如何透過[Microsoft 服務信任入口網站](https://aka.ms/AzureWormStorage), 下載不可變的儲存體如何解決這些法規需求。 [Azure 信任中心](https://www.microsoft.com/trustcenter/compliance/compliance-overview)包含有關我們的合規性認證的詳細資訊。

- **安全文件保留**：Azure Blob 儲存體的固定儲存體可確保任何使用者都無法修改或刪除資料, 包括具有帳戶系統管理許可權的使用者。

- **合法保存**：適用于 Azure Blob 儲存體的固定儲存體可讓使用者在所需的持續期間內, 儲存對訴訟或業務使用很重要的機密資訊, 直到移除保存為止。 這項功能不只限於合法使用案例, 也可以視為以事件為基礎的保存或企業鎖定, 需要根據事件觸發程式或公司原則來保護資料的需求。

不可變的儲存體支援下列各項:

- 以 **[時間為基礎的保留原則支援](#time-based-retention)** :使用者可以設定原則, 以在指定的間隔內儲存資料。 設定以時間為基礎的保留原則時, 可以建立和讀取 blob, 但不能修改或刪除。 保留期限到期後, 可以刪除 blob, 但不會覆寫。

- **[合法保存原則支援](#legal-holds)** :如果不知道保留間隔, 使用者可以設定合法保存來儲存資料,, 直到清除合法保存為止。  設定合法保存原則時, 可以建立和讀取 blob, 但不能修改或刪除。 每個合法保存都會與使用者定義的英數位元標記相關聯 (例如案例識別碼、事件名稱等), 做為識別碼字串。 

- **所有 Blob 層都支援**：WORM 原則與 Azure Blob 儲存層無關，而且會套用至所有層：經常性存取層、非經常性存取層和封存存取層。 使用者可以將資料轉換到其工作負載的成本最佳化層中，同時維護資料不變性。

- **容器層級設定**：使用者可以在容器層級設定以時間為基礎的保留原則和合法保存標記。 藉由使用簡單的容器層級設定, 使用者可以建立及鎖定以時間為基礎的保留原則、延長保留間隔、設定和清除合法保存等。 這些原則會套用到現有和新容器中的所有 Blob。

- **稽核記錄支援**：每個容器都包含原則 audit 記錄。 它會針對鎖定的以時間為基礎的保留原則, 最多顯示7個以時間為基礎的保留命令, 並包含使用者識別碼、命令類型、時間戳記及保留間隔。 針對合法保存，此記錄包含使用者識別碼、命令類型、時間戳記及合法保存標記。 此記錄會保留在原則的存留期間, 符合 SEC 17a-4 (f) 法規指導方針。 [ [Azure 活動記錄](../../azure-monitor/platform/activity-logs-overview.md)檔] 會顯示所有控制平面活動的更完整記錄;啟用[Azure 診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)時, 會保留並顯示資料平面作業。 基於法規需求或其他目的，使用者有責任持續不斷地儲存那些記錄。

## <a name="how-it-works"></a>運作方式

Azure Blob 儲存體的固定儲存體支援兩種 WORM 或固定原則：以時間為基礎的保留和合法保存。 在容器上套用以時間為基礎的保留原則或合法保存時, 所有現有的 blob 都會在30秒內移至不可變的 WORM 狀態。 所有上傳至該容器的新 blob 也會移至不可變的狀態。 當所有 blob 都移至不可變的狀態之後, 就會確認不可變的原則, 而且不允許不可變容器中現有和新物件的所有覆寫或刪除作業。

如果有任何受不可變原則保護的 blob, 則也不允許刪除容器和帳戶。 如果有至少一個 Blob 具有鎖定的時間型保留原則或法務保存措施，則「刪除容器」作業會失敗。 如果有至少一個 WORM 容器具有合法保存或有一個 Blob 具有有效保留間隔，則儲存體帳戶刪除作業會失敗。 

### <a name="time-based-retention"></a>以時間為基礎的保留

> [!IMPORTANT]
> 必須*鎖定*以時間為基礎的保留原則, blob 才會符合 SEC 17a-4 (f) 和其他法規合規性的不變 (寫入和刪除保護) 狀態。 我們建議您在合理的時間內鎖定原則, 通常不到24小時。 套用的以時間為基礎的保留原則初始狀態已*解除鎖定*, 可讓您測試功能並對原則進行變更, 然後再加以鎖定。 雖然 [已*解除鎖定*] 狀態提供非永久性保護, 但不建議針對短期功能試用以外的任何目的使用 [*解除鎖定*] 狀態。 

若在容器上套用以時間為基礎的保留原則，在「有效」保留期限內，容器中的所有 Blob 都會保持固定狀態。 現有 blob 的有效保留期限等於 blob 修改時間與使用者指定的保留間隔之間的差異。

若為新的 Blob，有效保留期限等於使用者指定的保留間隔。 因為使用者可以延長保留間隔，所以固定儲存體會使用使用者所指定保留間隔的最新值，計算有效的保留期限。

> [!TIP]
> **範例:** 使用者建立以時間為基礎的保留原則，其保留間隔為五年。
>
> 該容器中的現有 blob ( _testblob1)_ ) 是一年前建立的。 _Testblob1)_ 的有效保留期限為四年。
>
> 新的 blob ( _testblob2_) 現在已上傳至容器。 這個新 Blob 的有效保留期限為 5 年。

建議僅針對功能測試使用已解除鎖定的以時間為基礎的保留原則, 而且必須鎖定原則, 才能符合 SEC 17a-4 (f) 和其他法規合規性。 鎖定以時間為基礎的保留原則之後, 就無法移除原則, 而且最多可以有5個增加到有效保留期限。 如需有關如何設定和鎖定以時間為基礎之保留原則的詳細資訊, 請參閱[開始](#getting-started)使用一節。

### <a name="legal-holds"></a>合法保存

當您設定合法保存時，所有現有和新的 Blob 都會保持固狀態，直到清除合法保存為止。 如需有關如何設定和清除合法保存的詳細資訊，請參閱[使用者入門](#getting-started)。

一個容器可以同時具有合法保存和以時間為基礎的保留原則。 該容器中的所有 Blob 都會保持固定狀態，直到所有合法保存都遭到清除為止 (即使其有效保留期限已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有合法保存)。

下表顯示會針對不同固定案例停用的 Blob 作業類型。 如需詳細資訊，請參閱 [Azure Blob 服務 API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)文件。

|狀況  |Blob 狀態  |不允許 Blob 作業  |
|---------|---------|---------|
|Blob 上的有效保留間隔尚未過期及/或已設定合法保存     |固定：防刪與防寫保護         | 放置 Blob<sup>1</sup>、放置區塊<sup>1</sup>、放置區塊清單<sup>1</sup>、刪除容器、刪除 blob、設定 Blob 中繼資料、放置分頁、設定 blob 屬性、快照集 Blob、累加複製 Blob、附加區塊         |
|Blob 上的有效保留間隔已過期     |僅限防寫保護 (允許刪除作業)         |放置 Blob<sup>1</sup>、放置區塊<sup>1</sup>、放置區塊清單<sup>1</sup>、設定 Blob 中繼資料、放置分頁、設定 Blob 屬性、快照集 Blob、累加複製 Blob、附加區塊         |
|所有合法保存都已清除，且容器上未設定任何以時間為基礎的保留原則     |可變動         |None         |
|未建立任何 WORM 原則 (以時間為基礎的保留或合法保存)     |可變動         |None         |

<sup>1</sup>應用程式允許這些作業一次建立新的 blob。 不允許不可變容器中現有 blob 路徑上的所有後續覆寫作業。

## <a name="supported-values"></a>支援的值

### <a name="time-based-retention"></a>以時間為基礎的保留
- 針對儲存體帳戶, 具有鎖定之以時間為基礎的不可變原則的容器數目上限為1000。
- 保留間隔的最小值為1天。 最大值為146000天 (400 年)。
- 針對容器, 針對鎖定的以時間為基礎的不可變原則, 擴充保留間隔的編輯次數上限為5。
- 針對容器, 已鎖定的原則會保留最多7個以時間為基礎的保留原則 audit 記錄。

### <a name="legal-hold"></a>合法保存
- 若為儲存體帳戶，具有合法保存設定的容器數目上限為 1,000。
- 若為容器，合法保存標記數目上限為 10。
- 合法保存標記的最小長度為3個英數位元。 最大長度為23個英數位元。
- 針對容器, 原則期間會保留最多10個合法保存原則的 audit 記錄。

## <a name="pricing"></a>定價

使用這項功能不需額外付費。 固定資料的定價方式與一般可變動的資料相同。 如需 Azure Blob 儲存體定價的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="getting-started"></a>使用者入門
不可變的儲存體僅適用于一般用途 v2 和 Blob 儲存體帳戶。 這些帳戶必須透過[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)來管理。 如需升級現有一般用途 v1 儲存體帳戶的相關資訊, 請參閱[升級儲存體帳戶](../common/storage-account-upgrade.md)。

最新版本的[Azure 入口網站](https://portal.azure.com)、 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)和[Azure PowerShell](https://github.com/Azure/azure-powershell/releases)支援 Azure Blob 儲存體的固定儲存體。 也提供[用戶端程式庫支援](#client-libraries)。

### <a name="azure-portal"></a>Azure 入口網站

1. 建立新的容器或選取現有容器，以儲存必須保持固定狀態的 Blob。
 容器必須位於 GPv2 或 Blob 儲存體帳戶中。
2. 在容器設定中選取 [存取原則]。 然後選取 [固定 blob 儲存體] 之下的 [+ 新增原則]。

    ![入口網站中的容器設定](media/storage-blob-immutable-storage/portal-image-1.png)

3. 若要啟用以時間為基礎的保留，請從下拉式功能表中選取 [以時間為基礎的保留]。

    ![在 [原則類型] 下選取的 [以時間為基礎的保留]](media/storage-blob-immutable-storage/portal-image-2.png)

4. 輸入以天為單位的保留間隔 (可接受的值為1到146000天)。

    ![[更新保留期限] 方塊](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    原則的初始狀態為 [已解除鎖定], 可讓您測試功能並對原則進行變更, 然後再進行鎖定。 鎖定原則對於符合 SEC 17a-4 等法規而言是不可或缺的。

5. 鎖定原則。 以滑鼠右鍵按一下省略號 ( **...** ), 接著會出現下列功能表, 其中包含其他動作:

    ![功能表上的 [鎖定原則]](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. 選取 [**鎖定原則**] 並確認鎖定。 原則現已鎖定且無法刪除, 只會允許保留間隔的延伸。 不允許 Blob 刪除和覆寫。 

    ![確認功能表上的 [鎖定原則]](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. 若要啟用合法保存，請選取 [+ 新增原則]。 從下拉式功能表中選取 [合法保存]。

    ![在功能表上 [原則類型] 之下的 [合法保存]](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. 建立具有一或多個標記的合法保存。

    ![原則類型之下的 [標記名稱] 方塊](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. 若要清除合法保存, 只要移除套用的合法保存識別碼標記即可。

### <a name="azure-cli"></a>Azure CLI

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>PowerShell

Az. Storage 模組支援不可變的儲存體。  若要啟用此功能，請依照下列步驟執行：

1. 確定您已安裝最新版的 PowerShellGet：`Install-Module PowerShellGet –Repository PSGallery –Force`。
2. 移除任何先前安裝的 Azure PowerShell。
3. 安裝 Azure PowerShell：`Install-Module Az –Repository PSGallery –AllowClobber`。

本文後面的 [PowerShell 範例程式碼](#sample-powershell-code)一節會說明功能使用情況。

## <a name="client-libraries"></a>用戶端程式庫

下列用戶端程式庫支援適用於 Azure Blob 儲存體的固定儲存體：

- [.NET 用戶端程式庫版本 7.2.0-preview 和更新版本](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js 用戶端程式庫 4.0.0 版和更新版本](https://www.npmjs.com/package/azure-arm-storage)
- [Python 用戶端程式庫 2.0.0 候選版 2 和更新版本](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java 用戶端程式庫](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>常見問題集

**您可以提供 WORM 合規性的檔嗎？**

是的。 為了記載合規性, Microsoft 保留了領先的獨立評估公司, 專門從事記錄管理和資訊治理、Cohasset 的關聯、評估 Azure 不可變的 Blob 儲存體, 以及其與需求特定的合規性金融服務產業。 Cohasset 已驗證 Azure 不變 Blob 儲存體 (當用來以 WORM 狀態保留以時間為基礎的 Blob) 時, 符合 CFTC 規則 1.31 (c)-(d)、FINRA 規則4511和 SEC 規則17a-4 的相關儲存需求。 Microsoft 以這組規則為目標, 因為它們代表針對金融機構的記錄保留範圍內最具規範性的指導方針。 Cohasset 報告可在[Microsoft 服務信任中心](https://aka.ms/AzureWormStorage)取得。 若要向 Microsoft 要求關於蠕蟲合規性的證明, 請洽詢 Azure 支援。

**此功能只適用於區塊 Blob，或者也適用於分頁和附加 Blob？**

不可變的儲存體可以搭配任何 blob 類型使用, 因為它是在容器層級設定, 但我們建議您針對主要儲存區塊 blob 的容器使用 WORM。 不同于區塊 blob, 任何新的分頁 blob 和附加 blob 都必須在 WORM 容器外部建立, 然後再複製到中。 將這些 blob 複製到 WORM 容器後, 就不會再進一步*附加*至附加 blob 或分頁 blob 的變更。 因此, 強烈建議您在儲存任何使用中虛擬機器之 Vhd (分頁 blob) 的容器上設定 WORM 原則, 因為它會鎖定 VM 磁片。

**我是否需要建立新的儲存體帳戶才能使用這項功能？**

否, 您可以搭配任何現有或新建立的一般用途 v2 或 Blob 儲存體帳戶來使用不可變的儲存體。 這項功能適用于 GPv2 和 Blob 儲存體帳戶中的區塊 blob 使用。 一般用途 v1 儲存體帳戶不受支援, 但可以輕鬆地升級至一般用途 v2。 如需升級現有一般用途 v1 儲存體帳戶的相關資訊, 請參閱[升級儲存體帳戶](../common/storage-account-upgrade.md)。

**我可以同時套用合法保存和以時間為基礎的保留原則嗎？**

是, 容器可以同時具有合法保存和以時間為基礎的保留原則。 該容器中的所有 Blob 都會保持固定狀態，直到所有合法保存都遭到清除為止 (即使其有效保留期限已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有合法保存)。

**合法的持有原則是否僅適用于法律訴訟, 或是否有其他使用案例？**

否, 合法保存只是用於非以時間為基礎的保留原則的一般詞彙。 它不需要僅用於訴訟相關的訴訟。 合法保存原則適用于停用覆寫和刪除, 以保護重要的企業蠕蟲資料, 其中保留期限不明。 在自訂事件觸發程式需要使用以時間為基礎的保留原則之前, 您可以使用它做為企業原則來保護您的任務關鍵性 WORM 工作負載, 或使用它做為預備原則。 

**我可以移除*鎖定*的以時間為基礎的保留原則或合法保存嗎？**

只能從容器中移除解除鎖定的以時間為基礎的保留原則。 一旦鎖定以時間為基礎的保留原則, 就無法將它移除;僅允許有效的保留週期延伸。 您可以刪除合法的標記。 刪除所有合法標記時, 將會移除合法的保存。

**如果我嘗試刪除的容器具有以時間為基礎的「鎖定」保留原則或合法保存，則會發生什麼事？**

如果有至少一個 Blob 具有鎖定的時間型保留原則或法務保存措施，則「刪除容器」作業會失敗。 只有在沒有任何 Blob 具有有效的保留間隔，而且沒有法務保存措施時，「刪除容器」作業才會成功。 您必須先刪除 Blob，才可以刪除容器。

**如果我嘗試刪除的儲存體帳戶具有 WORM 帳戶，且該帳戶具有以時間為基礎的「鎖定」保留原則或合法保存，則會發生什麼事？**

如果有至少一個 WORM 容器具有合法保存或有一個 Blob 具有有效保留間隔，則儲存體帳戶刪除作業會失敗。 您必須先刪除所有 WORM 容器，才能刪除儲存體帳戶。 如需有關容器刪除的資訊，請參閱前面的問題。

**當 Blob 處於固定狀態時，我可以跨越不同 Blob 層 (經常性存取層、非經常性存取層、冷門存取層) 移動資料嗎？**

是, 您可以使用 [設定 Blob 層] 命令跨 Blob 層移動資料, 同時將資料保持在符合規範的固定狀態。 Blob 經常性存取層、非經常性存取層和封存存取層都支援固定儲存體。

**如果我付款失敗，而保留間隔尚未過期，則會發生什麼事？**

如果未付款，則會依照您與 Microsoft 簽訂的合約條款與條件規定，套用一般資料保留原則。

**您是否提供試用此功能的試用版或寬限期？**

是的。 第一次建立以時間為基礎的保留原則時, 它會處於「未*鎖定*」狀態。 在此狀態中，您可以對保留間隔進行任何所需的變更，例如增加或減少保留間隔，甚至刪除原則。 鎖定原則之後，會保持鎖定狀態，直到保留間隔到期為止。 這個鎖定的原則會防止刪除和修改保留間隔。 我們強烈建議僅將「未鎖定」狀態使用於試用目的，並且在 24 小時期間內鎖定原則。 這些做法可協助您符合 SEC 17a-4(f) 和其他法規。

**我可以搭配不可變的 blob 原則使用虛刪除嗎？**

是的。 [Azure Blob 儲存體](storage-blob-soft-delete.md)的虛刪除會套用至儲存體帳戶內的所有容器, 而不論是合法保存或以時間為基礎的保留原則。 建議您先啟用虛刪除以提供額外的保護, 再套用和確認任何不可變的 WORM 原則。 

**有哪些功能可用？**

Azure 公用、Azure 中國和 Azure Government 區域提供不可變的儲存體。 如果您的區域無法使用固定儲存體, 請洽詢支援和電子郵件azurestoragefeedback@microsoft.com。

## <a name="sample-powershell-code"></a>PowerShell 範例程式碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下 PowerShell 範例指令碼僅供參考。 此指令碼會建立新的儲存體帳戶和容器。 然後，示範如何設定及清除合法保存、建立及鎖定以時間為基礎的保留原則 (也稱為固定原則)，以及延長保留間隔。

設定及測試 Azure 儲存體帳戶：

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

設定及清除法務保存措施：

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

建立或更新不變性原則：
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

擷取不變性原則：
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

鎖定不變性原則 (加入 -Force 即可關閉提示)：
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

擴充不變性原則：
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

移除已解除鎖定的非永久性原則 (add-Force 以關閉提示):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
