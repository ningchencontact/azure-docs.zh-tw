---
title: Azure 企業註冊發票
description: 本文將說明如何管理 Azure 企業發票，並採取相關行動。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 2e9611995a233f9890aebb90fc3b8deaeed8efc2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226315"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 企業註冊發票

本文將說明如何管理 Azure 企業發票，並採取相關行動。 您的發票即代表帳單，因此您應該仔細檢查其正確性。 您也應熟悉管理發票時可能需要的其他工作。

## <a name="change-a-po-number-for-an-overage-invoice"></a>變更超額發票的 PO 編號

如果 EA 系統管理員未在發票日期之前設定編號，則 Azure EA 入口網站會自動產生預設的採購單 (PO) 號碼。 EA 管理員可以在收到自動發票通知電子郵件後的七天內更新 PO 號碼。

此外也可以輸入特定帳戶或訂用帳戶的採購單號碼。 報表會預設為在階層中設定的最低層級採購單號碼，這表示，若未輸入訂用帳戶採購單號碼，則會使用帳戶採購單號碼，且若未輸入帳戶採購單，則會使用部門採購單。

### <a name="to-update-the-azure-services-purchase-order-number"></a>若要更新 Azure 服務採購單號碼：

1. 在 Azure EA 入口網站中按一下 [報表]  ，然後按一下 [使用量摘要]  。
1. 選取右上角的 [**編輯 PO 號碼**]。
1. 選取 [Azure 服務]  選項按鈕。
1. 從日期範圍下拉式功能表中選取 [發票期間]  。 您可以在收到發票通知後的七天內或發票付費之前 (以先發生者為準) 編輯 PO 號碼。
1. 在 [ **PO 號碼**] 欄位中輸入新的 PO 號碼。
1. 按一下 [ **儲存**] 以提交變更。

### <a name="to-update-the-marketplace-purchase-order-number"></a>若要更新 Marketplace 採購單號碼：

1. 在 Azure EA 入口網站中按一下 [報表]  ，然後按一下 [使用量摘要]  。
1. 選取右上角的 [**編輯 PO 號碼**]
1. 選取 [Marketplace]  選項按鈕
1. 從日期範圍下拉式功能表中選取 [發票期間]  。 您可以在收到發票通知後的七天內或發票付費之前 (以先發生者為準) 編輯 PO 號碼。
1. 在 [ **PO 號碼**] 欄位中輸入新的 PO 號碼。
1. 按一下 [ **儲存**] 以提交變更。

## <a name="cadence-of-azure-ea-billing"></a>Azure EA 計費的頻率

### <a name="billing-intervals"></a>計費間隔

針對您購買的 Microsoft Azure 服務承諾用量，Microsoft 會在其每年的註冊生效日當天向您開立帳單，而對於使用量超過承諾用量的部分，則會在期末收費。 承諾用量費用的報價基礎是每月費率，且會每年預先收取。 超額費用會每月計算，並於計費週期結束時收取。

根據您選擇購買承諾用量的方式，您年度承諾用量的有效期，會與註冊週年日或一年期修訂訂用帳戶的生效日期相符。

您將根據您的註冊開始日期和設定收到超額發票。

**開始日期早於 2018 年 5 月1 日的直接註冊**：直接 Enterprise Azure (EA) 客戶的 Azure 服務 (不包括 Marketplace 服務) 會設定為年度計費週期。 您的計費週期會以週年日為基礎。 週年日是指您的合約開始生效的日期。 您必須超過承諾用量金額 ('MC') 閾值的 150%，才會收到第一張 Azure 服務超額發票。  當服務使用量總計超過 MC 閾值的 150% 時，即會自動根據週年日將您轉換為每季計費週期。  若未超過 MC 閾值的 150%，註冊將會繼續採用年度計費週期，且您將在承諾用量年度結束時收到超額發票。

**開始日期晚於 2018 年 5 月1 日的直接註冊**：客戶的 Azure 使用量和個別計費的費用發票將會採用每月計費週期。  Azure 承諾用量金額未涵蓋的任何費用，都將計為超額款項。  

**註冊開始日期早於 2018 年 5 月 1 日的間接註冊**：間接 Enterprise Azure (EA) 客戶會設定為每季計費週期。  通路合作夥伴 (CP) 將會直接向客戶開立發票。  

**開始日期晚於 2018 年 5 月1 日的間接註冊**：開始日期晚於或等於 2018 年 5 月1 日的任何間接合約，都會按月計費。  

### <a name="increasing-commitment"></a>增加承諾用量

承諾量可以隨時增加，且會依該年度承諾用量期間剩餘的月份數計費。 例如，如果您註冊了一年期的修訂訂用帳戶，並在第六個月增加承諾用量，我們就會針對該期間剩餘的六個月對您開立發票。 其後，您的承諾用量將會在承諾用量期間的後六個月進行更新，以計算任何超額費用。

### <a name="overage"></a>超額部分

對於超額部分，您必須針對在計費期間內超出承諾用量的使用量或保留支付費用。 若要檢視個別項目超額數量的計算明細，請參閱使用量摘要報表，或連絡您的通路合作夥伴。

對於發票上的每個項目，您將會看到費用總計 (應收金額)、用來涵蓋費用的承諾用量 (已使用的承諾用量)，以及超出承諾用量的費用金額 (淨額)。  對於超過承諾用量的淨額，才會計算適用的稅額。

超額發票會自動開立。  發出通知和發票的時間，取決於客戶計費週期的結束日期。  超額通知通常會在客戶計費結束日期後的七天傳送，在此期間內，客戶可在入口網站檢閱其費用，以及更新系統產生的 PO 號碼 (此更新也可在發出超額通知之前隨時進行)。  超額發票將在其後的 7-9 天傳送。

### <a name="azure-marketplace"></a>Azure Marketplace

自 2019 年 4 月的計費週期起，客戶將開始收到單一 Azure 發票，因為我們已將所有 Azure 和 Azure Marketplace 費用整合為單一發票，而不是兩張個別的發票。 (這項變更對於澳洲、日本或新加坡的客戶並無影響。)在轉換為合併發票期間，您會收到一張個別的 Marketplace 發票。 這張最後的個別發票會顯示您的計費更新日期之前的 Marketplace 費用。 該日期之後的 Marketplace 費用將出現在您的 Azure 發票上。 在轉換期間之後，您將在合併發票上看到所有的 Azure 和 Marketplace 費用。  

### <a name="purchase-order-numbers"></a>採購單號碼

採購單預設值是系統產生的採購單號碼。 使用者可在企業版入口網站中以企業系統管理員身分登入，並瀏覽至 [報表] 區段，以更新期採購單號碼。 視窗右上角會有 PO 號碼的方塊，可讓企業系統管理員按一下 [鉛筆] 圖示，以編輯 PO 號碼。

## <a name="adjust-billing-frequency"></a>調整帳單週期

客戶的帳單週期是每年、每季或每月。 計費週期會在客戶簽署合約時決定。 每月計費是最小的計費間隔。

針對直接註冊，將計費週期從每年變更為每季需要企業系統管理員的核准。 間接註冊需要合作夥伴系統管理員的核准。 變更會在目前的帳單季結束時生效。

若要將每年或每季的計費週期變更為每月，則必須修訂合約。  對現有註冊計費週期所做的任何變更，都需要企業系統管理員的核准，或由合約中識別為「帳單連絡人」  的個人核准。 您可以在 [Azure EA 入口網站支援](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)中提交您的核准，然後選取問題類別「帳單和發票」  。  變更會在目前的帳單季結束時生效。

如果修訂 M503 已簽署，您就可以將所有合約的任何頻率移至每月計費。 

## <a name="credits-and-adjustments"></a>信用額度和調整

所有適用於您註冊的信用額度或調整，都可以在 [https://ea.azure.com](https://ea.azure.com) 的 [報表]  區段看到。

若要檢視點數，請執行下列步驟：

1. 選取 [報表]  區段。
1. 按一下 [使用量摘要]  。
1. 在右上角，將 _M_ 變更為 _C_ 檢視。
1. 擴大 Azure 服務承諾用量表格中的調整欄位。
1. 在此行中，您會看到已套用到註冊的點數和簡短說明，例如：服務等級協定點數。

## <a name="request-an-invoice-copy"></a>要求發票副本

若需要您的發票複本，請洽詢您的合作夥伴。

## <a name="overage-offset-by-customers"></a>客戶的超額抵銷

如果客戶想要將預付金用在超額部分，則必須符合下列準則：

- 客戶有已產生的超額費用但尚未付費，並且在計費服務結束日期的一年內。
- 可用的預付金金額應涵蓋產生的總費用，包括所有過去未付款的 Azure 發票。
- 必須完全關閉要求完成的計費期限。 計費會在每個月的第五天之後完全關閉。
- 必須完全關閉要求抵銷的計費週期。
- ACD 折扣是實際的新承諾用量減去為先前用量規劃的任何資金。 這項需求只適用於產生的超額費用。 這僅適用於使用預付金的服務，因此無法涵蓋市集費用。 市集費用會個別計費。
- 若客戶想要完成超額抵銷，他們可以開啟支援要求。 或者，由客戶服務團隊開啟支援要求。 若要完成此程序，客戶的 EA 系統管理員或帳單連絡人必須提供電子郵件核准。

## <a name="view-price-sheet-information"></a>檢視價位表資訊

企業系統管理員可以檢視與他們 Azure 服務註冊相關聯的價格清單。

若要檢視目前的價位表：

1. 在 Azure EA 入口網站中，按一下 [報表]  ，然後按一下 [價位表]  。
2. 檢視價位表，或按一下 [下載]  。

![顯示價位表資訊的範例](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

若要下載價格記錄清單：

1. 在 Azure EA 入口網站中，按一下 [報表]  ，然後按一下 [下載使用量]  。
2. 下載價位表。

![顯示如何下載較舊價位表的範例](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

如果您有關於價格差異的問題，可能原因如下：

先前註冊和新註冊之間的價格可能已變更。 發生價格變更的原因是，價格是在合約開始日期到結束日期之間，針對特定註冊制定的。 當註冊轉移到新的註冊時，即會遵循新合約的價格。 價格是由客戶的價位表所定義。 因此，新註冊中的價格可能較高。

如果註冊進入延長的期限，則價格也會變更。 價格會變更為隨用隨付費率。

## <a name="request-detailed-usage-information"></a>要求詳細的使用量資訊

企業系統管理員可以在 Azure EA 入口網站中檢視其使用量資料的摘要、已使用的預付金，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

若要檢視特定帳戶的詳細使用量，您可以瀏覽至 [報表]   > [下載使用量]  ，來下載使用量詳細資料報表。 報表不包含任何適用的稅金。 從使用量發生到使用量在報表上反映時，可能會有最多 8 小時的延遲時間。

針對間接註冊，您的合作夥伴必須啟用標記功能，才能看到任何成本相關資訊。

## <a name="reports"></a>報告

企業系統管理員可以在企業版入口網站中檢視其使用量資料的摘要、已使用的承諾用量金額，以及與其他使用量相關聯的費用。 費用會顯示在所有帳戶和訂用帳戶的摘要層級上。

**EA 報表**

- 使用量摘要和圖表
- 服務使用量報表
- 餘額和費用報表
- 使用量詳細資料報表
- Marketplace 費用報表
- 價位表
- 進階報表下載
- CSV 報表格式

**檢視使用量摘要報表和圖表**

1. 在 Azure EA 入口網站的左側導覽區中按一下 [報表]  ，然後檢視 [使用量摘要]  索引標籤。
1. 從左上方的日期範圍下拉式功能表中，選取所需的承諾用量期間。
1. 選取圖表上的所需期間或月份，以檢視其他詳細資料。
1. 檢視每月使用量明細的圖表，其中包含已使用的用量、服務的超額部分、個別計費的費用和 Marketplace 費用。
1. 針對選取的月份，依圖表下方的部門、帳戶和訂用帳戶進行篩選。
1. 在 [依服務收費] 明細和 [依階層收費] 明細之間做切換。
1. 詳細檢視 Azure 服務、個別計費的費用，以及 Azure Marketplace 費用。

## <a name="service-usage-report"></a>服務使用量報表

[服務使用量報表] 頁面可讓企業系統管理員檢視其服務使用量資料的摘要。 雖然此處會以摘要層級顯示所有帳戶和訂用帳戶的使用量，但您也可以依帳戶或訂用帳戶來篩選報表，以檢視詳細的使用量。

請注意，產生使用量的日期與使用量反映在此報表中的時間，也可能會有最多五天的延遲。

### <a name="to-view-the-report"></a>若要檢視報表：

1. 登入企業版入口網站。
1. 在左側導覽區中按一下 [報表]  。
1. 按一下 [使用量摘要]  索引標籤。
1. 按一下所需的日期範圍。
1. 選擇要檢視的帳戶或訂用帳戶。
1. 從 [依服務收費] 或 [依階層收費] 變更為其他檢視，以顯示不同的明細。
1. 檢視詳細資料，包括服務名稱、測量單位、使用單位、生效費率和延伸成本。

## <a name="download-csv-reports"></a>下載 CSV 報表

[每月報表下載] 頁面可讓企業系統管理員下載數個 .csv 檔案格式的報表，包括餘額和費用報表、使用量詳細資料報表、Marketplace 費用報表，以及價位表。

## <a name="to-download-reports"></a>若要下載報表：

1. 在 Azure EA 入口網站中，按一下 [報表]  。
1. 從頂端功能區選取 [使用量下載]  。
1. 選取適當月份報表旁的 [下載]  。



## <a name="csv-report-formatting"></a>CSV 報表格式

在 Azure EA 入口網站中以歐元檢視 CSV 報表的客戶，可能會遇到關於逗點和句點的格式問題。

例如，您可能會看到：

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

您應該會看到：

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 小時 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>根本原因

Excel 會將所有欄位匯入為「一般」文字，並假設數字會以數學標準分隔："1,000.00"。  因此，以句點 (.) 作為千位分隔符號，並逗點作為小數點分隔符號 (,) 的歐洲貨幣 – "1.000,00"，將會不正確地顯示。 此問題可能會隨著您的區域語言設定而不同。

### <a name="when-importing-the-csv-use-the-following-steps"></a>匯入 CSV 時，請使用下列步驟：

1.    開啟 Excel，然後移至 [檔案] > [開啟]。
1.    「文字匯入精靈」隨即出現。
1.    在 [原始資料類型] 底下，選擇 [分隔符號]  。  預設值為「固定寬度」  。
1.    點擊 [下一步]  。
1.    在 [分隔符號] 底下，選取 [逗點] 的核取方塊。 預設值為 [Tab] (取消核取)。
1.    點擊 [下一步]  。
1.    捲動至 [ResourceRate] 和 [ExtendedCost] 資料行。
1.    選取 [ResourceRate] 資料行 (會以黑色醒目提示)。
9.    在 [資料行資料格式] 區段底下，選取 [文字] (而非 [一般])。  您會看到資料行標頭從 [一般] 變更為 [文字]。
10. 針對 [延伸成本] 資料行重複步驟 8 和 9。 選取 [完成]  。

如果您讓相關聯的 Excel 自動開啟 \*.csv 檔案，則必須在 Excel 中改用「開啟」功能。 開啟 Excel，然後移至 [檔案] > [開啟]。

## <a name="balance-and-charge-report"></a>餘額和費用報表

「餘額與費用報表」可提供餘額、新購買、Azure Marketplace 服務費用、調整，以及超額部分費用的每月摘要資訊。 Azure 服務承諾用量摘要表格中的所有資料列每個月都會保持不變，而所有購買和調整的詳細資料則會顯示在 [新購買詳細資料] 和 [調整詳細資料] 區段下。

### <a name="download-the-balance-and-charge-report"></a>下載餘額和費用報表

1. 以企業系統管理員身分登入 Azure EA 入口網站。
1. 在左側導覽區中按一下 [報表]  。
1. 按一下 [報表下載]  索引標籤。
1. 在 [餘額和費用]  資料行底下選取適當的月份，然後按一下以下載報表。

## <a name="usage-detail-report"></a>使用量詳細資料報表

「使用量詳細資料報表」會提供註冊所使用之服務和數量的每月摘要，包括計量名稱、計量類型和已使用數量的資訊。

### <a name="download-the-usage-detail-report"></a>下載使用量詳細資料報表

1. 以企業系統管理員身分登入 Azure EA 入口網站。
1. 在左側導覽區中按一下 [報表]  。
1. 按一下 [下載使用量]  索引標籤。
1. 在 [使用量詳細資料]  資料行底下選取適當的月份，然後按一下以下載報表。

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Azure EA 入口網站報表中的 Marketplace 費用

您可以在[這裡](https://azure.microsoft.com/marketplace/faq/)找到 Marketplace 費用的詳細資訊。

Marketplace 費用有兩種類型：

- **依使用量：** 依交易單位測量產品。  例如，虛擬機器會按小時計費，Bing API 搜尋會依搜尋次數計費。
- **月費：** 根據使用量/存取按月計費。

若要在企業版入口網站中檢視不同的費用：

1. **使用量摘要報表 –** **同時**顯示依使用量和月費的 Marketplace 費用。
1. **Marketplace 費用報表 –** **僅**顯示依使用量的 Marketplace 費用。  一次性費用不會顯示。

請注意，Azure Marketplace 不適用於 MPSA 註冊。

## <a name="advanced-report-download"></a>進階報表下載

如需報告特定的日期範圍或帳戶，可以使用進階報表下載。 自 2016 年 8 月 30 日起，輸出檔案的格式已從 .xlsx 變更為 .csv，以容納較大的記錄集。

1. 選取 [進階報表下載]  。
1. 選取 [適當日期範圍]  。
1. 選取 [適當帳戶]  。
1. 選取 [要求使用量資料]  。
1. 選取 [重新整理]  按鈕，直到報表狀態更新為 [下載] 為止。
1. 下載報表。

## <a name="reporting-for-non-enterprise-administrators"></a>非企業系統管理員的報告

企業系統管理員可為註冊的部門系統管理員 (DA) 和帳戶擁有者 (AO) 啟用檢視成本的存取權。 在啟用後，帳戶擁有者即可下載其帳戶和訂用帳戶專屬的 .csv 報表，並以視覺化方式在企業版入口網站的報告區段下檢視資訊。

### <a name="to-enable-access"></a>若要啟用存取權：

 1. 以企業系統管理員身分登入。
 1. 按一下左側導覽區上的 [管理]  。
 1. 按一下 [註冊]  索引標籤。
 1. 在 [註冊詳細資料] 區段底下，選取下列項目旁的鉛筆圖示：
    - DA 檢視費用
    - AO 檢視費用
 1. 選取 [啟用]  。
 1. 按一下 [檔案]  。

### <a name="to-view-reports"></a>若要檢視報表：

1. 以部門系統管理員或帳戶擁有者的身分登入 Azure EA 入口網站。
1. 在左側導覽區中按一下 [報表]  。
1. 按一下 [使用量摘要]  索引標籤，以視覺化方式檢視帳戶和訂用帳戶的資訊。
1. 按一下 [使用量下載]  以檢視 .csv 報表。

在使用「進階報表下載」  功能時，部門系統管理員和帳戶擁有者無法檢視費用。 成本會顯示為 $0。

AO 檢視費用的存取權會擴及帳戶擁有者，以及所有具有相關訂用帳戶權限的使用者。 如果您是間接客戶，則必須由通路合作夥伴為您啟用成本功能。

## <a name="move-usage-data-to-another-enrollment"></a>將使用量資料移至另一個註冊

只有在追溯轉移時，才需移動使用量資料。 有兩個選項可將使用量資料從一個註冊移至另一個註冊：

- 將帳戶從一個註冊轉移至另一個註冊
- 將註冊從一個註冊轉移至另一個註冊

無論是哪一個選項，您都必須向 EA 支援小組提交[支援要求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)，以取得協助。 

## <a name="azure-ea-pricing-overview"></a>Azure EA 定價概觀

本文件會詳細說明使用量的計算方式，並解答許多與企業方案中各種不同 Azure 服務的定價 (其計算方式更為複雜) 有關的常見問題。

### <a name="price-protection"></a>價格保護

客戶和通路合作夥伴 (在我們間接通道的案例中) 保證可獲得等於或低於其客戶價位表 (CPS) 中所指定的價格，或等於或低於其 Azure 購買生效日當天生效的價格。 此價格稱為基準價格。 就購買後導入的服務而言，此價格會是服務首次導入時依適用層級的折扣生效的價格。 此價格保護適用於承諾用量的持續期間，可能是一年或三年，視企業方案而定。

### <a name="price-changes"></a>價格變更

Microsoft 可能會在註冊期間內，就個別的 Azure 服務調降目前的企業方案價格。  我們會在較低的價格生效時，將這些調降的費率擴及現有的客戶。  如果這些費率後續又調升，註冊的服務價格增加的幅度將不會超過前述定義的客戶價格保護上限，但可能會比先前較低的價格高。  無論是哪一種情況，Microsoft 都會傳送電子郵件給註冊的相關企業和合作夥伴管理員，以通知客戶和間接通路合作夥伴即將進行價格變更。

### <a name="current-effective-pricing"></a>目前的有效定價

客戶和通路合作夥伴可藉由登入 [Azure 企業版入口網站](https://ea.azure.com/)並瀏覽至該註冊的價位表頁面，來檢視其目前的註冊定價。  如果您是透過我們其中一個通路合作夥伴間接購買 Azure，且他們並未為您的註冊啟用顯示標記價格的功能，您就必須經由通路合作夥伴取得定價更新。

### <a name="introduction-of-new-azure-services"></a>導入新的 Azure 服務

我們除了持續強化 Azure 外，也會定期新增與現有服務分開定價的新服務。  有些預覽服務會自動啟用，有些則需由客戶在 [Azure 帳戶入口網站](https://account.windowsazure.com/PreviewFeatures)中操作，才可供使用。 另請注意，當服務從預覽進入 GA 階段時，價格可能會隨著完整效能和可靠性 SLA 的套用而增加。 再者，某些服務提供在首次導入時生效的促銷價格，但日後價格有可能會調升。 任何從預覽或促銷價格轉換為 GA 的這類調升，都不受一般基準價格保護的限制，且將套用至這些服務後續的使用量。 客戶可以選擇不使用新服務，以避免產生這些服務的相關費用。

### <a name="introduction-of-regional-pricing"></a>導入區域定價

除了導入新的服務以外，服務也會隨著服務的區域支援有所增加，而定期從單一全域基準變更為更具區域性的模式。 服務的區域化首次導入時，相較於註冊先前的有效全域價格，這些新區域會套用價格保護。 不過，後續為這個相同服務導入的其他區域，將被視為新服務，並且為其提供獨立於基準價格保護以外的個別費率。

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN 開發/測試

企業系統管理員可讓帳戶擁有者根據 EA MSDN 開發/測試供應項目建立訂用帳戶。 為了讓此功能正確運作，帳戶擁有者必須設定基礎 MSDN 訂閱者所需的 EA MSDN 開發/測試訂用帳戶。 這可讓有效的 MSDN 訂閱者以特殊的開發/測試費率在 Azure 上執行開發和測試工作負載。 如需詳細資訊，請參閱 [EA MSDN 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="enterprise-program-usage-calculation-guidelines"></a>企業方案使用量計算指導方針

請參閱 [Azure 服務](https://azure.microsoft.com/services/)和 [Azure 定價](https://azure.microsoft.com/pricing/)，以取得每項個別服務的基本公開定價資訊、測量單位、常見問題，以及使用量報告指導方針。 此外，在計算服務使用量時，應遵循下列企業方案指導方針。

### <a name="enterprise-program-units-of-measure"></a>企業方案的測量單位

企業方案的測量單位常會與我們其他方案 (例如 Microsoft Online Services 合約方案 (MOSA)) 中顯示的不同。 這表示，許多服務的測量單位都會進行彙總，以提供標準化的定價。 企業版入口網站的 [使用量摘要] 檢視中顯示的測量單位，一律為企業量值。 [易記服務名稱](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx)中提供了每項服務目前的測量單位和轉換的完整清單。

### <a name="rounding-rules"></a>進位規則

在企業版入口網站內進行的進位，會使用 IEEE 標準銀行家進位法或高斯進位邏輯。 此方法會將半位數值進位至最接近的偶數位數值，不同於多數半進位的進位機制一律會將半位數值進位至下一個最高的數值。 相較於標準 Excel 邏輯，此方法實際上可提供比群組更精確的總和。

如果第一個捨入的數位值為 5，且其後沒有任何數位值，或其後的數位值為零，則使前一個數位值成為偶數 (也就是捨入至最接近的偶數位數值)。 例如，2.315 和 2.325 在捨入至最接近的百分位時，都是2.32。

以下資訊供您參考：在使用 Excel 為企業版入口網站內使用的進位和轉換規則建立模型時，應使用 MROUND 公式，如下所示。

| 案例 | 銀行家邏輯公式 |
| --- | --- |
| 進位用法 | =MROUND({_source_}, 0.0002) |
| 進位定價 (2 個小數位數) | =MROUND({_source_}, 0.02) |
| 進位定價 (0 個小數位數) | =MROUND({_source_}, 2) |

**表格** **2** **– 雲端服務和虛擬機器的時數轉換**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>下載使用量檔案與使用量摘要入口網站檢視之間的轉換

如同下載使用量資料報表中顯示的，原始資源使用率資料的報告最多可達六個小數位數。 不過，Azure 企業版入口網站會將使用量捨入至四個小數位數作為承諾用量單位，並將超額單位截斷為零個小數位數。 原始使用量會先捨入為四個小數位數，再轉換為企業測量單位，而產生的企業單位會再次捨入為四個小數位數。 轉換前的實際使用時數只會顯示在下載使用量報表中，而不會顯示於 UI 本身。

例如，假設我們在下載使用量報表中報告了實際 SQL Server 時數 694.533404。 這些單位隨後會依 100 計算時數轉換為 6.94533404 個單位，然後再捨入至 6.9453 顯示於企業版入口網站中。

然後，這些單位會直接乘以承諾用量單位價格，且結果會截斷為兩個小數位數，以決定應收帳單金額。 針對日圓 (JPY) 和韓元 (KRW)，應收金額會捨入為零個小數位數。

在這個相同的超額範例中，可計費單位會截斷為六，然後乘以超額單位價格，以決定應收帳單金額。 針對受控服務提供者 (MSP) 的計費，所有與標示為 MSP 的部門相關聯的使用量在轉換為 EA 測量單位之後，都會截斷為零個小數位數，其運作方式與超額報告相同。 因此，此使用量的總和可能會低於 UI 中報告的所有使用量的總和，這取決於 MSP 是仍在其承諾用量金額的餘額內，或是已經超額。

### <a name="graduated-pricing"></a>分級定價

企業方案定價目前不包含分級定價 (在分級定價中，每單位定價會隨著使用量的增加而降低)。 當您從採用分級定價的 MOSA 方案轉換為企業方案時，在調整企業方案折扣 (如果適用) 後，定價的設定會以該服務的基本層為準。

### <a name="partial-hour-billing"></a>零散時數計費

所有使用量都會根據轉換為零散時數的分鐘數來計費，而不是以一小時作為增量單位。  以小時為單位列出的企業費率，會直接套用至包含任何零散時數的總時數。

### <a name="average-daily-consumption"></a>平均每日使用量

如果一項服務按月計費，但使用量每天都會報告，則會每天評估一次此使用量，然後除以 31，再依該計費月份的天數加總。 如此，任何月份的費率都絕不會超過預期，而少於 31 天的月份則會有略低的費率。

### <a name="compute-hours-conversion"></a>計算時數轉換

過去 A0、A2、A3 及 A4 的標準與基本虛擬機器和雲端服務的所有使用量，均以 A1 虛擬機器計量分鐘數的分數 (A0) 或倍數 (A2、A3 及 A4) 呈現。 這對客戶造成了些許混淆，因此我們施行了變更，讓 A0、A2、A3 及 A4 計量有專屬的每分鐘使用量。

新的計量方式已在 2016 年 1 月 27 日到 1 月 28 日之間生效。 在此轉換期間的部署中，您會發現 .csv 下載中有兩個明細項目：一個項目是 A1 計量所呈現的使用量，另一個則是與部署大小相對應的新專數計量所呈現的使用量。

| **部署大小** | **至 2016 年 1 月 26 日止，使用量是以 A1 的倍數呈現** | **自 2016 年 1 月 27 日起，使用量改以專屬計量呈現** |
| --- | --- | --- |
| A0 | 0.25 倍的 A1 時數 | 1 倍的 A0 時數 |
| A2 | 2 倍的 A1 時數 | 1 倍的 A2 時數 |
| A3 | 4 倍的 A1 時數 | 1 倍的 A3 時數 |
| A4 | 8 倍的 A1 時數 | 1 倍的 A4 時數 |

### <a name="regions"></a>區域

下表針對區域會對定價產生影響的服務，顯示了地理位置和區域的對應：

| 地理區域 | 資料傳輸區域 | CDN 區域 |
| --- | --- | --- |
| 區域 1 | 北歐 <br> 西歐 <br> 美國西部 <br> 美國東部 <br> 美國中北部 <br> 美國中南部 <br> 美國東部 2 <br> 美國中部 | 北美洲 <br> 歐洲 |
| 區域 2 | 亞太地區東部 <br> 亞太地區東南部 <br> 日本東部 <br> 日本西部 <br> 澳洲東部 <br> 澳大利亞東南部 | 亞太地區 <br> 日本 <br> 拉丁美洲 <br> 中東/非洲 <br> 澳洲東部 <br> 澳大利亞東南部 |
| 區域 3 | 巴西南部 |   |

**表格** **4** **– 資料傳輸區域**

位於相同資料中心內的服務 (例如 O365 和 Azure) 之間的資料輸出不會產生任何費用。

### <a name="monetary-commitment-and-unbilled-usage"></a>預付金和未開立帳單的使用量

Azure 預付金是針對 Azure 服務預付的金額。 使用服務時就會用到預付金。 第一方 Azure 服務會使用預付金。 不過，個別計費和市集服務的費用屬於例外。

### <a name="charges-billed-separately"></a>個別計費的費用

某些由第三方來源提供的產品和服務並不會使用 Azure 承諾用量金額。 這些項目會在標準計費週期的超額發票中個別計費。

我們已將所有 Azure 和 Marketplace 費用整合為符合註冊計費週期的單一發票。 (這項變更不適用於澳洲、日本或新加坡的客戶。)

合併的發票會先顯示 Azure 使用量，再顯示任何 Marketplace 費用。 澳洲、日本或新加坡的客戶仍會在個別發票上看到 Marketplace 費用。

如果標準計費週期結束時並無超額使用量，則個別計費的費用將會個別開立發票。 (適用於澳洲、日本或新加坡的客戶)

**個別計費的服務包括：**

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 的註冊使用者
- Openlogic
- 遠端存取權限 XenApp Essentials 已註冊的使用者
- Ubuntu Advantage
- Visual Studio Enterprise (每月)
- Visual Studio Enterprise (每年)
- Visual Studio Professional (每月)
- Visual Studio Professional (每年)

## <a name="azure-marketplace-for-ea-customers"></a>適用於 EA 客戶的 Azure Marketplace

直接客戶的 Marketplace 費用會顯示在 Azure EA 入口網站上。 Marketplace 購買和使用量都會在承諾用量金額以外按季/按月於期末計費。

間接客戶可以在 Azure EA 入口網站的 [管理訂用帳戶] 頁面上找到其 Azure Marketplace 訂用帳戶，但價格將會隱藏。 客戶應該連絡其 LSP 以了解相關的 Marketplace 費用資訊。

新的每月或每年週期性 Marketplace 購買，將在購買 Marketplace 項目的期間內以完整金額計費。 這些項目會在原始購買下一個期間的同一天自動續約。

這並不會對任何現有的週期性 Marketplace 費用產生影響。 每月週期性費用會繼續在每個日曆月份的第一天續約，而年度費用則會在購買屆滿一年的當天續約。

Azure Marketplace 上提供的某些第三方轉銷商服務會使用您 Enterprise 合約 (EA) 的承諾用量金額餘額。 這些服務原先在 EA 承諾用量範圍外計費，並另外開立發票。 Marketplace 中這些服務的 EA 承諾用量金額有助於簡化客戶購買及款項管理。 如需目前使用承諾用量金額的完整服務清單，請參閱 [Azure 網站](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14)。

### <a name="partners"></a>合作夥伴

LSP 可瀏覽至 Azure EA 入口網站中的價位表，然後按一下右上角的 [Marketplace 價目表]  連結，以下載 Marketplace 專用的價目表。 Marketplace 價目表會反映所有可用的服務及其價格。

**若要下載價目表，請執行下列步驟：**

1. 瀏覽至 [報表] > [價位表]。
1. 在右上角，找出您的使用者名稱底下的 Azure Marketplace 價目表連結。
1. 以滑鼠右鍵按一下該連結，然後選取 [另存目標]  。
1. 在 [儲存] 視窗上，將文件的標題變更為 _AzureMarketplacePricelist.zip_，這會將檔案從 xlsx 變更為 zip 檔案。
1. 下載完成後，您將取得一個 zip 檔案，內含特定國家/地區的價目表。
1. LSP 應參考個別的國家/地區檔案，以了解特定國家/地區的定價。 LSP 應使用 [通知] 索引標籤查看在 Marketplace 中淨新增的 SKU，以及已移除的 SKU。
1. 價格不會頻繁變更，但在有所變更時，LSP 將在 30 天前收到價格調升和 FX 變更的電子郵件通知。
1. LSP 的每個註冊每季會收到每個 ISV 的一張發票。

### <a name="enabling-marketplace-purchases"></a>啟用 Marketplace 購買

企業系統管理員能夠為註冊下的所有 Azure 訂用帳戶「停用」或「啟用」Marketplace 購買。 如果企業系統管理員停用購買，而某些 Azure 訂用帳戶已有 Marketplace 訂用帳戶，這些 Marketplace 訂用帳戶將不會取消或受到影響。

雖然客戶可在 Azure EA 入口網站中將其直接 Azure 訂用帳戶與註冊產生關聯，藉以將訂用帳戶轉換至 EA，但此動作並不會自動轉換子系 Marketplace 訂用帳戶。

**若要啟用 Marketplace 購買：**

1. 以企業系統管理員身分登入 Azure EA 入口網站。
1. 瀏覽至 [管理]  。
1. 在 [註冊詳細資料]  底下，按一下 _Azure Marketplace_ 明細項目旁的鉛筆圖示。
1. 視需要切換 [啟用/停用]  或 [僅限免費 BYOL SKU]\*  。
1. 按一下 [檔案]  。

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Azure EA 入口網站報表中的 Marketplace 費用

您可以在[這裡](https://azure.microsoft.com/marketplace/faq/)找到 Marketplace 費用的詳細資訊。

Marketplace 費用有兩種類型：

- **依使用量：** 依交易單位測量產品。  例如，虛擬機器會按小時計費，而 Bing API 搜尋會依搜尋次數計費。
- **不依使用量：** 與使用量無關的一次性費用或週期性月費。

在 Marketplace 費用報表中會同時擷取依使用量和不依使用量的費用。

請注意，Azure Marketplace 不適用於 MPSA 註冊。

\*[BYOL (自備授權)] 和 [僅限免費] 選項會使 Azure Marketplace SKU 的購買和取得僅限於 BYOL 和免費 SKU。

### <a name="services-billed-hourly-for-ea"></a>EA 的服務按小時計費

相較於 MOSP 中的每月費率，EA 下的應用程式傳遞網路和 Web 應用程式防火牆會按小時計費。

### <a name="remote-app"></a>遠端應用程式

EA 客戶將根據其 EA 價格等級支付遠端應用程式的費用，且沒有額外的費用。 標準價位包含最初的 40 小時，而無限制的價位則涵蓋最初的 80 小時。 超過 80 小時後，遠端應用程式即停止發出使用量。

## <a name="azure-marketplace-faq"></a>Azure Marketplace 常見問題集

此常見問題集文件會討論 Azure 承諾用量金額對於 Azure Marketplace 之中某些第三方發佈服務的適用性有何更新。

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>我們在 Marketplace 服務和 Azure 承諾用量金額方面會有何變更？

除了 Azure 保留的 VM 執行個體 (RI) 以外，客戶從 Azure Marketplace 購買的所有服務，都會開立個別的發票。 我們正著手擴充 Azure 承諾用量金額的適用性，以納入客戶最常購買的一些由第三方發佈的 Azure Marketplace 服務。

### <a name="why-are-we-making-this-change"></a>為何要進行此變更？

一直以來，客戶都希望能有其他方式可充分運用其以 Azure 承諾用量金額的形式預付的款項。  我們藉由將 MC 擴充到這些服務，來因應客戶普遍的要求，並且為我們大部分的 Azure Marketplace 客戶帶來助益。

### <a name="what-is-the-customer-benefit"></a>什麼是客戶權益？

客戶可獲得更簡單的計費體驗，並確保他們能夠使用其 Azure 承諾用量金額。  使用 MC 將此權益新增至預付的承諾用量金額和 RI，可進一步提高 Azure 承諾用量金額的價值。

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>使用哪些服務時會扣減 Azure 承諾用量金額？我的客戶要如何得知？

在 Azure Marketplace 購買體驗期間，我們會以免責聲明區分會使用承諾用量金額的每項服務。 目前支援的發行者包括 Red Hat、SUSE、Autodesk 和 Oracle 發佈的特定服務。 具有類似命名慣例、但並非由前述廠商發佈的服務，將不會扣減承諾用量金額。 此常見問題的結尾處提供了完整清單。

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>客戶的承諾用量金額用完時，會怎麼樣？

客戶用完其所有的 MC 且已產生超額部分時，這些服務的相關費用將連同任何其他取用服務顯示在下一張超額發票中。  這是一項變更。過去，這些費用會隨其他 Azure Marketplace 供應項目開立在其本身的發票上。

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>我們為何不讓所有 Marketplace 購買都啟用 Azure 承諾用量金額？

我們一直都在設法提供 Azure 承諾用量金額方面的最佳客戶體驗。 這項變更將使許多客戶受惠，並解決在 Azure Marketplace 中的總支出比例偏高的問題。 未來可能會新增其他服務。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>這對間接註冊和合作夥伴有何影響？

我們的間接註冊客戶或合作夥伴將不受影響。 這些服務將與其他取用服務一樣，受到相同合作夥伴標記功能的控管。 唯一的變更將是其顯示所在的發票，以及從承諾用量金額支付的費用。

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>會扣減 Azure 承諾用量金額的服務清單

特定 Azure Marketplace 供應項目可以使用承諾用量金額。 如需參與此方案的完整產品清單，請參閱 [Azure 預付金](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。

## <a name="additional-information"></a>其他資訊

請參閱下列額外資訊來源，以取得詳細資訊。 這些檔案會在每個月的 6 日和 20 日進行更新。 每個檔案的詳細資料顯示如下：

| 附錄標題 | 說明 | URL 命名慣例 |
| --- | --- | --- |
| [**易記服務名稱**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 列出所有作用中的服務，以及服務類別、易記服務名稱、承諾用量名稱和部分編號、取用名稱和部分編號、測量單位，以及報告的使用量與顯示的企業版入口網站使用量之間的轉換因子。 | Friendly\_Service\_Names.xlsx |
| [**服務下載欄位**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 此試算表會列出「使用量下載報表」中的服務相關欄位所有的可能組合。 | Service\_Download\_Fields.xlsx |

**表格** **5** **– 其他資訊來源**

## <a name="power-bi-reporting"></a>Power BI 報告

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro 現已可供 EA 客戶使用。 使用 Power BI Pro，您將可透過額外的共同作業和資料重新整理功能產生和共用報表，以有效率地管理您的成本資料。 Power BI Pro 提供了更高的資料容量和資料串流限制。 我們即將為 Azure 企業版客戶推出絕佳的全新成本管理功能。

目前使用 Microsoft Azure 使用量見解內容套件的 Power BI 免費使用者，有資格體驗 60 天的 Power BI Pro 免費試用版。 如果您想要在免費試用之後繼續使用 Power BI Pro，只要新增授權即可。

若要註冊免費試用版，請移至齒輪圖示，然後選取 [管理個人儲存體]  。 接著，選取右側的 [免費試用專業版]  。 如需 Power BI Pro 免費試用版的詳細資訊，請參閱 [Power BI 自助式註冊](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial)。

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Microsoft Azure EA Power BI Pro 試用版條款

- **一般用途**：「Microsoft Azure 企業版」的延長 Power BI Pro 內容套件試用版供應專案 (簡稱「供應專案」) 可供現有合格使用者在供應項目期間內使用，讓他們能夠使用特定 Power BI 內容套件，存取與其 Microsoft Azure 使用量有關的特定見解。
- **資格**：Enterprise 合約 (EA) 下的使用者若已具備其組織的 Microsoft Azure 帳單、服務、服務及/或成本管理的相關功能，即可參與此「供應項目」。
- **排除項目**：
  - 已參與延長 Power BI Pro 試用版的使用者，仍將符合既有供應項目的參與資格，但無法加入 Azure EA Power BI Pro 試用版供應項目。
  - 參與此供應項目的使用者只能將 Power BI Pro 用於 Microsoft Azure 企業版內容套件。 禁止將 Power BI Pro 用於任何其他用途。
  - 期間：供應項目的開始日期為 2017 年 6 月 1 日，並於 2018 年 5 月 31 日結束。  使用者可在這 12 個月內隨時接受供應項目，但所有使用者的供應項目皆於 2018 年 5 月 31 日終止，無論其接受供應項目的時間點為何。

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>若要存取 Microsoft Azure 使用量見解內容套件：

1. 瀏覽至 [[Microsoft Azure 使用量見解]](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)。 按一下 [立即取得]  。
1. 提供註冊號碼和月數。 按 [下一步]  。
1. 提供您的 API 存取金鑰以進行連線。 您可以在[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)中找到註冊的金鑰。 按一下 [登入]  。
1. 匯入程序會自動開始。 完成時，新的儀表板、報表和模型將會出現在導覽窗格中。 請按一下儀表板以檢視匯入的資料。

如需如何為您的註冊產生 API 金鑰的詳細資訊，請瀏覽[企業版入口網站](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)上的 API 報表說明檔。 如需新內容套件的詳細資訊，請下載 [Microsoft Azure 使用量見解](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)文件。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>若要存取舊版 Power BI EA 內容套件：

 1. 瀏覽至 [Power BI 網站](https://app.powerbi.com/getdata/services/azure-enterprise)。
 1. 使用有效的公司或學校帳戶登入。
    - 公司或學校帳戶可以是用於透過 Azure EA 入口網站存取註冊的相同或不同帳戶。
 1. 在服務的儀表板上選取 [Microsoft Azure 企業版]  ，然後按一下 [連線]  。
 1. 在 [連線到 Azure 企業版] 畫面上，選擇：
    - Azure 環境 URL：[https://ea.azure.com](https://ea.azure.com/)。
    - 月數：選擇 1 到 36 之間的數字。
    - 註冊號碼：輸入註冊號碼。
    - 按 [下一步]  。
 1. 在 [驗證金鑰] 方塊上，輸入 API 金鑰。 您可以在 Azure 企業版入口網站的 [下載使用量] 索引標籤下取得 API 金鑰，然後按一下 [API 存取金鑰]
    - 將金鑰複製並貼到 [帳戶金鑰] 的方塊中
 1. 資料需要約 5-30 分鐘的時間才能載入至 Power BI，視資料集的大小而定。

Power BI 報告適用於能夠檢視帳單資訊的 EA 直接、合作夥伴和間接客戶。

## <a name="report-faq"></a>報表常見問題集

本文的這一節會解答與解讀報表有關的常見問題。

### <a name="why-is-my-cost-showing-as-0"></a>為何我的成本顯示為 $0？

**直接註冊** 如果您是帳戶擁有者或部門系統管理員，請洽詢 EA 系統管理員以啟用定價功能：

1. 按一下左側導覽區上的 [管理]  。
1. 按一下 DA (部門系統管理員) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用]  並儲存。
1. 按一下 AO (帳戶擁有者) 檢視費用旁的藍色鉛筆。
1. 選取 [啟用]  並儲存。

此動作將使帳戶擁有者和部門系統管理員能夠存取使用量報表上的成本/定價資訊。

**間接註冊** 請向您的合作夥伴確認他們已為您啟用定價功能。 此動作只能由合作夥伴完成，在開啟此功能後，您就能以 EA 系統管理員的身分檢視註冊的成本和定價。

如果您想要為帳戶擁有者和部門系統管理員啟用檢視費用功能，請依照先前列於**直接註冊**下的步驟操作。

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>使用量詳細資料報表沒有任何 SKU 資訊

使用量詳細資料報表中並沒有 SKU 資訊；不過，您可以檢視此報表中使用的服務資訊。 接著，您可以下載價位表報表以取得 SKU 資訊。

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>Marketplace 上的總金額不符合使用量摘要和 CSV 報表

「Marketplace 費用報表」只會顯示依使用量的 Marketplace 費用。 一次性費用不會顯示。 您可以參考使用量摘要頁面，以取得依使用量的費用和一次性費用的最新使用量。

### <a name="there-is-no-information-on-my-api-report"></a>我的 API 報表上沒有任何資訊

API 金鑰每六個月就會過期。 如果發生此問題，請產生新的 API 金鑰。 此外也請務必要求您的 EA 系統管理員產生新的 API 金鑰，並遵循 API 報表常見問題集中的步驟。

### <a name="my-power-bi-report-isnt-working"></a>我的 Power BI 報表無法運作

Power BI 發生問題時，請經由 [https://support.powerbi.com](https://support.powerbi.com) 向 Power BI 小組提出技術票證，讓他們協助您。

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>我的資源標籤未顯示在報表上

資源標籤可從 Azure 入口網站管理。 您可以經由 [https://portal.azure.com](https://portal.azure.com) 聯絡 Azure 訂用帳戶小組。 請依照[此連結](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)上的步驟提出支援要求。

### <a name="why-does-my-resource-rate-change-every-day"></a>為何我的資源費率每天都會變更？

詳細使用量報表中的「資源費率」是一個計算值，代表對服務收取的每月平均費率。 此費率會使用服務單位的每月承諾用量和每月超額費用的平均值來計算。 根據承諾用量和超額費率計費的使用量部分，將會持續變更到該月結束的當天為止。 因此，資源費率也會在當月期間持續變更。 資源費率會在當月結束後的第五天鎖定。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>資源費率計算程序的詞彙

**原始單位總計：** 詳細使用量報表中的已使用數量。
**每單位的 MOCP 資源：** 上游使用量系統會以不同的單位發出每項服務的使用量。 (預設值) **每單位使用量：** EA 測量單位。 (預設值) **價格：** Azure EA 入口網站的單價。
**總成本：** 詳細使用量報表中的延伸成本，或 Azure EA 入口網站中已使用的承諾用量 + 超額部分。


### <a name="charges-calculation"></a>費用計算

**轉換為每單位的 MOCP 資源** = ROUND(原始單位總計 * 每單位的 MOCP 資源，4) **轉換為單位** = 轉換為每單位的 MOCP 資源後的單位/每單位使用量 **總成本** = 單位 * 價格

### <a name="download-usage-calculation-logic"></a>下載使用量計算邏輯

**資源費率** = 總成本/(原始單位總計/每單位的 MOCP 資源)

資源費率是根據費用衍生而來的，通常與價位表中的實際單價不相符。

計算超額費用時，如同下載使用量資料報表中顯示的，原始資源使用率資料的報告最多可達六個小數位數。 不過，Azure EA 入口網站會將使用量捨入至四個小數位數作為承諾用量單位，並將超額單位截斷為零個小數位數。 這表示，在 Azure EA 入口網站中，對於所有以超額部分計費的使用量，我們都只會收取完整單位的費用。 以超額部分或混合月份計費的使用量，在單價和資源費率之間會有很大的差異。

## <a name="next-steps"></a>後續步驟
- 如需了解發票和費用的相關資訊，請參閱[了解您的 Azure Enterprise 合約帳單](billing-understand-your-bill-ea.md)。
- 若要開始使用 Azure EA 入口網站，請參閱[開始使用 Azure EA 入口網站](billing-ea-portal-get-started.md)。
