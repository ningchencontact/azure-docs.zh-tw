---
title: "升級傳統 Azure 容器登錄中"
description: "利用擴充功能集的 Basic、 Standard 和 Premium 受容器登錄升級您 unmanaged 的傳統容器登錄中。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 19090bb69d7165c1e904450dc93b925e23e44782
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-a-classic-container-registry"></a>升級傳統容器登錄中

Azure 容器登錄 (ACR) 是適用於數個服務層，[稱為 Sku](container-registry-skus.md)。 初始版本的 ACR 提供單一的 SKU，傳統缺少於 Basic、 Standard 和 Premium Sku 固有的數種功能 (通稱為*管理*登錄)。 這篇文章說明如何將未受管理的傳統登錄移轉至受管理的 Sku 的其中一個，以便您可以利用其增強的功能集。

## <a name="why-upgrade"></a>為何要升級？

因為不受管理的傳統登錄功能有限，我們建議所有傳統登錄都應該升級至基本、 標準或進階的受管理的登錄。 這些較高層級的 SKU 會將登錄更深入整合到 Azure 功能。

提供受管理的登錄：

* Azure Active Directory 整合[個別登入](container-registry-authentication.md#individual-login-with-azure-ad)
* 映像和標記刪除支援
* [異地複寫](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

「傳統」登錄大部分取決於您在建立登錄時，Azure 自動佈建在您 Azure 訂用帳戶中的儲存體帳戶。 相反地，「基本」、「標準」和「進階」SKU 會利用 *受控儲存體*。 也就是說，Azure 會明確地管理映像儲存體；在您自己的訂用帳戶中，不會建立不同的儲存體帳戶。

受管理的登錄儲存體有下列優點：

* 容器映像是進行[待用加密](../storage/common/storage-service-encryption.md)。
* 映像是使用[異地備援儲存體](../storage/common/storage-redundancy.md#geo-redundant-storage)所儲存，並確保使用多區域複寫來備份映像。
* 能夠自由地[Sku 之間移動](container-registry-skus.md#changing-skus)，當您選擇較高層級的 SKU 啟用更高的輸送量。 運用每個 SKU，ACR 即可在您的需求增加時滿足輸送量需求。
* 登錄和其儲存體的整合的安全性模型會提供簡化的權限管理。 您管理的權限只容器登錄中，而不需要也管理不同的儲存體帳戶的權限。

## <a name="migration-considerations"></a>移轉考量

當您變更傳統登錄到受管理的登錄時，Azure 必須複製所有現有的容器映像從您的訂用帳戶中的 ACR 建立儲存體帳戶到受 Azure 儲存體帳戶。 根據您的登錄的大小，此程序可能需要幾分鐘的時間，到數小時。

在轉換過程中，所有`docker push`會封鎖作業，而`docker pull`仍持續運作。

請勿刪除或修改備份傳統登錄在轉換過程中的儲存體帳戶的內容。 如此一來，可能會導致您的容器映像損毀。

移轉完成之後，ACR 會再使用原本支援傳統登錄您訂用帳戶的儲存體帳戶。 我們已經確認移轉成功之後，請考慮刪除儲存體帳戶，以協助將成本降至最低。

>[!IMPORTANT]
> 從傳統升級至其中一個受管理的 Sku 是**單向的程序**。 一旦在您轉換傳統登錄為 Basic、 Standard 或 Premium，您也無法還原成 傳統。 您可以不過，自由地之間移動 managed Sku 登錄足夠的容量。

## <a name="how-to-upgrade"></a>如何升級

您可以為其中一個受管理的 Sku 數種方式升級未受管理的傳統登錄。 在下列章節中，我們說明使用的程序[Azure CLI] [ azure-cli]和[Azure 入口網站][azure-portal]。

## <a name="upgrade-in-azure-cli"></a>在 Azure CLI 升級

若要升級的傳統登錄 Azure CLI 中，執行[az acr 更新][ az-acr-update]命令，並指定登錄新的 SKU。 在下列範例中，傳統登錄名為*myclassicregistry*升級至 Premium SKU:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

完成移轉時，您應該會看到類似下列的輸出。 請注意，`sku`是 「 高階 」 和`storageAccount`是"null"，表示 Azure 現在管理此登錄的映像儲存體。

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

如果您指定的受管理的登錄 SKU 的最大容量小於傳統系統登錄的大小，您會收到類似下面的錯誤訊息。

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

如果您收到類似的錯誤，請執行[az acr 更新][ az-acr-update]命令一次，並指定建議的 SKU，也就是下一個最高等級可容納您的映像的 SKU。

## <a name="upgrade-in-azure-portal"></a>Azure 入口網站中的 < 升級

當您升級使用 Azure 入口網站的傳統登錄時，Azure 會自動選取最低層級 SKU 可容納您的映像。 例如，如果您的登錄包含 12 GiB 映像中的，Azure 自動選取，並將轉換為標準的傳統登錄 （100 GiB 上限）。

若要升級使用 Azure 入口網站的傳統登錄，瀏覽至容器登錄**概觀**選取**升級到受管理的登錄**。

![傳統登錄升級 Azure 入口網站 UI 中的按鈕][update-classic-01-upgrade]

選取**確定**以確認您想要升級到受管理的登錄。

![傳統登錄升級確認在 Azure 入口網站 UI][update-classic-02-confirm]

在移轉期間，入口網站表示登錄**佈建狀態**是*更新*。 如先前所述，`docker push`作業會移轉期間，請停用和絕對不能刪除或更新由傳統登錄移轉正在進行-執行這項作業時使用的儲存體帳戶可能會導致影像損毀。

![傳統登錄升級 Azure 入口網站 UI 中的進度][update-classic-03-updating]

移轉完成時，**佈建狀態**指出*Succeeded*，您可以再次`docker push`登錄檔。

![傳統登錄升級 Azure 入口網站 UI 中的完成狀態][update-classic-04-updated]

## <a name="next-steps"></a>後續步驟

一旦您已升級傳統登錄為 Basic、 Standard 或 Premium，Azure 將不會再使用原本支援傳統登錄儲存體帳戶。 若要減少成本，請考慮刪除儲存體帳戶或帳戶，其中包含舊的容器映像中的 Blob 容器。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com