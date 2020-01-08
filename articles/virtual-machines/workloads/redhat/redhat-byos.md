---
title: Red Hat Enterprise Linux 攜帶您自己訂用帳戶的映射 |Microsoft Docs
description: 瞭解 Azure 上 Red Hat Enterprise Linux 的自備訂用帳戶映射
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486504"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux 在 Azure 中攜帶您自己的訂用帳戶映射
Red Hat Enterprise Linux （RHEL）映射可透過隨用隨付（PAYG）或自備訂用帳戶（BYOS）模型在 Azure 中使用。 本檔概要說明 Azure 中的 BYOS 映射。

## <a name="important-points-to-consider"></a>需要考慮的要點

- 此程式中提供的 RHEL BYOS 映射是已準備好用於生產環境的 RHEL 映射，與 Azure 資源庫/Marketplace 中的 RHEL PAYG 映射類似。 取得影像的註冊程式處於預覽階段。

- 這些映射遵循我們在[Azure 上 Red Hat Enterprise Linux 映射](./redhat-images.md)中所述的目前原則

- 標準支援原則適用于從這些映射建立的 Vm

- 從 RHEL BYOS 映射布建的 Vm 不會包含與 RHEL PAYG 映射相關聯的 RHEL 費用

- 映射已未獲授權，因此您必須使用訂用帳戶管理員來註冊並訂閱 Vm，以直接從 Red Hat 取得更新

- 目前無法在 Linux 映射的 BYOS 和 PAYG 計費模型之間動態切換。 需要從個別映射重新部署 VM，才能切換計費模型

- 這些 RHEL BYOS 映射支援 Azure 磁碟加密（ADE）。 ADE 支援目前為預覽狀態。 您必須先使用訂用帳戶管理員向 Red Hat 註冊，才能設定 ADE。 註冊之後，若要設定 ADE，請參閱[啟用 Linux IaaS vm 的 Azure 磁碟加密。](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- 雖然映射不會改變（除了標準的更新和修補程式之外），但註冊程式仍處於預覽階段，並會進一步改善流程以簡化程式

- 無論最終的執行方式為何，您都可以完全控制已從這些映射或其快照集布建的 Vm

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>存取 RHEL BYOS 映射的需求和條件

1. 熟悉[Red Hat 雲端存取計畫](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)條款，並在[Red Hat 的雲端存取註冊頁面](https://access.redhat.com/cloude/manager/image_imports/new)中註冊。

1. 完成[RHEL BYOS 存取要求表單](https://aka.ms/rhel-byos)。 您必須擁有您的 Red Hat 帳戶號碼，以及您用來存取 RHEL BYOS 映射的 Azure 訂用帳戶。

1. 經 Red Hat 和 Microsoft 審查並核准之後，您的 Azure 訂用帳戶就會啟用影像存取。

### <a name="expected-time-for-image-access"></a>影像存取的預期時間

當您完成 RHEL BYOS 表單並接受條款時，Red Hat 會在三個工作天內驗證您 BYOS 影像的資格，如果有效，您就能在一整天的一個工作天內收到 BYOS 影像的存取權。

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>從 Azure 入口網站使用 RHEL BYOS 映射

1. 在您的訂用帳戶啟用 RHEL BYOS 映射之後，您可以流覽至 [**建立資源**]，然後**查看 [全部**]，在 [Azure 入口網站](https://portal.azure.com)中找到它。

1. 在頁面頂端，您會看到您有私用供應專案。

    ![Marketplace 私人優惠](./media/rhel-byos-privateoffers.png)

1. 您可以按一下紫色連結，或向下流覽至頁面底部，以查看您的私用供應專案。

1. 在 UI 中的其餘部分將與任何其他現有的 Red Hat 映射並無不同。 選擇您的 RHEL 版本，並遵循提示來布建您的 VM。 此程式也可讓您在最後一個步驟中接受映射的條款。

>[!NOTE]
>到目前為止，這些步驟將不會啟用您的 RHEL BYOS 映射以進行程式設計部署，如下列「其他資訊」一節所述，將需要額外的步驟。

本檔的其餘部分著重于在映射上布建和接受條款的 CLI 方法。 與最終結果（已布建的 RHEL BYOS VM）相關，UI 和 CLI 可完全互換。

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>使用 Azure CLI 中的 RHEL BYOS 映射
下列指示集將逐步引導您使用 Azure CLI 來完成 RHEL VM 的初始部署程式。 這些指示假設您已[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

>[!IMPORTANT]
>請務必在所有下列命令的發行者、供應專案、計畫和映射參考中使用所有小寫字母

1. 檢查您是否在所要的訂用帳戶中：
    ```azurecli
    az account show -o=json
    ```

1. 為您的 RHEL BYOS VM 建立資源群組：
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受影像詞彙：
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >針對每個*Azure 訂用帳戶，每個映射 SKU 必須接受一次*這些條款。

1. 選擇性使用下列命令來驗證您的 VM 部署：
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. 執行與上述相同的命令，而不使用 `--validate` 引數來布建您的 VM：
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. 透過 SSH 連線到您的 VM，並確認您有未獲授權映射。 若要這麼做，請執行 `sudo yum repolist`。 輸出會要求您使用訂用帳戶管理員向 Red Hat 註冊 VM。

## <a name="additional-information"></a>其他資訊
- 如果您嘗試在未針對此供應專案啟用的訂用帳戶上布建 VM，您會收到下列錯誤，而且您應洽詢 Microsoft 或 Red Hat 以啟用您的訂用帳戶。
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- 如果您從 RHEL BYOS 映射建立快照集，並在[共用映射資源庫](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)中發佈映射，則必須提供符合快照的原始來源的計畫資訊。 例如，命令看起來可能像是（請注意最後一行中的計畫參數）：
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- 如果您使用自動化從 RHEL BYOS 映射布建 Vm，您將需要提供類似上述內容的方案參數。 例如，如果您使用 Terraform，您會在[計畫區塊](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供計畫資訊。

## <a name="next-steps"></a>後續步驟
* 深入瞭解[Azure Red Hat 更新基礎結構](./redhat-rhui.md)。
* 若要深入了解 Azure 中的 Red Hat 映像，請移至[文件頁面](./redhat-images.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。