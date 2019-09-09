---
title: 什麼是 Azure 保留項目？
description: 瞭解 Azure 保留和定價，以節省您的虛擬機器、SQL 資料庫、Azure Cosmos DB 和其他資源成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806354"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

Azure 保留可協助您藉由認可一年或三年的虛擬機器、SQL Database 計算容量、Azure Cosmos DB 輸送量或其他 Azure 資源來節省成本。 認可哥讓您取得所使用資源的折扣。 與隨用隨付價格相比，Reservations 可以大幅降低虛擬機器、SQL 資料庫計算、Azure Cosmos DB 或其他資源的成本，最高可達 72%。 保留會提供計費折扣，且不會影響資源的執行階段狀態。

您可以支付預先或每月的保留期。 預付前和每月保留的總成本是相同的，當您選擇按月支付時，您不需要支付任何額外費用。 每月付款僅適用于 Azure 保留，而不是協力廠商產品。

您可以在 [Azure 入口網站](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)購買保留。

## <a name="why-buy-a-reservation"></a>為何要購買保留？

如果您有長時間執行的虛擬機器、Azure Cosmos DB 或 SQL 資料庫，則購買保留可提供您最符合成本效益的選項。 例如，當您連續執行服務的四個實例時，如果沒有保留，則會以隨用隨付費率計費。 當您為這些資源購買保留時，您會立即取得保留折扣。 那些資源將不再以隨用隨付費率計費。

## <a name="charges-covered-by-reservation"></a>保留所涵蓋的費用

服務方案：

- **保留的虛擬機器實例**-保留僅涵蓋虛擬機器計算成本。 它不會涵蓋其他軟體、網路或儲存體費用。
- **Azure Cosmos DB 保留容量**-保留會涵蓋為您的資源布建的輸送量。 它未涵蓋儲存體和網路費用。
- **SQL Database 保留 vCore** -保留中只會包含計算成本。 授權為分開計費。
- **SQL 資料倉儲**-保留會涵蓋 cDWU 的使用方式。 它不會涵蓋與 SQL 資料倉儲使用量相關聯的儲存體或網路費用。
- **App Service 戳記費用**-保留區涵蓋戳記使用方式。 它不會套用至背景工作，因此與戳記相關聯的任何其他資源也會分開計費。

針對 Windows 虛擬機器和 SQL 資料庫，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。

## <a name="whos-eligible-to-purchase-a-reservation"></a>誰有資格購買保留？

若要購買方案，您必須擁有企業中的訂用帳戶擁有者角色（MS-AZR-0017P-Ms-azr-0017p 或 MS-MS-AZR-0017P-Ms-azr-0148p）或隨用隨付訂用帳戶（MS-MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）。 雲端解決方案提供者可以使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 來購買 Azure 保留。

Enterprise 合約（EA）客戶可以停用 EA 入口網站中的 [**新增保留實例**] 選項，以限制對 EA 管理員的購買。 EA 系統管理員必須是至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 當企業想要讓集中的小組購買不同成本中心的保留時，此選項非常有用。 購買之後，集中式小組可以將成本中心擁有者新增至保留區。 然後，擁有者可以將保留範圍限定在其訂用帳戶。 中央小組不需要擁有購買保留的訂用帳戶擁有者存取權。

保留折扣僅適用于與透過企業、雲端解決方案提供者（CSP）購買的訂用帳戶相關聯的資源，以及隨用隨付費率的個別方案。

## <a name="scope-reservations"></a>範圍保留

您可以將保留範圍限定于訂用帳戶或資源群組。 設定保留區的範圍會選取保留節約的適用位置。 當您將保留範圍限定于資源群組時，保留折扣僅適用于資源群組，而不會套用到整個訂用帳戶。

### <a name="reservation-scoping-options"></a>保留範圍選項

使用資源群組範圍時，您有三個選項可界定保留範圍，視您的需求而定：

- **單一資源群組範圍**—只會將保留折扣套用至所選資源群組中的相符資源。
- **單一訂**用帳戶範圍-將保留折扣套用至所選訂用帳戶中的相符資源。
- **共用範圍**-將保留折扣套用至帳單內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是由帳戶管理員所建立的所有合格訂用帳戶。

在您的使用量上套用保留折扣時，Azure 會依照下列連續處理保留：

1. 範圍設定為資源群組的保留
2. 單一範圍保留
3. 共用範圍保留

單一資源群組可以根據您的保留範圍，從多個保留區取得保留折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>將保留範圍限定于資源群組

當您購買保留時，您可以將保留範圍限定在資源群組中，或在購買後設定範圍。 您必須是訂用帳戶擁有者，才能將保留範圍限定于資源群組。

若要設定範圍，請移至 Azure 入口網站中的 [[購買保留](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)] 頁面。 選取您想要購買的保留類型。 在 [**選取您要購買的產品**選擇] 表單上，將 [範圍] 值變更為 [單一資源群組]。 然後，選取資源群組。

![顯示 VM 保留購買選擇的範例](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

系統會顯示虛擬機器保留中資源群組的購買建議。 建議是藉由分析過去30天的使用量來計算。 如果使用保留實例執行資源的成本比使用隨用隨付費率的資源成本更低，則會進行購買建議。 如需保留購買建議的詳細資訊，請參閱[根據使用模式取得保留實例購買建議](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)。

購買保留之後，您就可以隨時更新範圍。 若要這麼做，請移至保留區 **，按一下 [** 設定]，然後 rescope 保留。 Rescoping 保留不是商業交易。 您的保留期限不會變更。 如需有關更新範圍的詳細資訊，請參閱在[購買保留之後更新範圍](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

![顯示保留範圍變更的範例](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>監視和優化保留使用方式

您可以透過多種方式來監視保留使用量，方法是 Azure 入口網站、透過 Api，或透過使用資料。 若要查看您有權存取的所有保留專案，請移至 Azure 入口網站中的 [**保留**]。 [保留] 方格會顯示保留的最後記錄使用率百分比。 按一下 [保留]，以查看保留的長期使用方式。

如果您是 enterprise 合約的客戶，您也可以使用[api](billing-reservation-apis.md#see-reservation-usage)和[使用量資料](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)來取得保留使用量。

如果您注意到資源群組範圍的保留使用量很低，則可以將保留範圍更新為單一訂用帳戶，或在帳單內容中共用。 您也可以分割保留區，並將產生的保留專案套用至不同的資源群組。

### <a name="other-considerations"></a>其他考量

如果您的資源群組中沒有相符的資源，保留的使用量將會過低。 保留不會自動套用至使用率低的不同資源群組或訂用帳戶。

如果您將資源群組從一個訂用帳戶移到另一個，則保留範圍不會自動更新。 如果您刪除資源群組，則範圍不會更新。 您將必須[rescope 保留](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。 否則，保留專案將會使用量過低。

## <a name="discounted-subscription-and-offer-types"></a>折扣的訂用帳戶和供應專案類型

保留折扣適用於下列合格訂用帳戶和供應項目類型。

- Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148 P)
- 以隨用隨付費率計費的個別方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)
- CSP 訂用帳戶

在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="how-is-a-reservation-billed"></a>保留的計費方式為何？

保留會計費至與訂用帳戶繫結的付款方式。 如果您有 Enterprise 訂用帳戶，保留成本是從您的預付餘額扣除。 當您的承諾用量餘額未涵蓋保留的成本時，就會向您收取超額部分的費用。 如果您有來自個別方案的訂用帳戶與隨用隨付費率，您的帳戶所擁有的信用卡會立即收取預付費用。 每月付款會出現在您的發票上，而您的信用卡會按月計費。 當您按發票計費時，您會看到下一張發票的費用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣適用于符合您購買保留時所選屬性的資源使用量。 屬性包含符合的 Vm、SQL 資料庫、Azure Cosmos DB 或其他資源執行所在的範圍。 例如，如果您想要在美國西部區域的四個標準 D2 虛擬機器保留折扣，請選取 Vm 執行所在的訂用帳戶。

保留折扣是「*使用或遺失-it*」。 如果您沒有任何小時的相符資源，則會遺失該小時的保留數量。 您無法繼續執行未使用的保留時數。

當您關閉資源時，保留折扣會自動套用至指定範圍中的另一個相符資源。 如果在指定的範圍內找不到相符的資源，則保留的時數會*遺失*。

例如，您稍後可能會建立資源，並具有使用量過低的對應保留。 保留折扣會自動套用至新的相符資源。

如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。 您可以在購買保留之後變更範圍。 如需詳細資訊，請參閱[管理 Azure 保留](billing-manage-reserved-vm-instance.md)。

保留折扣僅適用于與企業、CSP 或使用隨用隨付費率之訂用帳戶相關聯的資源。 在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="when-the-reservation-term-expires"></a>當保留期限到期時

在保留期限結束時，計費折扣會到期。 虛擬機器、SQL database、Azure Cosmos DB 或其他資源會以隨用隨付價格計費。 Azure 保留不會自動續約。 若要繼續獲得計費折扣，您必須針對符合資格的服務和軟體購買新的保留。

## <a name="discount-applies-to-different-sizes"></a>折扣適用于不同大小

當您購買保留時，折扣可適用於屬性在相同大小群組內的其他執行個體。 這項功能稱為實例大小彈性。 折扣涵蓋範圍的彈性取決於您購買保留時所挑選的保留類型和屬性。

服務方案：

- 保留的 VM 執行個體：當您購買保留區並選取 [**針對實例大小彈性進行優化**] 時，折扣涵蓋範圍取決於您選取的 VM 大小。 此保留可適用於相同大小系列群組中的虛擬機器 (VM) 大小。 如需詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SQL Database 保留容量：折扣涵蓋範圍取決於您挑選的效能層級。 如需詳細資訊，請參閱[了解如何套用 Azure 保留折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 保留容量：折扣涵蓋範圍取決於所佈建的輸送量。 如需詳細資訊，請參閱[了解如何套用 Azure Cosmos DB 保留折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="reservation-notifications"></a>保留通知

根據您支付 Azure 訂用帳戶的方式而定，我們會以電子郵件將保留通知寄給組織中的下列使用者。 系統會針對各種事件傳送通知，包括：

- 購買
- 即將到期的保留期限
- 到期日
- 更新
- 取消
- 範圍變更

針對具有 EA 訂用帳戶的客戶：
- 購買通知會傳送給買方和 EA 通知連絡人。
- 其他保留週期通知只會傳送到 EA 通知連絡人。
- 使用 RBAC （IAM）許可權新增至保留的使用者，不會收到任何電子郵件通知。

針對具有個別訂閱的客戶：
- 購買者會收到購買通知。
- 在購買時，訂閱帳單帳戶擁有者會收到購買通知。
- 帳戶擁有者會接收所有其他通知。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 使用下列文章來深入了解 Azure 保留項目：
    - [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
    - [使用隨用隨付費率來瞭解訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

- 深入瞭解服務方案的保留：
    - [使用 Azure 保留的 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [具有 Azure Cosmos DB 保留容量的 Azure Cosmos DB 資源](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database 計算具有 Azure SQL Database 保留容量的資源](../sql-database/sql-database-reserved-capacity.md)深入瞭解軟體方案的保留：
    - [來自 Azure 保留的 Red Hat 軟體方案](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [來自 Azure 保留的 SUSE 軟體方案](../virtual-machines/linux/prepay-suse-software-charges.md)
