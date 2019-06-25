---
title: Azure 資訊安全中心的自適性網路強化 |Microsoft Docs
description: " 了解如何啟用 Azure 資訊安全中心的自適性網路強化。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: monhaber
ms.openlocfilehash: f35f410ddc039ee264fa1de317e152cb03f391b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241580"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 資訊安全中心的自適性網路強化
了解如何設定 Azure 資訊安全中心的自適性網路強化。

## <a name="what-is-adaptive-network-hardening"></a>什麼是自適性網路強化？
套用[網路安全性群組 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)來篩選流量的資源，可改善網路安全性的情勢。 不過，可以仍有某些情況下，實際的流量流經 NSG 的 NSG 規則定義的子集。 在這些情況下，進一步改善的安全性情勢，可藉由強化的 NSG 規則，根據實際的流量模式。

自適性網路強化提供建議以進一步加強的 NSG 規則。 它會使用機器學習演算法，其中通盤考量實際流量，已知的受信任的組態、 威脅情報，以及其他指標的危害，然後提供建議，可允許僅來自特定的 IP/連接埠 tuple 的流量。

例如，假設現有的 NSG 規則是允許從 140.20.30.10/24 連接埠 22 上的流量。 自適性網路強化的建議，根據分析，會縮小範圍，並允許流量從 140.23.30.10/29 – 也就是較窄的 IP 範圍，並拒絕所有其他流量到該連接埠。

![網路強化檢視](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> 自適性網路強化建議都支援下列連接埠：22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>檢視 自適性網路強化的警示和規則

1. 在 資訊安全中心中，選取**Networking** -> **調適性網路強化**。 Vm 網路列在三個不同的索引標籤：
   * **狀況不良的資源**:目前有建議和警示所執行的自適性網路強化的演算法所觸發的 Vm。 
   * **狀況良好的資源**:不使用警示和建議的 Vm。
   * **資源未經掃描便傳遞**:無法在執行的自適性網路強化的演算法，因為下列原因之一的 Vm:
      * **Vm 是傳統 Vm**:支援 Azure Resource Manager Vm。
      * **沒有足夠資料可供使用**:為了產生準確的流量，強化建議，資訊安全中心會需要至少 30 天的流量資料。
      * **VM 未受到 ASC 標準**:只會設定為 資訊安全中心標準定價層的 Vm 才適合這項功能。

     ![狀況不良的資源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 從**狀況不良的資源**索引標籤上，選取要檢視其警示和建議的強化規則来套用的 VM。

    ![強化的警示](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>檢閱並套用 自適性網路強化建議規則

1. 從**狀況不良的資源**索引標籤上，選取 VM。 會列出警示和建議的強化規則。

     ![堅固功能規則](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **規則**索引標籤會列出自適性網路強化建議您新增的規則。 **警示**索引標籤會列出流向的資源，不建議規則中允許的 IP 範圍內的流量，因為所產生的警示。

2. 如果您想要變更的某些規則的參數，您可以修改它，如所述[修改規則](#modify-rule)。
   > [!NOTE]
   > 您也可以[刪除](#delete-rule)或是[新增](#add-rule)規則。

3. 選取您想要套用 NSG，然後按一下 [規則]**強制執行**。

      > [!NOTE]
      > 強制執行的規則會新增至 nsg，以保護 VM。 （藉由其 NIC，相關聯的 NSG 或 VM 所在的子網路或兩者，VM 無法加以保護）

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### 修改規則  <a name ="modify-rule"> </a>

若要修改已建議規則的參數。 例如，您可能要變更建議的 IP 範圍。

一些重要指導方針修改的自適性網路強化的規則：

* 您可以修改 「 允許 」 規則 」 都只能的參數。 
* 您無法變更 「 允許 」 規則，成為 「 拒絕 」 規則。 

  > [!NOTE]
  > 建立和修改 「 拒絕 」 規則會對直接 NSG 的詳細資訊，請參閱[建立、 變更或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* A**拒絕所有流量**規則是會列在這裡，「 都拒絕 」 規則的唯一類型，而且無法修改。 不過，您可以刪除它 (請參閱[刪除規則](#delete-rule))。
  > [!NOTE]
  > A**拒絕所有流量**規則條件時，建議，如此一來執行演算法，資訊安全中心不會識別應允許，現有的 NSG 組態為基礎的流量。 因此，建議的規則是拒絕指定的連接埠的所有流量。 這種類型的規則名稱會顯示為 「*系統產生*"。 之後強制執行這項規則時，它在 NSG 中的實際名稱會是通訊協定、 流量方向、 「 拒絕 」 和一個隨機數字所組成的字串。

*若要修改的自適性網路強化的規則：*

1. 若要修改某些參數的規則，在**規則**索引標籤上，按一下規則的資料列結尾的三個點 （...） 上，按一下**編輯**。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 **編輯規則** 視窗中，更新您想要變更，然後按一下 詳細資料**儲存**。

   > [!NOTE]
   > 按一下後**儲存**，您已成功將變更的規則。 *不過，您有未套用到 NSG。* 若要套用它，您必須在清單中，選取的規則，按一下**強制執行**（為下一個步驟所述）。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要套用更新的規則，從清單中，選取 已更新的規則，然後按一下**強制執行**。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### 新增規則 <a name ="add-rule"> </a>

您可以加入不由資訊安全中心建議的 「 允許 」 規則。

> [!NOTE]
> 可以在這裡新增僅 「 允許 」 規則。 如果您想要新增 「 拒絕 」 規則，您可以對直接 NSG。 如需詳細資訊，請參閱 <<c0> [ 建立、 變更或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*若要新增的自適性網路強化的規則：*

1. 按一下 **新增規則**（位於左上角）。

   ![新增規則](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 **新的規則**視窗中，輸入詳細資料，然後按一下**新增**。

   > [!NOTE]
   > 按一下後**新增**、 您已成功新增規則，以及列出的其他建議的規則。 不過，您不具有在 NSG 上套用。 若要啟用它，您必須在清單中，選取的規則，按一下**強制執行**（為下一個步驟所述）。

3. 若要套用新的規則，從清單中，選取新的規則，然後按一下**強制執行**。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### 刪除規則 <a name ="delete-rule"> </a>

如有必要，您可以刪除建議的規則。 例如，您可能會決定套用建議的規則可能會封鎖合法的流量。

*若要刪除的自適性網路強化的規則：*

1. 在 **規則**索引標籤上，按一下規則的資料列結尾的三個點 （...） 上，按一下**刪除**。  

    ![堅固功能規則](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

