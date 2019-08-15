---
title: 使用共用映射資源庫來建立集區 Azure Batch |Microsoft Docs
description: 使用共用映射資源庫建立 Batch 集區, 以將自訂映射布建到包含應用程式所需軟體和資料的計算節點。 自訂映像是設定計算節點以執行 Batch 工作負載的有效方式。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036702"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>使用共用映射資源庫來建立集區

當您使用虛擬機器設定建立 Azure Batch 集區時，需指定 VM 映像，以提供集區中每個計算節點的作業系統。 您可以使用支援的 Azure Marketplace 映射來建立虛擬機器的集區, 或使用[共用映射資源庫](../virtual-machines/windows/shared-image-galleries.md)建立自訂映射。

## <a name="benefits-of-the-shared-image-gallery"></a>共用映射資源庫的優點

當您使用自訂映射的共用映射資源庫時, 您可以控制作業系統類型和設定, 以及資料磁片的類型。 您的共用映射可以包含應用程式和參考資料, 一旦布建之後, 所有 Batch 集區節點就會變成可用。

您也可以視環境需要有多個版本的映射。 當您使用映射版本來建立 VM 時, 會使用映射版本來為 VM 建立新的磁片。 

使用共用映射可節省準備集區計算節點以執行 Batch 工作負載的時間。 在布建之後, 您可以使用 Azure Marketplace 映射並在每個計算節點上安裝軟體, 但使用共用映射通常會更有效率。 此外, 您可以為共用映射指定多個複本, 因此當您建立具有許多 Vm 的集區 (超過600個 Vm) 時, 您會節省建立集區的時間。

使用為您的案例設定的共用映射可提供幾項優點:

* **跨區域使用相同的影像。** 您可以跨不同區域建立共用映射複本, 讓您的所有集區都能使用相同的映射。
* **設定作業系統 (OS)。** 您可以自訂映像作業系統磁碟的設定。
* **預先安裝應用程式。** 在布建具有啟動工作的計算節點之後, 在 OS 磁片上預先安裝應用程式比安裝應用程式更有效率且較不容易發生錯誤。
* **複製大量資料一次。** 將靜態資料複製到受控映射的資料磁片, 使其成為受管理共用映射的一部分。 這只需要執行一次，就能將資料提供給集區的每個節點。
* **將集區成長至較大的大小。** 使用共用映射資源庫, 您可以使用自訂映射和更多共用映射複本來建立較大的集區。
* **比自訂映射更好的效能。** 使用共用映射, 集區達到穩定狀態所花費的時間, 最快可達 25%, 且 VM 閒置延遲最高可達 30%。
* **映射版本設定和群組, 以方便管理。** 映射群組定義包含建立映射的原因、作業系統的目標, 以及使用映射的相關資訊。 群組影像可讓您更輕鬆地管理映射。 如需詳細資訊, 請參閱[影像定義](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>必要條件

* **Azure Batch 帳戶**。 若要建立 Batch 帳戶, 請參閱使用[Azure 入口網站](quick-create-portal.md)或[Azure CLI](quick-create-cli.md)的 batch 快速入門。

* **共用映射庫映射**。 如需準備共用映射的詳細資訊和步驟, 請參閱使用[Azure CLI 建立共用映射資源庫](../virtual-machines/linux/shared-images.md)或[使用 Azure 入口網站建立共用映射資源庫](../virtual-machines/linux/shared-images-portal.md)。

> [!NOTE]
> 您的共用映射必須與 Batch 帳戶位於相同的訂用帳戶中。 您的共用映射可以位於不同的區域中, 只要其複本位於與 Batch 帳戶相同的區域中。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 從共用映射建立集區

若要使用 Azure CLI 從共用映射建立集區, 請使用`az batch pool create`命令。 在`--image`欄位中指定共用映射識別碼。 請確定 OS 類型和 SKU 符合所指定的版本`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用從共用映射建立集區C#

或者, 您可以使用C# SDK, 從共用映射建立集區。

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="considerations-for-large-pools"></a>大型集區的考量

如果您打算使用共用映射來建立具有數百部或數千部 Vm 的集區, 請使用下列指導方針。

* **共用映射資源庫複本編號。**  針對最多600個實例的每個集區, 建議您至少保留一個複本。 例如, 如果您要建立包含 3000 Vm 的集區, 您應該至少保留映射的5個複本。 我們一律建議保留比最低需求更多的複本, 以獲得更好的效能。

* **調整 Timeout 大小**如果您的集區包含固定數目的節點 (如果不自動調整), 請`resizeTimeout`根據集區大小來增加集區的屬性。 針對每個 1000 Vm, 建議的調整大小時間至少為15分鐘。 例如, 使用 2000 Vm 的集區建議大小設定時間至少為30分鐘。

## <a name="next-steps"></a>後續步驟

* 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
