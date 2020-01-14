---
title: Azure 上的 Red Hat 工作負載概觀 | Microsoft Docs
description: 了解 Azure 上可用的 Red Hat 產品供應項目
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454972"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上的 Red Hat 工作負載
Red Hat 工作負載可透過 Azure 上的各種供應項目來獲得支援。 和 Red Hat 更新基礎結構 (RHUI) 一樣，Red Hat Enterprise Linux (RHEL) 映像位於 RHEL 工作負載的核心。

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux (RHEL) 映像
Azure 上提供了各式各樣的 RHEL 映像。 這些映像可透過兩種不同的授權模型來取得：隨用隨付 (PAYG) 和自備訂用帳戶 (BYOS)。 Azure 上的新 RHEL 映像會在有新發行的 RHEL 版本時發佈，並視需要在其生命週期內進行更新。

### <a name="pay-as-you-go-payg-images"></a>隨用隨付 (PAYG) 映像
Azure 會提供各種不同的 RHEL PAYG 映像。 這些映像在推出時便已獲得正確的 RHEL 權限，並且會連結至更新的來源 (Red Hat 更新基礎結構)。 這些映像會針對 RHEL 權利和更新收取進階費用。 RHEL PAYG 映像變體包含：
* 標準 RHEL
* RHEL for SAP
* RHEL for SAP (含高可用性和更新服務)。

如果您不想要煩惱是否要另外付費來取得適當數量的訂用帳戶，則可以使用 PAYG 映像。

### <a name="bring-your-own-subscription-byos-images"></a>自備訂用帳戶 (BYOS) 映像
Azure 也提供 RHEL BYOS 映像。 對於已有 Red Hat 訂用帳戶且想要在 Azure 中使用這些訂用帳戶的客戶而言，這些映像可能會很有用。 您必須先將現有訂用帳戶轉換成雲端存取訂用帳戶，才能在 Azure 中使用這些訂用帳戶。 只有當 Red Hat 確認您有足夠的雲端存取訂用帳戶時，才會授與您這些映像的存取權。 使用這些映像可讓客戶避免因使用 PAYG 映像而可能產生的重複計費情形。 您可以在[這裡](https://aka.ms/rhel-byos)要求 BYOS 映像的存取權。

> [!NOTE]
> 關於重複計費的注意事項：當使用者針對 RHEL 訂用帳戶支付兩次費用時，就會產生重複計費。 這種情況通常發生在客戶使用 subscription-manager 來連結 RHEL PAYG VM 上的權利時。 例如，若客戶使用 subscription-manager 來連結 RHEL PAYG 映像上 SAP 套件的權利，便會間接發生重複計費的情形，原因是其會針對 RHEL 支付兩次費用，一次是透過 PAYG 進階費用，一次則是透過其 SAP 訂用帳戶。 BYOS 映像的使用者則不會發生這種情況。

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat 更新基礎結構 (RHUI)
Azure 僅針對 PAYG RHEL 虛擬機器 (VM) 提供 Red Hat 更新基礎結構。 RHUI 實際上是 Red Hat CDN 的鏡像，但只能供 Azure PAYG RHEL VM 存取。 視已部署的 RHEL 映像而定，您將可以存取適當的套件。 例如，RHEL for SAP 映像除了可存取基底 RHEL 套件外，也可以存取 SAP 套件。

### <a name="rhui-update-behavior"></a>RHUI 更新行為
根據預設，連線至 RHUI 的 RHEL 映像將會在執行 `yum update` 時，更新至最新的 RHEL 次要版本。 此行為表示，如果 RHEL 7.4 VM 上執行了 `yum update` 作業，便可能會升級至 RHEL 7.7。 這是 RHUI 的設計。 不過，您可以藉由從一般 RHEL 存放庫切換到[擴充更新支援 (EUS) 存放庫](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)來減輕此升級行為。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Azure 上的 RHEL 映像](./redhat-images.md)
* 深入了解 [Red Hat 更新基礎結構](./redhat-rhui.md)
* 深入了解 [RHEL BYOS 供應項目](./redhat-byos.md)