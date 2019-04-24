---
title: 將傳統的 Azure 容器登錄升級
description: 將非受控的傳統容器登錄升級，以利用基本、標準和進階受控容器登錄的擴充功能集。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480303"
---
# <a name="upgrade-a-classic-container-registry"></a>將傳統的容器登錄升級

有好幾個服務層 ([也就是所謂的 SKU](container-registry-skus.md)) 會提供 Azure Container Registry (ACR)。 初始的 ACR 版本提供了單一 SKU (傳統)，該 SKU 缺少基本、標準和進階 SKU (統稱為「受控」登錄) 固有的數種功能。

经典 SKU 即将弃用，2019 年 4 月之后将不可用。 本文將詳細說明如何將非受控的傳統登錄移轉至其中一個受控 SKU，以便利用其經過強化的功能集。

## <a name="why-upgrade"></a>為何要升級？

经典注册表 SKU 即将**弃用**，**2019 年 4 月**之后将不可用。 应在 2019 年 4 月之前升级所有现有的经典注册表。 经典注册表的门户管理功能将被淘汰。在 2019 年 4 月之后，用于创建新的经典注册表的功能将被禁用。

由于经典非托管注册表的计划弃用和有限功能，所有经典注册表应升级到托管注册表（基本、标准或高级）。 這些較高層級的 SKU 會將登錄更深入整合到 Azure 功能。 有关不同服务层级的定价和功能的详细信息，请参阅[容器注册表 SKU](container-registry-skus.md)。

「傳統」登錄依賴您在建立登錄時，Azure 自動佈建在您 Azure 訂用帳戶中的儲存體帳戶。 相比之下，基本、標準和進階 SKU 會以透明的方式處理您的映像儲存體來利用 Azure 的[進階儲存體功能](container-registry-storage.md)。 您自己的訂用帳戶中未建立個別儲存體帳戶。

受控登錄儲存體可提供下列優點：

* 容器映像是進行[待用加密](container-registry-storage.md#encryption-at-rest)。
* 使用儲存映像[異地備援儲存體](container-registry-storage.md#geo-redundant-storage)，並確保您使用多區域複寫 (Premium SKU) 的映像的備份。
* 自由地[在 SKU 之間移動](container-registry-skus.md#changing-skus)的能力，讓您在選擇較高層級的 SKU 時獲得較高的輸送量。 運用每個 SKU，ACR 即可在您的需求增加時滿足輸送量需求。
* 適用於登錄和其儲存體的整合安全性模型能提供簡化的權限管理功能。 您只負責管理容器登錄的權限，而不必還要管理不同儲存體帳戶的權限。

如需 ACR 中映像儲存體的其他相關詳細資料，請參閱 [Azure Container Registry 中的容器映像儲存體](container-registry-storage.md)。

## <a name="migration-considerations"></a>移轉考量

将经典注册表升级到托管注册表时，Azure 必须将所有现有的容器映像从订阅中 ACR 创建的存储帐户复制到 Azure 托管的存储帐户中。 視登錄大小而定，此程序所需時間從數分鐘到數小時不等。 迁移时间是根据每分钟大约迁移 0.5 GiB 估算的。

在转换过程中，迁移操作还剩 10% 时，将禁用 `docker push` 操作。 `docker pull` 仍可正常运行。

在轉換過程中，請勿刪除或修改支援傳統登錄之儲存體帳戶的內容。 如果您這麼做，可能會導致容器映像損毀。

迁移完成后，Azure 容器注册表不再使用订阅中最初充当经典注册表后备的存储帐户。 在確認移轉成功之後，請考慮刪除該儲存體帳戶，以協助將成本降至最低。

>[!IMPORTANT]
> 從傳統升級為其中一個受控 SKU 是**單向程序**。 傳統登錄一旦轉換為基本、標準或進階，就無法還原為傳統。 不過，您可以在容量足供登錄使用的受控 SKU 之間自由地移動。

## <a name="how-to-upgrade"></a>如何升級

您可以透過數種方式將非受控的傳統登錄升級為其中一個受控 SKU。 在下列幾節中，我們會說明使用 [Azure CLI][azure-cli] 和 [Azure 入口網站][azure-portal]的程序。

## <a name="upgrade-in-azure-cli"></a>在 Azure CLI 中升級

若要在 Azure CLI 中將傳統登錄升級，請執行 [az acr update][az-acr-update] 命令，並為登錄指定新的 SKU。 在下列範例中，名為 myclassicregistry 的傳統登錄會升級為進階 SKU：

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

當移轉完成時，您應該會看到類似下面的輸出。 请注意，`sku` 为“高级”，`storageAccount` 为 `null`，表示 Azure 现在为此注册表管理映像存储。

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

如果您指定之受控登錄 SKU 的最大容量小於傳統登錄的大小，您會收到類似下面的錯誤訊息。

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

如果您收到類似錯誤，請再次執行 [az acr update][az-acr-update] 命令並指定所建議的 SKU (也就是可容納映像的下一個最高等級 SKU)。

## <a name="upgrade-in-azure-portal"></a>在 Azure 入口網站中升級

使用 Azure 门户升级经典注册表时，Azure 会自动选择标准或高级 SKU，具体取决于哪个 SKU 可以容纳映像。 例如，如果注册表包含 100 GiB 以下的映像，Azure 会自动选择经典注册表并将其转换为标准注册表（最大容量为 100 GiB）。

若要使用 Azure 入口網站來為傳統登錄升級，請瀏覽至容器登錄 [概觀]，然後選取 [升級為受控登錄]。

![Azure 入口網站 UI 中的傳統登錄升級按鈕][update-classic-01-upgrade]

選取 [確定] 以確認您要升級為受控登錄。

在移轉期間，入口網站會指出登錄的 [佈建狀態] 是 [更新中]。 如前所述，迁移操作还剩 10% 时，将禁用 `docker push` 操作。 迁移正在进行时，请不得删除或更新经典注册表使用的存储帐户，否则可能导致映像损坏。

![Azure 入口網站 UI 中的傳統登錄升級進度][update-classic-03-updating]

迁移完成后，“预配状态”会指示“成功”，此时可以恢复注册表的正常操作。

![Azure 入口網站 UI 中的傳統登錄升級完成狀態][update-classic-04-updated]

## <a name="next-steps"></a>後續步驟

将经典注册表升级到托管注册表后，Azure 不再使用一开始充当经典注册表后备的存储帐户。 若要降低成本，請考慮刪除儲存體帳戶或帳戶內包含舊容器映像的 Blob 容器。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com