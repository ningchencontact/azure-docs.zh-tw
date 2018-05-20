---
ms.topic: include
ms.openlocfilehash: 31b0d0018129ee65bb124c8008759cc6c7c8510e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>預付具有保留的 VM 執行個體的虛擬機器

預付虛擬機器，並使用保留的虛擬機器執行個體節省成本。 如需詳細資訊，請參閱[保留的虛擬機器執行個體供應項目](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

您可以在 [Azure 入口網站](https://portal.azure.com)中購買保留的虛擬機器執行個體。 購買保留的虛擬機器執行個體：
-   您必須至少為一個企業或預付型方案訂用帳戶的「擁有者」角色。
-   針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用保留項目購買。
-   針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

## <a name="buy-a-reserved-virtual-machine-instance"></a>購買保留的虛擬機器執行個體
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務] > [保留]。
3. 選取 [新增] 以購買新的保留項目。
4. 填寫必要欄位。 符合您選取之屬性的執行中 VM 執行個體符合取得保留項目折扣的資格。 取得折扣的 VM 執行個體實際數目取決於選取的範圍和數量。

    | 欄位      | 說明|
    |:------------|:--------------|
    |Name        |此保留項目的名稱。| 
    |訂用帳戶|用來支付保留項目的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P) 或預付型方案 (供應項目號碼：MS-AZR-0003P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對預付型方案訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。|    
    |Scope       |保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <ul><li>單一訂用帳戶 - 保留項目折扣會套用至此訂用帳戶中的 VM。 </li><li>共用 - 保留項目折扣會套用至計費內容內任何訂用帳戶中執行的 VM。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶 (開發/測試訂用帳戶除外)。 針對預付型方案客戶，共用範圍是帳戶系統管理員所建立的所有預付型方案訂用帳戶。</li></ul>|
    |位置    |保留項目所涵蓋的 Azure 區域。|    
    |VM 大小     |VM 執行個體的大小。|
    |詞彙        |一年或三年。|
    |數量    |保留項目內所購買的執行個體數目。 數量是可以取得帳單折扣之執行中 VM 執行個體的數目。 例如，如果您在美國東部執行 10 個 Standard_D2 VM，那麼您會指定數量為 10，以最大化所有執行中機器的效益。 |
5. 當您選取 [計算成本] 時，可以檢視保留項目的成本。

    ![提交保留項目購買之前的螢幕擷取畫面](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 選取 [購買]。
7. 選取 [檢視此保留] 以查看您的購買狀態。

    ![提交保留項目購買之前的螢幕擷取畫面](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>後續步驟 
保留項目折扣會自動套用到符合保留項目範圍和屬性之執行中虛擬機器的數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、CLI 或 API 來更新保留項目的範圍。 

若要了解如何管理保留項目，請參閱[管理 Azure 保留的虛擬機器執行個體](../articles/billing/billing-manage-reserved-vm-instance.md)。

若要深入了解保留的虛擬機器執行個體，請參閱下列文章。

- [使用 Azure 保留的虛擬機器執行個體以節省虛擬機器的成本](../articles/billing/billing-save-compute-costs-reservations.md)
- [了解保留執行個體折扣如何套用](../articles/billing/billing-understand-vm-reservation-charges.md)
- [了解預付型方案的保留執行個體使用量](../articles/billing/billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊之保留執行個體的使用方式](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留的執行個體內](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 程式中的保留執行個體](https://docs.microsoft.com/partner-center/azure-reservations)