---
title: 什麼是 Azure 保留項目？ | Microsoft Docs
description: 了解 Azure Reservations 和定價，以節省虛擬機器、SQL 資料庫、Azure Cosmos DB 及其他資源的成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149192"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

Azure Reservations 可協助您透過預先支付一年或三年的虛擬機器、SQL 資料庫計算容量、Azure Cosmos DB 輸送量或其他 Azure 資源的費用來節省成本。 預先支付費用可讓您在所使用的資源上取得折扣。 與隨用隨付價格相比，Reservations 可以大幅降低虛擬機器、SQL 資料庫計算、Azure Cosmos DB 或其他資源的成本，最高可達 72%。 保留會提供計費折扣，且不會影響資源的執行階段狀態。

您可以在 [Azure 入口網站](https://aka.ms/reservations)購買保留。

## <a name="why-buy-a-reservation"></a>為什麼要購買的保留項目嗎？

如果您有虛擬機器、 Azure Cosmos DB 或針對長時間執行的 SQL database，購買保留項目可讓您最具成本效益的選項。 比方說，當您持續執行四個未保留的服務執行個體，您是在隨用隨付費率計費。 若您購買這些資源的保留項目時，您會立即取得保留折扣。 那些資源將不再以隨用隨付方案費率計費。

## <a name="charges-covered-by-reservation"></a>保留項目所涵蓋的費用

服務方案：

- 保留的虛擬機器執行個體：保留僅涵蓋虛擬機器計算成本。 它不會涵蓋其他軟體、網路或儲存體費用。
- Azure Cosmos DB 保留容量：保留可涵蓋為您的資源佈建的輸送量。 它未涵蓋儲存體和網路費用。
- SQL Database 保留 vCore：保留僅包含計算成本。 授權為分開計費。

針對 Windows 虛擬機器和 SQL 資料庫，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。

## <a name="whos-eligible-to-purchase-a-reservation"></a>誰有資格購買保留？

購買方案，您必須在 （MS-AZR-0017 P 或 MS-AZR-0148 P） 的企業或隨用隨付訂用帳戶 （MS-AZR-003 P 或 MS-AZR-0023 P） 的訂用帳戶擁有者角色。 雲端解決方案提供者可以使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 購買 Azure 保留項目。

EA 客戶可以藉由停用限制給 EA 系統管理員購買**新增保留的執行個體**在 EA 入口網站中的選項。 EA 系統管理員必須至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 此選項為適用於想要購買保留項目不同的成本中心的集中式的小組的企業。 購買後，集中式管理的小組可以新增保留項目成本中心擁有者。 擁有者可以接著將範圍將保留對應至其訂用帳戶。 這個核心小組不需要有訂用帳戶擁有者存取權，購買保留項目。

保留折扣僅適用於與 Enterprise、隨用隨付方案，或 CSP 訂用帳戶類型相關聯的資源。

## <a name="reservation-scope"></a>保留範圍

保留範圍會決定要套用保留折扣的資源。 保留範圍可以包含下列值：

**共用範圍**-保留折扣套用至計費內容中的合格訂用帳戶中相符的資源。

- Enterprise 合約客戶，計費內容會是註冊。
 針對隨用隨付客戶計費的範圍會是所有合格的訂閱帳戶系統管理員所建立。

**單一訂用帳戶**-保留折扣套用至選取的訂用帳戶中相符的資源。

您可以[後，您購買保留，請更新範圍](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

## <a name="discounted-subscription-and-offer-types"></a>實付款項折扣的訂用帳戶和供應項目類型

保留折扣會套用到下列符合資格的訂用帳戶，並提供類型。

- Enterprise 合約 (提供數字：MS-AZR-0017P 或 MS-AZR-0148 P)
- 預付型方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023 P)
- CSP 訂用帳戶

在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="how-is-a-reservation-billed"></a>保留的計費方式為何？

保留會計費至與訂用帳戶繫結的付款方式。 如果您有 Enterprise 訂用帳戶，保留成本是從您的預付餘額扣除。 如果您的預付餘額未涵蓋保留的成本，您會支付超額部分。 如果您有隨用隨付訂用帳戶，會對您的帳戶所登記的信用卡立即計費。 如果是透過發票計費，您會在下一張發票上看到費用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣如何套用

保留折扣適用於符合當您購買保留時選取之屬性的資源使用量。 屬性包含相符的 VM、SQL 資料庫、Azure Cosmos DB 或其他資源的執行範圍。 例如，如果您想要保留折扣在美國西部區域中的四個標準 D2 虛擬機器，然後選取 Vm 執行所在的訂用帳戶。

保留折扣會 「*使用-it-或-遺失-it*"。 如果您沒有相符的任何小時的資源，然後您會遺失保留數量的那一小時。 您無法執行轉送未使用保留的時數。

當您關閉資源時，保留折扣會自動套用至指定之範圍中的另一個相符的資源。 如果沒有相符的資源位於指定的範圍，則保留的時間為週間*遺失*。

例如，您可能會稍後建立的資源，並有相符的保留項目，指出使用量過低。 在此範例中，保留折扣會自動套用到新的比對資源。

如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。 您可以在購買保留之後變更範圍。 如需詳細資訊，請參閱[管理 Azure 保留](billing-manage-reserved-vm-instance.md)。

保留折扣僅適用於與 Enterprise、隨用隨付方案，或 CSP 訂用帳戶類型相關聯的資源。 在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="when-the-reservation-term-expires"></a>當保留期限到期時

在保留期限結束時，計費折扣會到期，而虛擬機器、SQL 資料庫、 Azure Cosmos DB 或其他資源就會改以隨用隨付價格計費。 Azure 保留並不會自動更新。 若要繼續獲得計費折扣，您必須針對符合資格的服務和軟體購買新的保留。

## <a name="discount-applies-to-different-sizes"></a>折扣適用於不同的大小

當您購買保留時，折扣可適用於屬性在相同大小群組內的其他執行個體。 這項功能稱為執行個體大小的彈性。 折扣涵蓋範圍的彈性取決於您購買保留時所挑選的保留類型和屬性。

服務方案：

- 保留的 VM 執行個體：當您購買保留，並選取**適合**:**執行個體大小的彈性**，折扣涵蓋範圍取決於您選取的 VM 大小。 此保留可適用於相同大小系列群組中的虛擬機器 (VM) 大小。 如需詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SQL Database 保留容量：折扣涵蓋範圍取決於您挑選的效能層級。 如需詳細資訊，請參閱[了解如何套用 Azure 保留折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 保留容量：折扣涵蓋範圍取決於所佈建的輸送量。 如需詳細資訊，請參閱[了解如何套用 Azure Cosmos DB 保留折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 使用下列文章來深入了解 Azure 保留項目：
    - [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
    - [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

- 深入了解服務方案的保留項目：
    - [使用 Azure 保留的 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [使用 Azure Cosmos DB 的 azure Cosmos DB 資源保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [使用 Azure SQL Database 的 SQL Database 計算資源保留容量](../sql-database/sql-database-reserved-capacity.md)深入了解軟體方案的保留項目：
    - [從 Azure 保留的 Red Hat 軟體方案](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [從 Azure 保留的 SUSE 軟體方案](../virtual-machines/linux/prepay-suse-software-charges.md)
