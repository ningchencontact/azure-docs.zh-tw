---
title: 從自訂映像佈建 Azure Batch 集區 | Microsoft Docs
description: 您可以從自訂映像建立 Batch 集區，以佈建含有您應用程式所需軟體和資料的計算節點。 自訂映像是設定計算節點以執行 Batch 工作負載的有效方式。
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 80cba5e1b5e38e31dea2272cc4e33b4a95940e41
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565620"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>使用自訂映像來建立虛擬機器的集區 

當您使用虛擬機器設定建立 Azure Batch 集區時，需指定 VM 映像，以提供集區中每個計算節點的作業系統。 您可以使用支援的 Azure Marketplace 映像或自訂映像 (即您自行建立並設定的 VM 映像) 來建立虛擬機器集區。 自訂映像必須是*受控映像*資源，且位於和 Batch 帳戶相同的 Azure 訂用帳戶和區域中。

## <a name="benefits-of-custom-images"></a>自訂映像的優點

當您提供自訂映像，您可以控制作業系統設定以及要使用的作業系統和資料磁碟類型。 自訂映像可以包含應用程式和參考資料，它們在所有 Batch 集區節點上一經佈建便可使用。

使用自訂映像可以節省讓集區的計算節點可以執行 Batch 工作負載的準備時間。 雖然您可以使用 Azure Marketplace 映像，並且在佈建後在每個節點上安裝軟體，但使用自訂映像可能比較有效率。

使用專為您的案例設定的自訂映像有幾個優點：

- **設定作業系統 (OS)** 。 您可以自訂映像作業系統磁碟的設定。 
- **預先安裝應用程式。** 在 OS 磁碟上預先安裝應用程式，與在佈建計算節點之後使用開始工作來安裝應用程式相比，這樣更有效率且更不容易出錯。
- **節省 VM 的重新開機時間。** 應用程式安裝通常需要重新啟動 VM，這相當耗時。 預先安裝應用程式可以節省重新開機時間。 
- **一次複製大量資料。** 藉由將靜態資料複製到受控映像的資料磁碟，使靜態資料成為受控自訂映像的一部分。 這只需要執行一次，就能將資料提供給集區的每個節點。
- **選擇磁碟類型。** 您可以選擇在 OS 磁碟和資料磁碟使用進階儲存體。
- **讓集區變大。** 當您使用受控自訂映像來建立集區時，您無須複製映像 Blob VHD，集區便可以成長。

## <a name="prerequisites"></a>必要條件

- **受控映像資源**。 若要使用自訂映像來建立虛擬機器集區，您必須在與 Batch 帳戶相同的 Azure 訂用帳戶和區域中，擁有或建立受控映像資源。 您應該從 VM 之 OS 磁碟 (以及視需要從其連結之資料磁碟) 的快照集建立該映像。 如需詳細資訊及準備受控映像的步驟，請參閱下一節。
  - 針對您建立的每個集區使用唯一的自訂映像。
  - 若要使用 Batch API 以映像建立集區，請指定映像的**資源識別碼**，其形式為 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。 若要使用入口網站，請使用映像的**名稱**。  
  - 受控映像資源應該在集區存留期內都存在，以供集區調升規模，並可在刪除集區之後移除。

- **Azure Active Directory (AAD) 驗證**。 Batch 用戶端 API 必須使用 AAD 驗證。 Azure Batch 對於 AAD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。

## <a name="prepare-a-custom-image"></a>準備自訂映像

在 Azure 中，您可以從 Azure VM 之 OS 和資料磁碟的快照集、從含有受控磁碟的一般化 Azure VM，或從您所上傳的一般化內部部署 VHD，準備受控映像。 若要使用自訂映像來可靠地調整 Batch 集區，建議您「只」  使用第一個方法：使用 VM 磁碟的快照集。 請參閱下列步驟來準備 VM、建立快照集，然後從快照集建立映像。

### <a name="prepare-a-vm"></a>準備 VM

如果您要建立新的 VM 映像，請使用 批次所支援作為您的受控映像基底映像的第一個合作對象 Azure Marketplace 映像。 只有第一方映像可用來當做基底映像。 若要取得 Azure Batch 支援的 Azure Marketplace 映像參考的完整清單，請參閱[清單節點代理程式 Sku](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)作業。

> [!NOTE]
> 您無法使用具有額外授權和購買條款的第三方映像作為您的基礎映像。 如需這些 Marketplace 映像的相關資訊，請參閱 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 的指導方針。


* 確定使用受控磁碟來建立 VM。 當您建立 VM 時，這是預設的儲存體設定。
* 不要在 VM 上安裝 Azure 延伸模組，例如「自訂指令碼」延伸模組。 如果映像包含預先安裝的延伸模組，則 Azure 在部署 Batch 集區時可能會遇到問題。
* 當使用連接的資料磁碟時，您要掛接和格式化的磁碟內的 VM，以使用它們。
* 確定您提供的基本 OS 映像使用預設的暫存磁碟機。 Batch 節點代理程式目前需要有預設的暫存磁碟機。
* 一旦 VM 開始執行之後，請透過 RDP (適用於 Windows) 或 SSH (適用於 Linux) 向它連線。 安裝任何必要的軟體或複製所需的資料。  

### <a name="create-a-vm-snapshot"></a>建立 VM 快照集

快照集是完整的 VHD 唯讀複本。 若要建立 VM 之 OS 或資料磁碟的快照集，您可以使用 Azure 入口網站或命令列工具。 如需了解建立快照集的步驟與選項，請參閱適用於 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指導方針。

### <a name="create-an-image-from-one-or-more-snapshots"></a>從一或多個快照集建立映像

若要從快照集建立受控映像，請使用 Azure 命令列工具，例如 [az image create](/cli/azure/image) 命令。 您可以指定 OS 磁碟快照集，以及視需要指定一或多個資料磁碟快照集，來建立映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 入口網站中從自訂映像建立集區

在您儲存自訂映像並知道其資源識別碼或名稱之後，請從該映像建立 Batch 集區。 下列步驟顯示如何從 Azure 入口網站建立集區。

> [!NOTE]
> 如果您要使用其中一個 Batch API 建立集區，請確定您用於 AAD 驗證的身分識別具備映像資源的權限。 請參閱[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)。
>
> 受管理的映像的資源集區的存留期必須存在。 如果基礎的資源遭到刪除，無法調整集區。 

1. 在 Azure 入口網站中瀏覽至您的 Batch 帳戶。 此帳戶必須與包含自訂映像的資源群組位於相同的訂用帳戶和區域中。 
2. 在左側的 [設定]  視窗中，選取 [集區]  功能表項目。
3. 在 [集區]  視窗中，選取 [新增]  命令。
4. 在 [新增集區]  視窗上，從 [映像類型]  下拉式清單選取 [自訂映像 (Linux/Windows)]  。 從 [自訂 VM 映像]  下拉式清單中，選取映像名稱 (資源識別碼的簡短形式)。
5. 依據您的自訂映像選取正確的**發行者/提供項目/SKU**。
6. 指定其餘的必要設定，包括**節點大小**、**目標專用的節點**和**低優先權的節點**，以及所需的任何選擇性設定。

    例如，針對 Microsoft Windows Server Datacenter 2016 自訂映像，[新增集區]  視窗隨即顯示，如下所示：

    ![從自訂 Windows 映像新增集區](media/batch-custom-images/add-pool-custom-image.png)
  
若要檢查現有的集區是否以自訂映像作為基礎，請參閱 [集區]  視窗的資源摘要區段中的 **Operating System** 屬性。 如果已從自訂映像建立集區，它就會設定為**自訂 VM 映像**。

與集區相關聯的所有自訂映像都會顯示在集區的 [屬性]  視窗中。

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果您打算使用自訂映像來建立含有數百個或更多 VM 的集區，請務必依照先前的指導方針，使用從 VM 快照集建立的映像。

另請注意下列事項：

- **大小限制** - 當您使用自訂映像時，Batch 會將集區大小限制在 2500 個專用計算節點，或 1000 個低優先順序節點。

  如果您使用同一個映像 (或以相同基礎快照集為基礎的多個映像) 來建立多個集區，則集區中的計算節點總數不可超過上述限制。 我們不建議將一個映像或其基礎快照集用於多個集區。

  如果您使用[輸入 NAT 集區](pool-endpoint-configuration.md)來設定集區，則可能會降低限制。

- **調整逾時** - 如果您的集區包含固定數目的節點 (不自動調整規模)，請將集區的 resizeTimeout 屬性值提高到 20-30 分鐘之類的值。 如果您的集區在逾時期間內未達到其目標大小，請執行另一個[調整大小作業](/rest/api/batchservice/pool/resize)。

  如果您規劃的集區含有超過 300 個計算節點，您可能需要多次調整集區大小，才能達到目標大小。

## <a name="considerations-for-using-packer"></a>使用 Packer 的考量

直接使用 Packer 建立受控映像資源才會使用使用者訂用帳戶模式的 Batch 帳戶。 針對 Batch 服務模式的帳戶，您必須先建立 VHD，然後將 VHD 匯入至受管理的映像資源。 根據集區配置模式 （使用者訂用帳戶或批次服務），您的步驟來建立受控映像資源而異。

請確定用來建立受控映像的資源存在的任何參考的自訂映像的集區的存留期。 若要這樣做的失敗可以會造成集區配置失敗及/或調整大小失敗。 

如果已移除映像或基礎資源，您可能會收到錯誤類似於： `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果發生這種情況，請確定尚未移除基礎的資源。

如需有關使用 Packer 來建立 VM 的詳細資訊，請參閱 <<c0> [ 建立 Linux 映像使用 Packer](../virtual-machines/linux/build-image-with-packer.md)或是[建置 Windows 映像使用 Packer](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>後續步驟

- 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
