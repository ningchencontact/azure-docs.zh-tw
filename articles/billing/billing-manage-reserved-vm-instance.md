---
title: 管理 Azure 保留專案
description: 瞭解您可以如何管理 Azure 保留專案。
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840015"
---
# <a name="manage-reservations-for-azure-resources"></a>管理 Azure 資源的保留

購買 Azure 保留之後, 您可能需要將保留套用至不同的訂用帳戶、變更可以管理保留的人員或變更保留範圍。 您也可以將保留分割成兩個保留，以將您購買的部分執行個體套用至另一個訂用帳戶。

如果您已購買 Azure 保留的虛擬機器執行個體, 您可以變更保留的 [優化] 設定。 保留折扣可以套用至相同系列的 VM，或是您可以為特定 VM 大小保留資料中心容量。 而且, 您應該嘗試將保留優化, 使其完全使用。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>保留順序和保留

當您購買保留時, 會建立兩個物件:**保留順序**和**保留**。

在購買時, 保留訂單在其底下有一個保留。 [分割]、[合併]、[部分退款] 或 [exchange] 等動作會在**保留順序**之下建立新的保留。

若要查看保留順序, 請移至 [**保留**] > 選取保留區, 然後按一下 [**保留訂單識別碼**]。

![保留訂單詳細資料的範例, 其中顯示保留訂單識別碼 ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

保留會繼承其保留順序的許可權。

## <a name="change-the-reservation-scope"></a>變更保留範圍

 您的保留折扣適用于虛擬機器、SQL 資料庫、Azure Cosmos DB 或符合保留範圍中執行的其他資源。 計費內容取決於購買此保留所用的訂用帳戶。

更新保留範圍：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務] > [保留]。
3. 選取保留。
4. 選取 [設定] > [組態]。
5. 變更範圍。

如果您從 [共用] 變更為 [單一範圍], 則只能選取您是擁有者的訂用帳戶。 只能選取與保留屬於相同計費內容的訂用帳戶。

此範圍僅適用于使用隨用隨付費率的個別訂用帳戶 (提供 MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p)、企業供應專案 MS-MS-AZR-0017P-ms-azr-0017p 或 MS MS-AZR-0017P-Ms-azr-0148p 或 CSP 訂用帳戶類型。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>新增或變更可以管理保留的使用者

您可以透過將人員新增至保留順序或保留的角色, 來委派保留管理。 根據預設, 放置保留順序和帳戶管理員的人員會擁有保留順序和保留的擁有者角色。

您可以從取得保留折扣的訂用帳戶, 獨立管理保留訂單和保留的存取權。 當您授與其他人管理保留順序或保留的許可權時, 不會授與他們管理訂用帳戶的許可權。 同樣地, 如果您授與其他人管理保留範圍中訂用帳戶的許可權, 則不會授與他們管理保留順序或保留的許可權。

若要執行交換或退款, 使用者必須具有保留訂單的存取權。 授與使用者許可權時, 最好將許可權授與保留順序, 而不是保留。


委派保留的存取管理：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]  >  [保留] 來列出您具有存取權的保留。
3. 選取您想要將存取權委派給其他使用者的保留。
4. 選取 [存取控制 (IAM)]。
5. 選取 [新增角色指派] > [角色] > [擁有者]。 或是，如果您想要給予有限的存取權，可選取不同角色。
6. 輸入要新增為擁有者之使用者的電子郵件地址。
7. 選取使用者，然後選取 [儲存]。

## <a name="split-a-single-reservation-into-two-reservations"></a>將單一保留分割成兩個保留

 購買保留內的多個資源執行個體之後，建議您將該保留中的執行個體指派至不同的訂用帳戶。 依預設，所有執行個體都具有一個範圍 - 單一訂用帳戶或共用。 例如, 您購買了10個保留實例, 並將範圍指定為訂用帳戶 A。您現在可能會想要將7個保留的範圍變更為訂用帳戶 A, 並將剩餘的三個設為訂用帳戶 B。分割保留可讓您散發實例, 以進行更細微的範圍管理。 您可以選擇共用範圍來簡化訂用帳戶的配置。 但是，基於成本管理或預算考量，您可以配置一些數量給特定的訂用帳戶。

 您可以透過 PowerShell、CLI 或 API 來將保留分成兩個保留。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 分割保留

1. 執行下列命令以取得保留訂單識別碼：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 取得保留的詳細資料：

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 將保留分割成兩個，並分散執行個體：

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 您可以執行下列命令來更新範圍：

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換或退款保留

您可以取消、交換或退款保留, 但有某些限制。 如需詳細資訊, 請參閱[Azure 保留的自助交換和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>變更保留 VM 執行個體的最佳化設定

 當您購買保留的 VM 執行個體時，您可以選擇執行個體大小的彈性或容量優先順序。 執行個體大小彈性會將保留項目折扣套用至同一個 [VM 大小群組](https://aka.ms/RIVMGroups)中的其他 VM。 容量優先順序會讓資料中心容量供您的部署優先使用。 此選項可讓您更加確信您能夠在需要時啟動 VM 執行個體。

根據預設，當保留範圍為共用時，執行個體大小彈性就會啟用。 資料中心容量不會針對 VM 部署設定優先權。

對於單一範圍的保留，您可以為了容量優先順序 (而不是為了 VM 執行個體大小彈性) 而將保留最佳化。

若要更新保留的最佳化設定：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]  >  [保留]。
3. 選取保留。
4. 選取 [設定] > [組態]。
5. 變更**最佳化設定**。

## <a name="optimize-reservation-use"></a>優化保留使用

只有在持續的資源使用方式下, 才會節省 Azure 保留空間。 當您購買保留時, 您必須支付一或三年期的最高資源使用量 (100%) 的預付成本。 請嘗試最大化您的保留, 以充分運用及省下更多費用。 下列各節說明如何監視保留並優化其使用方式。

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure 入口網站中的視圖保留使用

在 Azure 入口網站中, 有一種方式可查看保留使用量。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [**所有服務** > ] [[**保留**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)], 並記下保留的**使用率 (%)** 。  
  ![顯示保留清單的影像](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. 選取保留區。
4. 檢查一段時間的保留使用趨勢。  
  ![顯示保留使用的影像 ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>使用 API 的查看保留專案

如果您是 Enterprise 合約 (EA) 客戶, 您可以透過程式設計方式來查看組織中的保留如何使用。 您會透過使用量資料取得未使用的保留。 當您查看保留費用時, 請記住, 資料會分成實際成本和分攤成本。 實際成本會提供資料來協調您的每月帳單。 它也具有保留購買成本和保留應用程式詳細資料。 分攤成本與實際成本類似, 不同之處在于保留使用量的有效價格是按比例計算。 未使用的保留時數會顯示在分攤成本資料中。 如需 EA 客戶使用方式資料的詳細資訊, 請參閱[取得 Enterprise 合約保留成本和使用量](billing-understand-reserved-instance-usage-ea.md)。

針對其他訂用帳戶類型, 請使用 API[保留摘要-依保留順序和保留的清單](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

### <a name="optimize-your-reservation"></a>優化您的保留

如果您發現貴組織的保留未充分運用:

- 請確定您的組織所建立的虛擬機器符合保留的 VM 大小。
- 請確認執行個體大小彈性已開啟。 如需詳細資訊，請參閱[管理保留 - 變更保留的 VM 執行個體的最佳化設定](#change-optimize-setting-for-reserved-vm-instances)。
- 將保留範圍變更為 [_共用_], 使其更廣泛地套用。 如需詳細資訊, 請參閱[變更保留的範圍](#change-the-reservation-scope)。
- 請考慮交換未使用的數量。 如需詳細資訊, 請參閱[取消和交換](#cancel-exchange-or-refund-reservations)。


## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有任何疑問或需要協助, 請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留專案？](billing-save-compute-costs-reservations.md)

購買服務方案:
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [以 Azure Cosmos DB 保留容量預先支付 Azure Cosmos DB 資源的費用](../cosmos-db/cosmos-db-reserved-capacity.md)

購買軟體方案:
- [預付 Azure 保留的 Red Hat 軟體方案](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [從 Azure 保留預付 SUSE 軟體方案](../virtual-machines/linux/prepay-suse-software-charges.md)

瞭解折扣和使用方式:
- [了解 VM 保留折扣的套用方式](billing-understand-vm-reservation-charges.md)
- [瞭解如何套用 Red Hat Enterprise Linux 軟體方案折扣](../billing/billing-understand-rhel-reservation-charges.md)
- [了解如何套用 SUSE Linux Enterprise 軟體方案折扣](../billing/billing-understand-suse-reservation-charges.md)
- [了解其他保留折扣的套用方式](billing-understand-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體成本不包含在 Reservations 內](billing-reserved-instance-windows-software-costs.md)
