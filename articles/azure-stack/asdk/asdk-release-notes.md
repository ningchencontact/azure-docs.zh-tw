---
title: Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs
description: Azure Stack 開發套件的增強功能、修正及已知問題。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915878"
---
# <a name="asdk-release-notes"></a>ASDK 版本資訊

本文提供 Azure Stack 開發套件 (ASDK) 中變更、修正及已知問題的相關資訊。 如果您不確定所執行的版本，可以使用[入口網站來進行檢查](../azure-stack-updates.md#determine-the-current-version)。

請訂閱 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [摘要](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，以便隨時收到 ASDK 的最新訊息。

## <a name="build-1903"></a>組建 1903

1903 承載不包含 ASDK 版本。

## <a name="build-11902069"></a>組建 1.1902.0.69

### <a name="new-features"></a>新功能

- 1902 組建在 Azure Stack 系統管理員入口網站上導入了新的使用者介面，用以建立方案、供應項目、配額和附加方案。 如需詳細資訊 (包括螢幕擷取畫面)，請參閱[建立方案、供應項目和配額](../azure-stack-create-plan.md)。

- 如需此版本中其他變更和改進功能的清單，請參閱 Azure Stack 版本資訊中的[這個小節](../azure-stack-update-1902.md#improvements)。

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>已知問題

- 已發現送至內部負載平衡器 (ILB) 的封包若超過 1450 位元組即會遭捨棄的已知問題。 此問題的起因是主機的 MTU 設定太低，不足以容納周遊特定角色的 VXLAN 封裝封包，而該角色自 1901 起已移至主機。 對您而言，我們認定至少有兩種情況可能出現此問題：

  - 對位於內部負載平衡器 (ILB) 後方的 SQL Always-On 進行 AQL 查詢，且查詢大小超過 660 位元組。
  - Kubernetes 部署在您嘗試啟用多個主機時失敗。  

  如果位於相同虛擬網路的 VM 與 ILB 互相通訊，但兩者分處於不同子網路，就會發生此問題。 您可以在 ASDK 主機上使用已提升權限的命令提示字元執行下列命令，以解決此問題：

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>組建 1.1901.0.95

請參閱 [Azure Stack 版本資訊中的重要組建資訊](../azure-stack-update-1901.md#build-reference)。

### <a name="changes"></a>變更

此組建包含下列適用於 Azure Stack 的改良功能：

- BGP 和 NAT 元件現在會部署於實體主機上。 這樣就不需要使用兩個公用或公司 IP 位址來部署 ASDK，而且也可簡化部署。
- Azure Stack 整合系統備份現在可以使用 **asdk-installer.ps1** PowerShell 指令碼來[驗證](asdk-validate-backup.md)。

### <a name="new-features"></a>新功能

- 如需此版本中新功能的清單，請參閱 Azure Stack 版本資訊的[這個小節](../azure-stack-update-1901.md#new-features)。

### <a name="fixed-and-known-issues"></a>已修正和已知問題

- 如需此版本中已修正問題的清單，請參閱 Azure Stack 版本資訊的[這個小節](../azure-stack-update-1901.md#fixed-issues)。 如需已知問題的清單，請參閱[這個小節](../azure-stack-update-1901.md#known-issues-post-installation)。
- 請注意，[可用的 Azure Stack Hotfix](../azure-stack-update-1901.md#azure-stack-hotfixes) 不適用 Azure Stack ASDK。

## <a name="build-118110101"></a>組建 1.1811.0.101

### <a name="changes"></a>變更

此組建包含下列適用於 Azure Stack 的改良功能：  

- 針對 ASDK，有一組新的最基本且建議的硬體與軟體需求。 這些新的建議規格記載於 [Azure Stack 部署規劃考量](asdk-deploy-considerations.md)。 由於 Azure Stack 平台已經演進，因此現在有更多服務可供使用，也可能需要更多資源。 增加的規格皆反映在這些已修訂的建議事項中。

### <a name="new-features"></a>新功能

如需此版本中新功能的清單，請參閱 Azure Stack 版本資訊的[這個小節](../azure-stack-update-1811.md#new-features)。

### <a name="fixed-and-known-issues"></a>已修正和已知問題

如需此版本中已修正問題的清單，請參閱 Azure Stack 版本資訊的[這個小節](../azure-stack-update-1811.md#fixed-issues)。 如需已知問題的清單，請參閱[這個小節](../azure-stack-update-1811.md#known-issues-post-installation)。
