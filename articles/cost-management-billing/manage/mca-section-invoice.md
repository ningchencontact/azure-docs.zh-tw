---
title: 根據您的需求來組織發票-Azure
description: 瞭解如何在您的發票上組織成本。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991057"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>藉由自訂您的帳單帳戶來組織成本

您的 Microsoft 客戶合約帳單帳戶可讓您彈性地根據自己的需求來組織成本，無論是由部門、專案或開發環境。 

本文說明如何使用 Azure 入口網站來組織您的成本。 適用于 Microsoft 客戶合約的帳單帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>使用帳單設定檔和發票區段來為您的帳戶編制結構

在 Microsoft 客戶合約的帳單帳戶中，您可以使用帳單設定檔和發票區段來組織您的成本。

![顯示 Microsoft 客戶合約計費階層的螢幕擷取畫面](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>帳單設定檔

帳單設定檔代表發票和相關的帳單資訊，例如付款方法和帳單位址。 您帳戶中的每個帳單設定檔都會在月初產生每月發票。 發票包含上個月的 Azure 使用量和其他購買費用。

當您註冊 Azure 時，系統會自動建立帳單設定檔和您的帳單帳戶。 您可以建立額外的帳單設定檔，以多個月的發票來組織成本。 

> [!IMPORTANT]
>
> 建立額外的帳單設定檔可能會影響您的整體成本。 如需詳細資訊，請參閱[新增帳單設定檔時要考慮的事項](#things-to-consider-when-adding-new-billing-profiles)。

### <a name="invoice-section"></a>發票區段

發票區段代表發票中的成本群組。 系統會自動為您帳戶中的每個帳單設定檔建立發票區段。 您可以根據您的需求，建立其他區段來組織成本。 每個發票區段都會顯示在發票上，並附上該月份產生的費用。 

下圖顯示包含兩個發票區段的發票-工程和行銷。 每個區段的摘要和詳細費用都會顯示在發票中。 映射中顯示的價格僅供範例之用，不代表 Azure 服務的實際價格。 

![顯示含有區段之發票的影像](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>常見案例的帳單帳戶結構

本節說明組織成本和對應帳單帳戶結構的常見案例：

|案例  |結構  |
|---------|---------|
|適用于 Azure 的插頭註冊，需要一個每月發票。 | 帳單設定檔和發票區段。 當使用者註冊 Azure 時，會自動設定此結構，而且不需要任何額外的步驟。 |

![簡單計費案例的資訊圖形](./media/mca-section-invoice/organize-billing-scenario1.png)

|案例  |結構  |
|---------|---------|
|Contoso 是一個需要每月發票的小型組織，但依據其部門來分組成本-行銷和財務。  | Contoso 的帳單設定檔以及每個行銷和財務部門的發票區段。 |

![簡單計費案例的資訊圖形](./media/mca-section-invoice/organize-billing-scenario2.png)

|案例  |結構  |
|---------|---------|
|Fabrikam 是中等大小的組織，其工程和行銷部門需要個別的發票。 針對工程部門，他們想要依環境來分組成本-生產和開發。  | 適用于行銷和財務部門的帳單設定檔。 針對行銷部門，每個發票區段適用于生產和開發環境。 |

![簡單計費案例的資訊圖形](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>建立新的發票區段

若要建立發票區段，您必須是**帳單設定檔擁有者**或**帳單設定檔參與者**。 如需詳細資訊，請參閱[管理帳單設定檔的發票區段](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 從左側窗格中選取 [**帳單設定檔**]。 從清單中，選取帳單設定檔。 新的區段會顯示在所選帳單設定檔的發票上。 

   [顯示帳單配置檔案清單的 ![螢幕擷取畫面](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 從左側窗格中選取 [**發票區段**]，然後從頁面頂端選取 [**新增**]。

   [![顯示新增發票區段的螢幕擷取畫面](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 輸入發票區段的名稱。 

   [顯示發票區段建立頁面的 ![螢幕擷取畫面](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. 選取 [建立]。

## <a name="create-a-new-billing-profile"></a>建立新的帳單設定檔

若要建立帳單設定檔，您必須是**帳單帳戶擁有**者或**帳單帳戶參與者**。 如需詳細資訊，請參閱[管理帳單帳戶的帳單設定檔](understand-mca-roles.md#manage-billing-profiles-for-billing-account)。

> [!IMPORTANT]
>
> 建立額外的帳單設定檔可能會影響您的整體成本。 如需詳細資訊，請參閱[新增帳單設定檔時要考慮的事項](#things-to-consider-when-adding-new-billing-profiles)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 從左側窗格中選取 [**帳單設定檔**]，然後從頁面頂端選取 [**新增**]。

   [顯示帳單配置檔案清單的 ![螢幕擷取畫面](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > 如果您在 [帳單設定檔] 頁面中看不到 [新增] 按鈕，表示您的帳戶無法使用此功能。 目前僅適用于使用 Microsoft 代表時已設定的帳戶。

4. 填妥表單，然後按一下 [建立]。

   [顯示帳單設定檔建立頁面的 ![螢幕擷取畫面](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |欄位  |定義  |
    |---------|---------|
    |名稱     | 此顯示名稱可協助您輕鬆地識別 Azure 入口網站中的帳單設定檔。  |
    |PO 編號    | 選擇性的訂購單號碼。 PO 號碼會顯示在為帳單設定檔產生的發票上。 |
    |帳單地址   | 計費位址會顯示在為帳單設定檔產生的發票上。 |
    |電子郵件發票   | 勾選 [電子郵件發票] 方塊，以透過電子郵件接收此帳單設定檔的發票。 如果您未加入宣告，則可以在 Azure 入口網站中查看並下載發票。|

5. 選取 [建立]。

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>連結發票區段和帳單設定檔的費用

根據您的需求自訂帳單帳戶之後，您可以將訂用帳戶和其他產品連結到您想要的發票區段和帳單設定檔。

### <a name="link-a-new-subscription"></a>連結新的訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 尋找**訂用帳戶**。

   [在入口網站中顯示訂用帳戶搜尋的 ![螢幕擷取畫面](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. 從頁面頂端選取 [新增]。

   ![在 [訂用帳戶] 檢視中顯示 [新增] 按鈕的螢幕擷取畫面](./media/mca-section-invoice/subscription-add.png)

4. 如果您有多個帳單帳戶的存取權，請選取您的 Microsoft 客戶合約帳單帳戶。

   ![在 [訂用帳戶] 檢視中顯示 [新增] 按鈕的螢幕擷取畫面](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. 選取將依據訂用帳戶使用量計費的帳單設定檔。 此訂用帳戶的 Azure 使用量和其他購買費用將以所選帳單設定檔的發票計費。

6. 選取 [發票] 區段以連結訂用帳戶的費用。 費用將會顯示在帳單設定檔發票的此區段下。

7. 選取 Azure 方案，並為您的訂用帳戶輸入易記名稱。 

9. 按一下頁面底部的 [新增]。  

### <a name="link-existing-subscriptions-and-products"></a>連結現有的訂閱和產品

如果您有現有的 Azure 訂用帳戶或其他產品（例如 Azure Marketplace 和應用程式來源資源），您可以將它們從現有的發票區段移至另一個發票區段，以重新組織您的成本。 

> [!IMPORTANT]
>
> 訂用帳戶和其他產品只能在屬於相同帳單設定檔的發票區段之間移動。 不支援在不同的帳單設定檔中，跨發票區段移動訂用帳戶和產品。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]。

   ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/mca-section-invoice/search-cmb.png)

3. 若要將訂用帳戶連結至新的發票區段，請從畫面左側選取 [ **Azure 訂閱**]。 如需其他產品（例如 Azure Marketplace 和應用程式來源資源），請選取 [**週期性費用**]。

   [![螢幕擷取畫面，其中顯示變更發票區段的選項](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. 在頁面中，針對您想要連結到新發票區段的訂用帳戶或產品，按一下省略號（三個點）。 選取 [**變更發票] 區段**。

5. 從下拉式清單中選取 [新增發票] 區段。 下拉式清單只會顯示與現有發票區段相同的帳單設定檔相關聯的發票區段。

    [![螢幕擷取畫面，其中顯示選取新的發票區段](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. 選取 [儲存]。

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>加入新的帳單設定檔時所要考慮的事項

### <a name="azure-usage-charges-may-be-impacted"></a>Azure 使用量費用可能會受到影響

在您的 Microsoft 客戶合約帳單帳戶中，每個帳單設定檔的 Azure 使用量會每月匯總一次。 具有分層定價的 Azure 資源價格是根據每個帳單設定檔的使用方式來決定。 計算價格時，不會在帳單設定檔之間匯總使用量。 這可能會影響具有多個帳單設定檔之帳戶的 Azure 使用量整體成本。

讓我們來看一個範例，瞭解兩個案例的成本有何差異。 案例中使用的價格僅供範例之用，不代表 Azure 服務的實際價格。

#### <a name="you-only-have-one-billing-profile"></a>您只有一個帳單設定檔。

假設您使用的是 Azure 區塊 blob 儲存體，其費用為每 GB 00184，適用于前 50 tb （TB），然後00177每 GB 為下一個 450 tb。 您在帳單設定檔計費的訂用帳戶中使用了 100 TB，以下是您要支付多少費用。

|  層定價（美元） |數量 | 金額（美元）|
|---------|---------|---------|
|第一個 50 TB/月每 TB 1.84    | 50 TB        | 92.0   |
|每 TB 1.77，適用于下一個 450 TB/月    |  50 TB         | 88.5   |
|總計     |     100 TB  | 180.5

在此案例中，使用 100 TB 資料的總費用為**180.5**

#### <a name="you-have-multiple-billing-profiles"></a>您有多個帳單設定檔。

現在，假設您已建立另一個帳單設定檔，並透過訂用帳戶計費至第一個帳單設定檔的訂用帳戶使用 50 GB，而透過訂用帳戶向第二個帳單設定檔收取 50 GB 的訂用帳戶，以下是您要支付多少費用。

`Charges for the first billing profile`

|  層定價（美元） |數量 | 金額（美元）|
|---------|---------|---------|
|第一個 50 TB/月每 TB 1.84    | 50 TB        | 92.0  |
|每 TB 1.77，適用于下一個 450 TB/月    |  0 TB         | 0.0  |
|總計     |     50 TB  | 92.0 

`Charges for the second billing profile`

|  層定價（美元） |數量 | 金額（美元）|
|---------|---------|---------|
|第一個 50 TB/月每 TB 1.84    | 50 TB        | 92.0  |
|每 TB 1.77，適用于下一個 450 TB/月    |  0 TB         | 0.0  |
|總計     |     50 TB  | 92.0 

在此案例中，使用 100 TB 資料的總費用為**184.0** （92.0 * 2）。

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 保留權益可能不適用於所有訂用帳戶

具有共用範圍的 Azure 保留會套用至單一帳單設定檔中的訂用帳戶，而且不會在帳單設定檔之間共用。 

![不同帳單帳戶結構的保留應用程式資訊圖形](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

在上圖中，Contoso 有兩個訂用帳戶。 根據計費帳戶的結構方式，Azure 保留權益的套用方式會有所不同。 在左側案例中，保留權益會套用至兩個訂用帳戶，並向工程帳單設定檔收費。 在右側案例中，保留權益只會套用至訂用帳戶1，因為它是唯一向工程帳單設定檔收費的訂用帳戶。 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 連絡客戶支援

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶](create-subscription.md)
- [在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [向其他計費帳戶中的使用者接管 Azure 訂用帳戶的帳單擁有權](mca-request-billing-ownership.md)
