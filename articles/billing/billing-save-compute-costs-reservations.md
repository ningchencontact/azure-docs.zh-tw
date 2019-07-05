---
title: 什麼是 Azure 保留項目？
description: 了解 Azure Reservations 和定價，以節省虛擬機器、SQL 資料庫、Azure Cosmos DB 及其他資源的成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cd0a70aa0fb5096c5b0157ae078c961da03109bc
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565336"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

Azure Reservations 可協助您透過預先支付一年或三年的虛擬機器、SQL 資料庫計算容量、Azure Cosmos DB 輸送量或其他 Azure 資源的費用來節省成本。 預先支付費用可讓您在所使用的資源上取得折扣。 與隨用隨付價格相比，Reservations 可以大幅降低虛擬機器、SQL 資料庫計算、Azure Cosmos DB 或其他資源的成本，最高可達 72%。 保留會提供計費折扣，且不會影響資源的執行階段狀態。

您可以在 [Azure 入口網站](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)購買保留。

## <a name="why-buy-a-reservation"></a>為什麼要購買的保留項目嗎？

如果您有虛擬機器、 Azure Cosmos DB 或針對長時間執行的 SQL database，購買保留項目可讓您最具成本效益的選項。 比方說，當您持續執行四個未保留的服務執行個體，您是在隨用隨付費率計費。 若您購買這些資源的保留項目時，您會立即取得保留折扣。 那些資源將不再以隨用隨付費率計費。

## <a name="charges-covered-by-reservation"></a>保留項目所涵蓋的費用

服務方案：

- **保留的虛擬機器執行個體**-保留僅涵蓋虛擬機器計算成本。 它不會涵蓋其他軟體、網路或儲存體費用。
- **Azure Cosmos DB 的保留容量**-保留可涵蓋為您的資源佈建的輸送量。 它未涵蓋儲存體和網路費用。
- **SQL Database 會保留 vCore** -僅計算成本會隨附於保留項目。 授權為分開計費。

針對 Windows 虛擬機器和 SQL 資料庫，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。

## <a name="whos-eligible-to-purchase-a-reservation"></a>誰有資格購買保留？

購買方案，您必須在 （MS-AZR-0017 P 或 MS-AZR-0148 P） 的企業或隨用隨付訂用帳戶 （MS-AZR-003 P 或 MS-AZR-0023 P） 的訂用帳戶擁有者角色。 雲端解決方案提供者可以使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 購買 Azure 保留項目。

EA 客戶可以藉由停用限制給 EA 系統管理員購買**新增保留的執行個體**EA 入口網站中的選項。 EA 系統管理員必須至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 此選項為適用於想要購買保留項目不同的成本中心的集中式的小組的企業。 購買後，集中式管理的小組可以新增保留項目成本中心擁有者。 擁有者可以接著將範圍將保留對應至其訂用帳戶。 這個核心小組不需要有訂用帳戶擁有者存取權，購買保留項目。

保留折扣僅適用於透過 Enterprise、 CSP 和個別的計劃，以隨用隨付的費率購買的訂用帳戶相關聯的資源。

## <a name="scope-reservations"></a>領域保留區

您可以將訂用帳戶或資源群組的保留範圍。 設定保留範圍選取適用省下的保留項目。 當您設定範圍的資源群組的保留時，請保留折扣僅適用於資源群組，不完整的訂用帳戶。

### <a name="reservation-scoping-options"></a>保留範圍選項

與資源群組範圍，您會有三個選項，來限定範圍的保留項目，根據您的需求：

- **單一資源群組範圍**— 在選取的資源群組中的比對資源套用保留折扣。
- **單一訂用帳戶範圍**— 保留折扣會套用至選取的訂用帳戶中比對資源。
- **共用範圍**— 會保留折扣套用至對應的計費內容中的合格訂用帳戶中的資源。 Enterprise 合約客戶，計費內容會是註冊。 以隨用隨付費率的個別訂用帳戶，計費的範圍是由帳戶系統管理員建立的所有合格訂閱。

同時在您的使用量套用保留折扣，Azure 會處理以下列順序保留項目：

1. 保留範圍設定為資源群組
2. 單一的領域保留區
3. 共用的領域保留區

單一資源群組可以從多個保留項目，根據您設定您的保留的範圍取得保留折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>範圍保留給資源群組

您可以在購買保留項目，或您在購買後設定範圍時，範圍至資源群組的保留項目。 您必須是訂用帳戶擁有者來限定範圍至資源群組的保留項目。

若要設定範圍，請前往[購買保留](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)在 Azure 入口網站中的頁面。 然後，選取您想要購買的保留項目類型。 在上**選取您想要購買的產品**選取項目表單中，變更**範圍**值**單一資源群組**，然後選取 資源群組。

![範例顯示 VM 保留購買選取項目](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

會顯示保留的虛擬機器中的資源群組的購買建議。 建議的計算方式是您的使用量分析過去 30 天。 如果使用保留的執行個體中執行資源的成本是以隨用隨付費率執行資源的成本便宜，是由所購買建議。 如需有關保留購買建議的詳細資訊，請參閱[取得保留的執行個體購買建議根據使用方式模式](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)部落格文章。

您購買保留項目之後，您一律可以更新範圍。 若要這樣做，請移至保留項目，請按一下**組態**並重新指定保留項目。 Rescoping 保留項目不是商業交易。 您的保留期間不會變更。 如需有關如何更新範圍的詳細資訊，請參閱[後，您購買保留，請更新範圍](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

![顯示 保留範圍變更的範例](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>監視和最佳化保留使用情況

您可以監視您的保留使用情況，以多種方式 – 透過 Azure 入口網站、 透過 Api，或使用方式資料。 若要查看所有您具有保留的存取權，請前往**保留**在 Azure 入口網站中。 此保留項目的方格會顯示最後一個記錄的使用量百分比為保留項目。 按一下以查看長期保留使用量的保留項目。

您也可以取得使用的保留區使用率[Api](billing-reservation-apis.md#see-reservation-usage)進出您[使用量資料](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)如果您是 enterprise 合約客戶。

如果您注意到您的資源群組的使用量範圍保留項目偏低，則您可以更新單一訂用帳戶，或將它共用於計費的內容將保留範圍。 您也可以將保留分割，並將產生的保留項目套用至不同的資源群組。

### <a name="other-considerations"></a>其他考量

如果您沒有相符的資源群組中的資源，然後保留項目將會是使用量過低。 此保留項目不會自動套用至不同的資源群組或訂用帳戶沒有低使用率。

如果從一個訂用帳戶移動到另一個資源群組，不會自動更新保留範圍。 您必須重新指定保留項目。 否則，此保留項目將是使用量過低。

## <a name="discounted-subscription-and-offer-types"></a>實付款項折扣的訂用帳戶和供應項目類型

保留折扣會套用到下列符合資格的訂用帳戶，並提供類型。

- Enterprise 合約 (提供數字：MS-AZR-0017P 或 MS-AZR-0148 P)
- 個別的計劃，以隨用隨付的費率 (提供數字：MS-AZR-0003P 或 MS-AZR-0023 P)
- CSP 訂用帳戶

在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="how-is-a-reservation-billed"></a>保留的計費方式為何？

保留會計費至與訂用帳戶繫結的付款方式。 如果您有 Enterprise 訂用帳戶，保留成本是從您的預付餘額扣除。 如果您的預付餘額未涵蓋保留的成本，您會支付超額部分。 如果您有隨用隨付費率的個別方案的訂用帳戶，您對您的帳戶的信用卡支付立即。 如果是透過發票計費，您會在下一張發票上看到費用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣如何套用

保留折扣適用於符合當您購買保留時選取之屬性的資源使用量。 屬性包含相符的 VM、SQL 資料庫、Azure Cosmos DB 或其他資源的執行範圍。 例如，如果您想要保留折扣在美國西部區域中的四個標準 D2 虛擬機器，然後選取 Vm 執行所在的訂用帳戶。

保留折扣會 「*使用-it-或-遺失-it*"。 如果您沒有相符的任何小時的資源，然後您會遺失保留數量的那一小時。 您無法執行轉送未使用保留的時數。

當您關閉資源時，保留折扣會自動套用至指定之範圍中的另一個相符的資源。 如果沒有相符的資源位於指定的範圍，則保留的時間為週間*遺失*。

例如，您可能會稍後建立的資源，並有相符的保留項目，指出使用量過低。 在此範例中，保留折扣會自動套用到新的比對資源。

如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。 您可以在購買保留之後變更範圍。 如需詳細資訊，請參閱[管理 Azure 保留](billing-manage-reserved-vm-instance.md)。

保留折扣僅適用於企業中，CSP，相關聯的資源，或使用付的訂用帳戶移費率。 在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

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
    - [了解您的訂用帳戶，以隨用隨付費率的保留使用情況](billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

- 深入了解服務方案的保留項目：
    - [使用 Azure 保留的 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [使用 Azure Cosmos DB 的 azure Cosmos DB 資源保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [使用 Azure SQL Database 的 SQL Database 計算資源保留容量](../sql-database/sql-database-reserved-capacity.md)深入了解軟體方案的保留項目：
    - [從 Azure 保留的 Red Hat 軟體方案](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [從 Azure 保留的 SUSE 軟體方案](../virtual-machines/linux/prepay-suse-software-charges.md)
