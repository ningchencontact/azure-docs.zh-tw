---
title: 將傳統的 Azure 容器登錄升級
description: 將非受控的傳統容器登錄升級，以利用基本、標準和進階受控容器登錄的擴充功能集。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310384"
---
# <a name="upgrade-a-classic-container-registry"></a>將傳統的容器登錄升級

有好幾個服務層級 ([也就是所謂的 SKU](container-registry-skus.md)) 會提供 Azure Container Registry (ACR)。 初始的 ACR 版本提供了單一 SKU (傳統)，該 SKU 缺少基本、標準和進階 SKU (統稱為「受控」  登錄) 固有的數種功能。

傳統 SKU 即將淘汰, 將在2019年4月後無法使用。 本文將詳細說明如何將非受控的傳統登錄移轉至其中一個受控 SKU，以便利用其經過強化的功能集。

## <a name="why-upgrade"></a>為何要升級？

傳統登錄 SKU 即將**淘汰**, 將在**2019 年4月**後無法使用。 所有現有的傳統登錄都應該在2019年4月之前升級。 傳統登錄的入口網站管理功能將會被淘汰。建立新的傳統登錄將于2019年4月後停用。

基於傳統非受控登錄的已規劃淘汰和有限功能, 所有傳統登錄都應該升級為受控登錄 (Basic、Standard 或 Premium)。 這些較高層級的 SKU 會將登錄更深入整合到 Azure 功能。 如需不同服務層之價格和功能的詳細資訊, 請參閱[Container Registry sku](container-registry-skus.md)。

「傳統」登錄依賴您在建立登錄時，Azure 自動佈建在您 Azure 訂用帳戶中的儲存體帳戶。 相比之下，基本、標準和進階 SKU 會以透明的方式處理您的映像儲存體來利用 Azure 的[進階儲存體功能](container-registry-storage.md)。 您自己的訂用帳戶中未建立個別儲存體帳戶。

受控登錄儲存體可提供下列優點：

* 容器映像是進行[待用加密](container-registry-storage.md#encryption-at-rest)。
* 影像會使用[異地多餘的儲存體](container-registry-storage.md#geo-redundant-storage)儲存, 以確保您可以使用多重區域複寫來備份映射 (僅限 Premium SKU)。
* 自由地[在 SKU 之間移動](container-registry-skus.md#changing-skus)的能力，讓您在選擇較高層級的 SKU 時獲得較高的輸送量。 運用每個 SKU，ACR 即可在您的需求增加時滿足輸送量需求。
* 適用於登錄和其儲存體的整合安全性模型能提供簡化的權限管理功能。 您只負責管理容器登錄的權限，而不必還要管理不同儲存體帳戶的權限。

如需 ACR 中映像儲存體的其他相關詳細資料，請參閱 [Azure Container Registry 中的容器映像儲存體](container-registry-storage.md)。

## <a name="migration-considerations"></a>移轉考量

當您將傳統登錄升級為受控登錄時, Azure 必須將所有現有容器映射從您訂用帳戶中 ACR 建立的儲存體帳戶複製到 Azure 所管理的儲存體帳戶。 視登錄大小而定，此程序所需時間從數分鐘到數小時不等。 基於估計目的, 每分鐘預期大約 0.5 GiB 的遷移時間。

在轉換過程中, `docker push`作業會在前 10% 的遷移期間停用。 `docker pull`會繼續正常運作。

在轉換過程中，請勿刪除或修改支援傳統登錄之儲存體帳戶的內容。 如果您這麼做，可能會導致容器映像損毀。

完成遷移之後, Azure Container Registry 不會再使用您訂用帳戶中原本支援傳統登錄的儲存體帳戶。 在確認移轉成功之後，請考慮刪除該儲存體帳戶，以協助將成本降至最低。

>[!IMPORTANT]
> 從傳統升級為其中一個受控 SKU 是**單向程序**。 傳統登錄一旦轉換為基本、標準或進階，就無法還原為傳統。 不過，您可以在容量足供登錄使用的受控 SKU 之間自由地移動。

## <a name="how-to-upgrade"></a>如何升級

您可以透過數種方式將非受控的傳統登錄升級為其中一個受控 SKU。 在下列各節中, 我們會說明使用[Azure CLI][azure-cli] and the [Azure portal][azure-portal]的程式。

## <a name="upgrade-in-azure-cli"></a>在 Azure CLI 中升級

若要升級 Azure CLI 中的傳統登錄, 請執行[az acr update][az-acr-update]命令並指定登錄的新 SKU。 在下列範例中，名為 myclassicregistry  的傳統登錄會升級為進階 SKU：

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

當移轉完成時，您應該會看到類似下面的輸出。 請注意, `storageAccount` `null`是 "Premium", 而是, 表示 Azure 現在會管理此登錄的`sku`映射儲存體。

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

如果您收到類似的錯誤, 請再次執行[az acr update][az-acr-update]命令並指定建議的 sku, 這是可容納您映射的下一個最高層級 sku。

## <a name="upgrade-in-azure-portal"></a>在 Azure 入口網站中升級

當您使用 Azure 入口網站升級傳統登錄時, Azure 會自動選取 [標準] 或 [Premium] SKU, 視哪一個 SKU 可容納您的映射而定。 例如, 如果您的登錄在映射中包含少於100的 GiB, Azure 就會自動選取並將傳統登錄轉換成標準 (100 GiB 最大值)。

若要使用 Azure 入口網站來為傳統登錄升級，請瀏覽至容器登錄 [概觀]  ，然後選取 [升級為受控登錄]  。

![Azure 入口網站 UI 中的傳統登錄升級按鈕][update-classic-01-upgrade]

選取 [確定]  以確認您要升級為受控登錄。

在移轉期間，入口網站會指出登錄的 [佈建狀態]  是 [更新中]  。 如先前所述`docker push` , 作業會在前 10% 的遷移期間停用。 當遷移進行中時, 請勿刪除或更新傳統登錄所使用的儲存體帳戶--這麼做可能會導致映射損毀。

![Azure 入口網站 UI 中的傳統登錄升級進度][update-classic-03-updating]

當遷移完成時, 布建**狀態**會指出 [*成功*], 而且您可以使用登錄繼續正常操作。

![Azure 入口網站 UI 中的傳統登錄升級完成狀態][update-classic-04-updated]

## <a name="next-steps"></a>後續步驟

將傳統登錄升級為受控登錄之後, Azure 就不再使用原本支援傳統登錄的儲存體帳戶。 若要降低成本，請考慮刪除儲存體帳戶或帳戶內包含舊容器映像的 Blob 容器。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com