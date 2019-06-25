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
ms.openlocfilehash: d58c596421cec2e69210dd39a5d4a9708c154b44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492762"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>在 Azure Blob 儲存體中儲存業務關鍵資料

不可變的儲存體，Azure Blob 儲存體可讓使用者將業務關鍵的資料物件儲存在蠕蟲 （一次寫入、 讀取許多） 狀態。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。 Blob 物件建立和讀取，但不是修改或刪除，在保留間隔期間。 一般目的 v2 及所有 Azure 區域中的 Blob 儲存體帳戶會啟用不可變的儲存體。

## <a name="overview"></a>概觀

不可變的儲存體可協助醫療保健組織、 金融機構，以及相關的產業-特別是訊息代理程式經銷商組織安全地儲存資料。 它也可以運用在任何案例中保護重要資料，避免修改或刪除。 

典型應用包括：

- **法規遵循**：Azure Blob 儲存體的固定儲存體可協助組織處理 SEC 17a-4(f)、CFTC 1.31(d)、FINRA 和其他法規。 Cohasset 產生關聯的技術白皮書詳細說明這些法規的要求會透過可下載的儲存體位址如何變[Microsoft 服務信任入口網站](https://aka.ms/AzureWormStorage)。 [Azure 信任中心](https://www.microsoft.com/trustcenter/compliance/compliance-overview)包含我們的合規性認證的詳細的資訊。

- **安全文件保留**：不可變的儲存體，Azure Blob 儲存體可確保無法修改或刪除任何使用者，包括以帳戶系統管理權限的使用者資料。

- **合法保存**：不可變的儲存體，Azure Blob 儲存體可讓使用者儲存訴訟或商務用在防狀態所需的持續時間，直到移除保留至關重要的機密資訊。 這項功能不只限於合法使用案例，但可以也視為事件為基礎的保留或企業鎖定，需要保護資料是根據事件觸發程序或公司原則的情況。

不可變的儲存體支援下列各項：

- **[支援以時間為基礎的保留原則](#time-based-retention)** :使用者可以設定原則來將資料儲存在指定間隔內。 當時間為基礎的保留原則已設定時，blob 可以建立和讀取，但不是修改或刪除。 保留期限到期之後，即可刪除 blob，但不是會覆寫。

- **[法務保存措施原則支援](#legal-holds)** :如果不知道在保留間隔，使用者可以設定法務保存措施來 immutably 儲存資料，直到已清除的適用法務保存措施。  當法務保存措施原則設定時，blob 可以建立和讀取，但不是修改或刪除。 每個適用法務保存措施是相關聯的使用者定義英數字元標記 （例如案例識別碼、 事件名稱等），做為識別項字串。 

- **所有 Blob 層都支援**：WORM 原則與 Azure Blob 儲存層無關，而且會套用至所有層：經常性存取層、非經常性存取層和封存存取層。 使用者可以將資料轉換到其工作負載的成本最佳化層中，同時維護資料不變性。

- **容器層級設定**：使用者可以在容器層級設定以時間為基礎的保留原則和合法保存標記。 使用簡單的容器層級設定，使用者可以建立和鎖定時間為基礎的保留原則、 擴充保留間隔、 設定和清除法務保存措施，和更多功能。 這些原則會套用到現有和新容器中的所有 Blob。

- **稽核記錄支援**：每個容器包含原則稽核記錄檔。 它會顯示最多七個時間為基礎保留鎖定的時間為基礎的保留原則的命令，並包含使用者識別碼、 命令類型、 時間戳記，以及保留間隔。 針對合法保存，此記錄包含使用者識別碼、命令類型、時間戳記及合法保存標記。 此記錄檔會保留原則，根據秒 17a-4(f) 法規的指導方針的存留期。 [Azure 活動記錄檔](../../azure-monitor/platform/activity-logs-overview.md)示範的更完整的記錄檔的所有控制平面活動，同時讓[Azure 診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)會保留，並顯示資料平面作業。 基於法規需求或其他目的，使用者有責任持續不斷地儲存那些記錄。

## <a name="how-it-works"></a>運作方式

Azure Blob 儲存體的固定儲存體支援兩種 WORM 或固定原則：以時間為基礎的保留和合法保存。 當時間為基礎的保留原則或法務保存措施套用在容器上時，所有現有的 blob 將移至不可變的蠕蟲狀態在 30 秒內。 所有新的 blob 上傳至該容器也會移動至不可變的狀態。 當所有 blob 已都移至不可變的狀態，確認不可變的原則之後，而且全部覆寫或刪除現有和新的物件不可變的容器中的作業不允許。

容器和帳戶刪除也不允許有不可變的原則所保護的任何 blob。 如果有至少一個 Blob 具有鎖定的時間型保留原則或法務保存措施，則「刪除容器」作業會失敗。 如果有至少一個 WORM 容器具有合法保存或有一個 Blob 具有有效保留間隔，則儲存體帳戶刪除作業會失敗。 

### <a name="time-based-retention"></a>以時間為基礎的保留期

> [!IMPORTANT]
> 必須是以時間為基礎的保留原則*鎖定*處於 標準 blob 不可變 （寫入和刪除受保護） 秒 17a-4(f) 和其他法規的合規性狀態。 我們建議您先鎖定原則在合理的時間通常少於 24 小時。 套用以時間為基礎的保留原則的初始狀態*解除鎖定*，可讓您測試功能和變更原則之前鎖定它。 雖然*解除鎖定*狀態會提供不變性的保護，我們不建議使用*解除鎖定*狀態短期功能試用版以外的任何用途。 

若在容器上套用以時間為基礎的保留原則，在「有效」  保留期限內，容器中的所有 Blob 都會保持固定狀態。 現有的 blob 的有效的保留期限會等於 blob 修改時間和使用者指定的保留間隔之間的差異。

若為新的 Blob，有效保留期限等於使用者指定的保留間隔。 因為使用者可以延長保留間隔，所以固定儲存體會使用使用者所指定保留間隔的最新值，計算有效的保留期限。

> [!TIP]
> **範例：** 使用者建立以時間為基礎的保留原則，其保留間隔為五年。
>
> 在該容器中，現有的 blob _testblob1_，建立前一年。 有效的保留期限_testblob1_是四年的時間。
>
> 新的 blob _testblob2_，現在已上傳至容器。 這個新 Blob 的有效保留期限為 5 年。

建議使用 「 解除鎖定的時間為基礎的保留原則，僅供測試的功能，必須鎖定原則，才能符合秒 17a-4(f) 及其他法規的合規性。 一旦鎖定時間為基礎的保留原則，無法移除原則，並允許最多 5 個增加為有效的保留期限。 如需有關如何設定和鎖定時間為基礎的保留原則的詳細資訊，請參閱 <<c0> [ 開始使用](#getting-started)一節。

### <a name="legal-holds"></a>合法保存

當您設定合法保存時，所有現有和新的 Blob 都會保持固狀態，直到清除合法保存為止。 如需有關如何設定和清除合法保存的詳細資訊，請參閱[使用者入門](#getting-started)。

一個容器可以同時具有合法保存和以時間為基礎的保留原則。 該容器中的所有 Blob 都會保持固定狀態，直到所有合法保存都遭到清除為止 (即使其有效保留期限已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有合法保存)。

下表顯示會針對不同固定案例停用的 Blob 作業類型。 如需詳細資訊，請參閱 [Azure Blob 服務 API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)文件。

|案例  |Blob 狀態  |不允許 Blob 作業  |
|---------|---------|---------|
|Blob 上的有效保留間隔尚未過期及/或已設定合法保存     |固定：防刪與防寫保護         | 放置 Blob<sup>1</sup>，將區塊放<sup>1</sup>、 放置區塊清單<sup>1</sup>、 刪除容器中，刪除 Blob，設定 Blob 中繼資料，請將頁面中，設定 Blob 屬性 」、 「 快照集 Blob、 「 累加複製 Blob附加區塊         |
|Blob 上的有效保留間隔已過期     |僅限防寫保護 (允許刪除作業)         |放置 Blob<sup>1</sup>、放置區塊<sup>1</sup>、放置區塊清單<sup>1</sup>、設定 Blob 中繼資料、放置分頁、設定 Blob 屬性、快照集 Blob、累加複製 Blob、附加區塊         |
|所有合法保存都已清除，且容器上未設定任何以時間為基礎的保留原則     |可變動         |None         |
|未建立任何 WORM 原則 (以時間為基礎的保留或合法保存)     |可變動         |None         |

<sup>1</sup>應用程式可讓這些作業來一次建立新的 blob。 所有後續覆寫現有的 blob 路徑中的不可變的容器上的作業不允許。

## <a name="supported-values"></a>支援的值

### <a name="time-based-retention"></a>以時間為基礎的保留期
- 儲存體帳戶、 容器與鎖定時間為基礎的不可變原則的最大數目為 1,000。
- 最小保留間隔為 1 天。 最大值為 146,000 天 （400 年）。
- 針對容器中，編輯擴充鎖定時間為基礎的不可變原則保留間隔的最大數目為 5。
- 針對容器中，最多 7 以時間為基礎的保留原則稽核記錄檔會保留原則的持續時間。

### <a name="legal-hold"></a>法務保存措施
- 若為儲存體帳戶，具有合法保存設定的容器數目上限為 1,000。
- 若為容器，合法保存標記數目上限為 10。
- 適用法務保存措施標記的最小長度是 3 個的英數字元。 最大長度為 23 的英數字元。
- 容器，合法的 10 個最多會保留原則稽核記錄檔會保留原則的持續時間。

## <a name="pricing"></a>價格

使用這項功能不需額外付費。 固定資料的定價方式與一般可變動的資料相同。 如需 Azure Blob 儲存體定價的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="getting-started"></a>開始使用
不可變的儲存體是只適用於一般用途 v2 和 Blob 儲存體帳戶。 這些帳戶必須透過管理[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 如需有關升級現有的一般用途 v1 儲存體帳戶，請參閱[儲存體帳戶升級](../common/storage-account-upgrade.md)。

最新版本[Azure 入口網站](https://portal.azure.com)， [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，並[Azure PowerShell](https://github.com/Azure/azure-powershell/releases)支援 Azure Blob 儲存體的不可變的儲存體。 [用戶端程式庫支援](#client-libraries)也會提供。

### <a name="azure-portal"></a>Azure 入口網站

1. 建立新的容器或選取現有容器，以儲存必須保持固定狀態的 Blob。
 容器必須位於 GPv2 或 Blob 儲存體帳戶中。
2. 在容器設定中選取 [存取原則]  。 然後選取 [固定 blob 儲存體]  之下的 [+ 新增原則]  。

    ![入口網站中的容器設定](media/storage-blob-immutable-storage/portal-image-1.png)

3. 若要啟用以時間為基礎的保留，請從下拉式功能表中選取 [以時間為基礎的保留]  。

    ![在 [原則類型] 下選取的 [以時間為基礎的保留]](media/storage-blob-immutable-storage/portal-image-2.png)

4. 輸入保留間隔天數 （1 至 146000 天的可接受的值）。

    ![[更新保留期限] 方塊](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    此原則的初始狀態是解除鎖定可讓您測試功能和變更原則之前鎖定它。 鎖定原則是不可或缺的秒 17a-4 等法規的合規性的。

5. 鎖定原則。 以滑鼠右鍵按一下省略符號 ( **...** )，並會出現下列功能表的 其他動作：

    ![功能表上的 [鎖定原則]](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. 選取 **鎖定原則**並確認鎖定。 原則現在已被鎖定，且無法刪除，系統會允許在保留間隔的延伸模組。 不允許覆寫及刪除 blob。 

    ![確認 [鎖定原則] 功能表上](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. 若要啟用合法保存，請選取 [+ 新增原則]  。 從下拉式功能表中選取 [合法保存]  。

    ![在功能表上 [原則類型] 之下的 [合法保存]](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. 建立具有一或多個標記的合法保存。

    ![原則類型之下的 [標記名稱] 方塊](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. 若要清除適用法務保存措施，只要移除套用的適用法務保存措施識別碼標記。

### <a name="azure-cli"></a>Azure CLI

此功能包含在下列命令群組中：`az storage container immutability-policy` 和 `az storage container legal-hold`。 在其上執行 `-h` 來查看命令。

### <a name="powershell"></a>PowerShell

Az.Storage 模組支援不可變的儲存體。  若要啟用此功能，請依照下列步驟執行：

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

**您可以提供蠕蟲合規性文的件嗎？**

是。 文件合規性，Microsoft 保留領先業界的獨立評定公司專門記錄管理與資訊監管，Cohasset 產生關聯，來評估 Azure 不可變的 Blob 儲存體和其遵守特定的需求金融服務產業中。 Cohasset 驗證 Azure 不變的 Blob 儲存體，用來保留時間為基礎的 Blob，蠕蟲處於時符合 CFTC 規則 1.31(c)-(d)、 FINRA 規則 4511，和 SEC Rule 17a-4 相關的儲存體需求。 Microsoft 會針對這組規則，因為它們代表記錄保留金融機構的全域最精準的指引。 Cohasset 報表位於[Microsoft 服務信任中心](https://aka.ms/AzureWormStorage)。 若要向 Microsoft 要求的證明字母，有關蠕蟲合規性，請連絡 Azure 支援。

**此功能只適用於區塊 Blob，或者也適用於分頁和附加 Blob？**

固定儲存體可以與任何 Blob 類型一起使用，但我們建議您大部分將其用於區塊 Blob。 不同於區塊 Blob，分頁 Blob 和附加 Blob 必須在 WORM 容器外部建立，然後再複製到其中。 之後您這些將 blob 複製到 「 蠕蟲 」 容器沒有進一步*附加*附加 blob 或分頁 blob 的變更允許。

**我需要建立新的儲存體帳戶，才能使用此功能嗎？**

否，您可以使用不可變的儲存體與任何現有或新建立的一般用途 v2 或 Blob 儲存體帳戶。 這項功能適用於使用 GPv2 和 Blob 儲存體帳戶中的區塊 blob 的使用方式。 一般用途 v1 儲存體帳戶不支援，但可以輕鬆地升級至一般用途 v2。 如需有關升級現有的一般用途 v1 儲存體帳戶，請參閱[儲存體帳戶升級](../common/storage-account-upgrade.md)。

**可以套用的適用法務保存措施和以時間為基礎的保留原則嗎？**

是，容器可以在同一時間有適用法務保存措施和以時間為基礎的保留原則。 該容器中的所有 Blob 都會保持固定狀態，直到所有合法保存都遭到清除為止 (即使其有效保留期限已過期)。 相反地，Blob 會保持固定狀態，直到有效保留週期到期為止 (即使已清除所有合法保存)。

**只有在法律訴訟時才適用法務保存措施原則，或是否有其他使用案例？**

否，合法的保存是只用於非時間為基礎的保留原則的一般術語。 它不需要只用於訴訟相關記錄。 法務保存措施原則可用於停用 覆寫及刪除保護重要的企業蠕蟲資料的保留期限是未知的位置。 您可以為企業原則使用它來保護您任務關鍵性蠕蟲工作負載，或使用它作為暫存原則之前的自訂事件觸發程序需要使用以時間為基礎的保留原則。 

**我可以移除*鎖定*以時間為基礎的保留原則或法務保存措施嗎？**

僅解除鎖定的時間為基礎的保留原則可以從容器移除。 一旦鎖定時間為基礎的保留原則，就無法移除;只有有效的保留期限允許擴充功能。 您可以刪除法務保存措施的標記。 刪除所有的法律標籤，會移除適用法務保存措施。

**如果我嘗試刪除的容器具有以時間為基礎的「鎖定」  保留原則或合法保存，則會發生什麼事？**

如果有至少一個 Blob 具有鎖定的時間型保留原則或法務保存措施，則「刪除容器」作業會失敗。 只有在沒有任何 Blob 具有有效的保留間隔，而且沒有法務保存措施時，「刪除容器」作業才會成功。 您必須先刪除 Blob，才可以刪除容器。

**如果我嘗試刪除的儲存體帳戶具有 WORM 帳戶，且該帳戶具有以時間為基礎的「鎖定」  保留原則或合法保存，則會發生什麼事？**

如果有至少一個 WORM 容器具有合法保存或有一個 Blob 具有有效保留間隔，則儲存體帳戶刪除作業會失敗。 您必須先刪除所有 WORM 容器，才能刪除儲存體帳戶。 如需有關容器刪除的資訊，請參閱前面的問題。

**當 Blob 處於固定狀態時，我可以跨越不同 Blob 層 (經常性存取層、非經常性存取層、冷門存取層) 移動資料嗎？**

是，您可以使用設定 Blob 層命令，在 blob 層之間移動資料，同時符合規範的狀態不變的資料。 Blob 經常性存取層、非經常性存取層和封存存取層都支援固定儲存體。

**如果我付款失敗，而保留間隔尚未過期，則會發生什麼事？**

如果未付款，則會依照您與 Microsoft 簽訂的合約條款與條件規定，套用一般資料保留原則。

**您是否提供試用此功能的試用版或寬限期？**

是。 第一次建立以時間為基礎的保留原則時，它是在*解除鎖定*狀態。 在此狀態中，您可以對保留間隔進行任何所需的變更，例如增加或減少保留間隔，甚至刪除原則。 鎖定原則之後，會保持鎖定狀態，直到保留間隔到期為止。 此鎖定的原則可防止刪除和修改的保留間隔。 我們強烈建議僅將「未鎖定」  狀態使用於試用目的，並且在 24 小時期間內鎖定原則。 這些做法可協助您符合 SEC 17a-4(f) 和其他法規。

**我可以使用虛刪除與不可變的 blob 原則嗎？**

是。 [Azure Blob 儲存體的虛刪除](storage-blob-soft-delete.md)適用於儲存體帳戶，不論適用法務保存措施或以時間為基礎的保留原則內的所有容器。 我們建議先套用任何不可變的蠕蟲原則，並確認啟用虛刪除額外的保護。 

**此功能可使用的？**

Azure 公用、Azure 中國和 Azure Government 區域提供不可變的儲存體。 如果您的區域不提供不可變的儲存體，請連絡支援與電子郵件azurestoragefeedback@microsoft.com。

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

移除未鎖定的不變性原則 （新增-Force，即可關閉提示）：
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
