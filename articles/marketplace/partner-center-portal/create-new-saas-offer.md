---
title: 在商業的 Marketplace 中建立新的 SaaS 供應項目
description: 如何為服務 (SaaS) 供應項目清單或銷售的 Azure Marketplace、 AppSource 或透過雲端解決方案提供者 (CSP) 計劃使用在 Microsoft 合作夥伴中心上的商業 Marketplace 入口網站建立新的軟體。
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 32537426b7b9b1a7015610fc0c3e2dd7c3efa49b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806202"
---
# <a name="create-a-new-saas-offer"></a>建立新的 SaaS 供應項目

若要開始建立服務 (SaaS) 提供的軟體，請確定您第一次[建立合作夥伴中心帳戶](./create-account.md)，然後開啟[商業 Marketplace 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，使用**提供**選取的索引標籤。 

![在合作夥伴中心上的商業 Marketplace 儀表板](./media/commercial-marketplace-offers.png)

選取 +**建立新的...** 按鈕，然後選取**軟體即服務**功能表項目。 

如果您選取其中一個其他供應項目類型，您將會重新導向至較舊[Cloud Partner 入口網站](https://cloudpartner.azure.com/)。  唯一的 SaaS 供應項目可在合作夥伴中心商業 Marketplace 入口網站中這一次。 

![在合作夥伴中心建立供應項目 視窗](./media/new-offer.png)


**新的供應項目**對話方塊隨即出現。 ![[新增供應項目] 對話方塊](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>供應項目識別碼和名稱

- **供應項目識別碼**：在您的帳戶中建立每個優惠的唯一識別碼。 此識別碼會顯示在 marketplace 供應項目與 Azure Resource Manager 範本 （如果適用） 的 URL 位址中的客戶。 供應項目 ID 必須是小寫字母、 英數字元 （包括連字號和底線，但沒有空格）。 這是限制為 50 個字元，而且無法更新您選取之後建立。  
範例： 測試供應項目 1
<br>產生的 URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **供應項目名稱**：SaaS 應用程式供應項目，在發行集、 公告和網站一致的官方名稱。  此名稱可能 trademarked。  提供名稱不能包含空白字元，emoji （除非它們是商標或著作權符號），而且必須是限制為 50 個字元。
<br>範例：測試供應項目 1&#8482;

選取 [建立] 。  **提供概觀**頁面已經為此供應項目。  

![在合作夥伴中心上的供應項目概觀](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>供應項目概觀

**提供概觀**頁面包含： 

- **發佈狀態**來發佈此供應項目和每個步驟需要多久才能完成所需的步驟以視覺化方式顯示。 不完整的發行步驟圖示會呈現灰色。 

- **提供概觀**功能表就會包含此供應項目上執行作業的連結清單。 此作業的清單會隨著您選取的供應項目的。  
    - 如果供應項目為草稿 – 刪除草稿 
    - 如果供應項目是即時 – 停止銷售的供應項目 
    - 如果供應項目處於預覽狀態-上線 
    - 如果您尚未完成發行者登出 – 取消發行

## <a name="offer-setup"></a>供應項目設定

**提供安裝** 索引標籤會要求下列資訊。 選取 **儲存**完成這些欄位之後。

- **您想要透過 Microsoft 銷售嗎？** （是/否）
    - **是**，您想要銷售您的供應項目，透過 Microsoft，與裝載您的名義; 上的 marketplace 交易的 Microsoft 或 
    - **否**，您不希望只是列出您的供應項目，透過市集、 處理獨立 Microsoft 於任何貨幣交易。    

### <a name="sell-through-microsoft"></a>透過 Microsoft 銷售

透過 Microsoft 銷售提供更好的客戶探索和擷取，可讓 Microsoft 代表您的主應用程式 marketplace 交易，並利用 Microsoft 的全域可用的商務功能。

#### <a name="saas-offer-requirements"></a>SaaS 供應項目需求

若要透過合作夥伴中心上的商業 Marketplace 提供的服務 (SaaS)，請列出軟體，必須符合下列準則：

- 您的供應項目必須與 Azure 的用戶端相容。 (通常 SaaS 應用程式也會裝載於 Azure 上取得最佳的效能及相容性，但這不一定需要。) 
- 必須使用您的供應項目[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/)身分識別管理和驗證。
- 必須使用您的供應項目[履行的 SaaS Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2)整合與 Azure Marketplace。

#### <a name="billing-infrastructure-costs"></a>基礎結構成本計費
SaaS 供應項目，您，為 「 發行者 」，必須考慮 Azure 基礎結構使用費用，以及軟體授權費用為單一的成本項目。 這項成本被以客戶的一般每月費用。 管理，且直接會計入您的合作夥伴，azure 基礎結構使用方式。 客戶不會看到實際的基礎結構使用量費用。 發行者通常選擇將 Azure 基礎結構使用量費用算在他們的軟體授權定價中。 

軟體授權費用會以每月、 週期性站台為基礎的訂用帳戶的固定費率收費，且不計量或使用情況為基礎。

|**您的授權成本**|**每月美金 $100 元**|
|:---|:---|
|Azure 使用量成本 (D1/1 核心)|直接向發行者收費，不是向客戶收費|
|Microsoft 向客戶收費|每個月 （「 發行者 」 必須負責授權費中任何所產生或傳遞基礎結構成本） 的 $100.00 美元|

- 在此案例中，Microsoft 針對您的軟體授權收費 $100.00，並支付 $80.00 給發行者。
- 有資格的合作夥伴**降低 Marketplace 服務費用**降低的交易費用 SaaS 上提供從 2019 年直到 2020 年 6 月將會看到。 在此案例中，Microsoft 會針對您的軟體授權的 $100.00 美元，並支付美金，鍵入 90.00 出到 「 發行者 」。

> [!NOTE]
> **降低 Marketplace 服務費用**:為特定 SaaS 提供，您已在我們的商業 Marketplace 上發行，Microsoft 將會降低其 Marketplace 服務費用從 20%（如 Microsoft Publisher Agreement 所述） 到 10%。 為了讓您的供應項目來限定，至少其中一個供應項目必須指定由 Microsoft 為 IP 共同銷售準備或 IP 設定優先順序的共同銷售。  資格必須符合才能接收此縮減的 Marketplace 服務費用的每月每個日曆月結束前至少五 （5） 工作天。  降低 Marketplace 服務費用不適用於 Vm、 受管理應用程式或任何其他商業 Marketplace 透過提供的產品。  降低 Marketplace 服務費用才會提供完整的供應項目 2019 5 月 31 日和 2020 年 6 月 30 日之間的 Microsoft 所收集的授權費用。  此時間之後，Marketplace 服務費用將會回到其正常的數量而定。 

|**Microsoft 收取的費用**|**每月美金 $100 元**|
|:---|:---|
|Microsoft 向您支付授權成本的 80% <br>**針對合格的 SaaS 應用程式，Microsoft 支付授權成本的 90%*|每月 $80.00 <br>*$* 每個月 *，鍵入 90.00|


#### <a name="csp-program-opt-in"></a>CSP 計劃選用功能
[雲端解決方案提供者 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)程式可讓軟體供應項目，以觸及數百萬名符合資格的 Microsoft 客戶，需要最少行銷和銷售的投資。

- **通道：在 CSP 計劃中提供的供應項目**（核取方塊）

表示在 CSP 計劃中提供供應項目，可讓搭售方案的一部分的產品銷售給客戶的雲端解決方案提供者。 

### <a name="list-through-microsoft"></a>透過 Microsoft 的清單

升級您的 Microsoft 業務，藉由建立 marketplace 清單。 選取要列出您供應項目只以及無法透過 Microsoft 表示，Microsoft 不會直接參與軟體授權的交易。 沒有任何相關聯的交易費用和 「 發行者 」 端保留的任何軟體授權費用，對客戶收集的 100%。 不過，發行者負責支援的軟體授權的交易，包括但不是限於所有層面： 訂單履行、 計量、 計費、 發票、 付款與集合。 

- **您要如何與此清單的供應項目互動的潛在客戶？**

##### <a name="get-it-now-free"></a>立即取得 （免費）
藉由提供有效的 URL （http 或 https 開頭），他們可以存取您的應用程式的免費列出您的優惠給客戶。  例如：`https://contoso.com/saas-app`

##### <a name="free-trial"></a>免費試用
列出您的供應項目，免費試用的客戶提供有效的 URL （http 或 https 開頭），他們可以存取您的應用程式。  例如：`https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>與我連絡
連接您的客戶關係管理 (CRM) 系統，以收集客戶的連絡資訊。 將要求的客戶，才能共用其資訊的權限。 這些客戶詳細資料，以及供應項目名稱、 識別碼和 marketplace 來源他們找到您的供應項目，將會傳送到您已設定的 CRM 系統。 如需有關如何設定您的 CRM 的詳細資訊，請參閱 < [Connect 潛在客戶管理](#connect-lead-management)。 

## <a name="enable-a-test-drive"></a>啟用試用產品

試用產品是由提供他們 '購買前先試用' 的選項，而導致增加的轉換及產生高素質的潛在客戶展示您的供應項目，向潛在客戶的好方法。 [深入了解試用產品。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **啟用試用**（核取方塊） 

藉由啟用測試磁碟機，您必須設定示範環境，供客戶試用您的供應項目固定的一段時間。 

### <a name="type-of-test-drive"></a>測試磁碟機類型

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**:部署範本，其中包含構成方案的所有 Azure 資源。 符合此案例中的產品使用只有 Azure 資源。
- **[Dynamics 365 Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**:Microsoft 裝載，並為 Business Central 的企業資源規劃系統維護 （包括佈建和部署） 的測試磁碟服務 (財務、 作業、 供應鏈，CRM 等等。)。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**:Microsoft 主控和維護 （包括佈建和部署） 的磁碟機測試服務之 Customer Engagement 系統 （銷售、 服務、 專案服務、 現場服務等）。  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**:Microsoft 主控，並為 Finance and Operations 企業資源規劃 （財務、 作業、 製造、 供應鏈等） 的系統維護 （包括佈建和部署） 的磁碟機測試服務。 
- **[邏輯應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**:部署範本，包含所有複雜的方案架構。 任何自訂產品應該使用這種類型的試用產品。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**:自訂建置的儀表板內嵌的連結。 想要示範互動式 Power BI 視覺效果應該使用這種類型的試用產品的產品。 您只需要上傳內嵌的 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他測試磁碟機資源
- [測試磁碟機技術，最佳作法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試用產品行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [測試磁碟機概觀的一個頁面巡覽區](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連接 潛在客戶管理

直接由列出 marketplace 將供應項目和連結您的客戶關係管理 (CRM) 系統，以便您可以在客戶表達興趣，或部署之後，立即收到客戶的連絡資訊與客戶連接您產品。

- **選擇 潛在客戶目的地**（下拉式選單）：提供您要我們傳送潛在客戶的 CRM 系統的連線詳細資料。 

合作夥伴中心支援下列的 CRM 系統的潛在客戶管理。 選取的安裝指示的連結。

- Azure Blob – 提供連絡電子郵件、 容器名稱和儲存體帳戶連接字串。 
- [Azure 資料表](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)– 提供連絡人電子郵件和儲存體帳戶連接字串。 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – 提供連絡電子郵件、 URL 和驗證模式 （Office 365 或 Azure Active Directory）。
- [Https 端點](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)– 提供連絡人電子郵件和 HTTPS 端點 URL。 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – 提供連絡電子郵件、 表單識別碼、 Munchkin 帳戶識別碼，以及伺服器識別碼。
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -提供連絡人電子郵件和組織識別碼。 

#### <a name="additional-lead-management-resources"></a>其他的潛在客戶管理資源
- [潛在客戶管理常見問題集](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [潛在客戶的常見組態錯誤](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潛在客戶管理概觀的一個頁面巡覽區](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

請記得**儲存**再繼續進行下一節 ！

## <a name="properties"></a>properties
**屬性** 索引標籤會要求您定義的類別和產業用來分組您的供應項目上的市集供應項目和您的應用程式版本支援的法律合約。 

選取 **儲存**完成這些欄位之後。 

### <a name="category"></a>類別
選取最少的一 （1），最多三 （3） 用於將您的供應項目分組為適當的 marketplace 搜尋區域的類別。 請呼叫您的供應項目如何支援這些類別在供應項目描述中。 

### <a name="industry"></a>產業
選取產業最多兩個 （2），用於將您的供應項目分組為適當的 marketplace 搜尋區域。 如果您的供應項目不是特定的產業，請勿選取其中一個。 請呼叫您的供應項目如何支援所選的業界供應項目描述中。 

### <a name="app-version"></a>應用程式版本
這是選擇性欄位 AppSource marketplace 中用來識別您的供應項目的版本號碼。 

### <a name="standard-contract"></a>標準合約
- **使用標準的合約？** 

若要簡化客戶的採購程序，並降低軟體廠商的合法的複雜性，Microsoft 會提供標準的合約範本以協助在 marketplace 中的交易。 

製作自訂的條款及條件，可以選擇 Azure Marketplace 發行者提供其軟體，客戶只需要檢查，並接受一次的標準契約義務。 

標準的合約可以在這裡找到： https://go.microsoft.com/fwlink/?linkid=2041178。

##### <a name="terms-of-use"></a>使用規定
如果您的授權條款不同於標準的合約，您可以選擇輸入您自己的法律使用條款的以下。 您也可以在此欄位中，輸入最多 10,000 個字元的文字。 如果您使用規定需要更長的描述，請輸入單一的 URL 連結到這個欄位可以找到其他授權條款。 它會顯示給客戶作為作用中的連結。

客戶必須先接受這些條款，才能試用您的應用程式。 

請記得**儲存**再繼續進行下一節 ！

## <a name="offer-listing"></a>供應項目清單
列出索引標籤會顯示您的供應項目可使用的語言 （和市場） 供應項目，英文 （美國） 目前是唯一可用的位置。 此外，此頁面會顯示特定語言的清單和已加入該日期/時間的狀態。 您必須定義 marketplace 詳細資料 （提供項目名稱、 描述、 搜尋詞彙等） 為每個語言 / 市場。

### <a name="offer-listings"></a>供應項目清單
提供要顯示在 marketplace 中，包括您的供應項目描述和行銷資產的詳細資料。

- **名稱**（必要）：此處定義的名稱會顯示為列出您已選擇 marketplace(s) 上您供應項目的標題。 名稱會根據您先前預先**新的供應項目**項目。  這可能被 trademarked。  這必須不能包含空白字元，emoji （除非它們是商標和著作權符號），而且必須是限制為 50 個字元。
- **摘要**（必要）：提供您的供應項目用於 marketplace listing(s) 搜尋結果中的簡短描述。 您可以在此欄位中最多 100 個字元的文字輸入。
- **描述**（必要）：提供您的供應項目會顯示在 marketplace listing(s) 概觀的描述。 請考慮包含價值主張，主要優點，任何類別或產業的關聯，應用程式內購買的機會，任何必要的公開揭示，以及連結，進一步瞭解。
您可以在此欄位中最多 3,000 個字元的文字輸入。 如需其他秘訣，請參閱文章[撰寫的絕佳應用程式描述](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)。
- **搜尋關鍵字**:輸入最多三個搜尋關鍵字，客戶可以使用 marketplace(s) 中尋找您的供應項目。
- **開始使用指示**（必要）：說明如何設定和開始使用您的應用程式的潛在客戶。  本快速入門中可以包含更詳細的線上文件連結。 您可以在此欄位中最多 3,000 個字元的文字輸入。 

#### <a name="links"></a>連結

- **隱私權原則**（必要）：連結至您的組織隱私權原則。 您有責任確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則
- **CSP 計劃的行銷資料**（選擇性）：您必須提供行銷資料，如果您選擇將供應項目連結[雲端解決方案提供者 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)程式。 CSP 會擴充您的供應項目，以符合資格的客戶有更多讓 CSP 合作夥伴套件組合，市場，並轉售您的供應項目。 這些轉銷商必須為行銷您的供應項目資料的存取。 如需詳細資訊，請參閱 < [Go To Market 服務](https://partner.microsoft.com/reach-customers/gtm)。
- **實用連結**（選擇性）：選擇性補充線上文件應用程式或列出提供的相關的服務的相關**標題**並**URL**。 按一下 新增額外的實用連結 **+ 加入 URL**。

#### <a name="contact-information"></a>連絡資訊

- **連絡人**:每個客戶連絡人，提供員工**名稱**，**電話號碼**，並**電子郵件**位址。  (這些*不會*公開顯示)。 A**支援 URL**也必須有**支援連絡人**群組。  (這項資訊*將*公開顯示)。

**支援連絡人**（必要）：用於一般支援問題。

**工程連絡人**（必要）：有關其他技術問題。

**通道管理員連絡**（必要）：用於有關 CSP 計劃的轉銷商問題。

#### <a name="files-and-images"></a>檔案和映像

- **文件**（必要）：新增相關的行銷文件，為您的供應項目，以 PDF 格式，提供最少的一 （1），最多三 （3） 每份文件的供應項目。
- **映像**（選擇性）：有多個位置，您的供應項目標誌影像可能會出現整個 marketplace(s)，需要下列大小： 小小的位置：48 x 48 像素 _（必要）、_ 媒體：90 x 90 像素為單位，大：216 x 216 像素 _（必要）、_ 寬：255 x 115 像素，Hero:815 x 290 像素為單位。 必須是所有映像。PNG 格式。
- **螢幕擷取畫面**（必要）：新增螢幕擷取畫面示範您的供應項目。 最多五 （5） 螢幕擷取畫面可能會增加，並應該調整大小為 1280 x 720 像素。 必須是所有映像。PNG 格式。
- **影片**（選擇性）：新增影片示範您的供應項目連結。 您可以使用 YouTube 和/或 Vimeo 影片的連結，這會隨著您的供應項目顯示給客戶。 您也必須在輸入視訊的縮圖映像大小為 1280 x 720 像素的 PNG 格式。 您可以顯示每個供應項目 4 部影片的最大值。

請記得**儲存**再繼續進行下一節 ！

#### <a name="additional-marketplace-listing-resources"></a>其他的 marketplace 清單資源

- [最佳作法 marketplace 優惠清單](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)



## <a name="preview"></a>預覽

**Preview**索引標籤可讓您定義有限**預覽對象**來釋放您的供應項目發佈 marketplace 接觸到的即時您供應項目之前。

> [!IMPORTANT]
> 您必須選取**上線**供應項目將會發行即時至 marketplace 公開對象之後檢查中預覽供應項目之前。

- **定義預覽對象：新增單一的 AAD/MSA 帳戶電子郵件，每行，以及選擇性的描述。**

手動新增最多十 （10） 電子郵件地址或二十 (20) 如果上傳 CSV 檔案中，針對現有 Microsoft 帳戶 (MSA) 或 Azure Active Directory (AAD) 帳戶，以協助驗證您的供應項目，發行前的生活。 藉由新增這些帳戶，您會定義對象，則可預覽存取您的供應項目發行至 marketplace(s) 之前。 如果您的供應項目已經上線，您仍然可以進行測試的任何變更或更新您的供應項目來定義預覽對象。

> [!NOTE]
> 私用的對象與不同的預覽對象。 預覽對象允許存取您的供應項目_先前_來即時在 marketplace 中發行。 您也可以選擇建立方案，並將它提供只給私用的對象。 在 **計劃清單**索引標籤上，您可以定義使用的私用對象**這是私用方案**核取方塊。 然後，您可以定義最多 20,000 個客戶使用 Azure 租用戶識別碼的私用對象。

## <a name="technical-configuration"></a>技術設定

**技術設定** 索引標籤上定義的技術詳細資料 （URL 路徑、 webhook、 租用戶識別碼和應用程式識別碼） 用來連接到您的供應項目。 此連線可讓我們為客戶的 Azure 訂用帳戶中的資源佈建您的供應項目，如果他們選擇要取得它。

- **登陸頁面 URL** （必要）：定義站台 URL 的客戶將會導向至登入之後取得從 marketplace 供應項目。 此 URL 也會是接收連線 API 以與 Microsoft 建立商務的端點。

- **連線 webhook** （必要）：針對 Microsoft 代表客戶傳送給您所需的所有非同步事件 (範例：Azure 訂用帳戶已經無效），我們會要求您提供連線 webhook。 如果您還沒有 webhook 系統位置中，最簡單的組態是 HTTP 端點的邏輯應用程式會接聽任何事件張貼到它，並適當地處理它們 (例如 https://prod-1westus.logic.azure.com:443/work)。 如需詳細資訊，請參閱[在邏輯應用程式中透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。

- **Azure AD 租用戶識別碼**（必要）：在 Azure 入口網站中，我們需要您[建立 Azure Active Directory (AD) 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)，讓我們能驗證我們的兩個服務之間的連線位於已驗證的通訊。 若要尋找[租用戶識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)，請移至您的 Azure Active Directory，然後選取**屬性**，然後尋找**目錄識別碼**數字 （例如，列出50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式識別碼**（必要）：您也需要您[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)和驗證金鑰。 若要取得這些值，請移至您的 Azure Active Directory，然後選取**應用程式註冊**，然後尋找**APPLICATION-ID**列出 (例如 50c464d3-4930-494c-963c-1e951d15360e) 的數目。 若要尋找的驗證金鑰，請前往**設定**，然後選取**金鑰**。 您必須提供描述和持續時間，然後會提供數字值。

 請注意，Azure 應用程式識別碼關聯至您的發行者識別碼，因此請確定相同的應用程式識別碼會在您所有的供應項目。

## <a name="plan-overview"></a>方案概觀

**計劃概觀** 索引標籤可讓您提供各種不同的計劃選項相同的供應項目內。 （有時稱為 Sku） 這些計劃可以與版本、 營收或服務層。 您必須設定至少一個要銷售您的供應項目在 marketplace 中的方案。

建立之後，您會看到您的計劃名稱、 Id、 計價模式，可用性 （公用或私人），目前發行狀態，以及任何可用的動作。

-   **動作**中可用**計劃概觀**您計劃的目前狀態而有所不同，而且可能包含：
  - 如果計劃狀態是 **草稿**– 刪除草稿
  - 如果計劃狀態是  **Live** – 停止銷售計劃 或 同步處理私用對象

**建立新的計劃**（一個為選取要透過 Microsoft 銷售人員的計劃的最小）

- **計劃識別碼：** 在這項優惠中的每個計劃的唯一的計劃識別碼。 此識別碼會顯示給客戶之產品的 URL 和 Azure Resource Manager 範本中 （如果適用）。 使用小寫的英數字元、 連字號或底線。 最多 50 個字元的允許此計劃識別碼。 請注意，選取在建立之後無法修改的識別碼。
- **方案名稱：** 當您決定其想要選取您的供應項目內時，客戶會看到此名稱。 在這項優惠中的每個計劃的唯一供應項目名稱。 方案名稱用來區分屬於相同的供應項目 （例如的軟體方案 供應項目名稱:Windows Server;計劃：Windows Server 2016、windows Server 2019)。

### <a name="plan-listing"></a>計劃清單

**計劃清單** 索引標籤會顯示可使用，您的計劃的語言 （和市場） 目前的英文 （美國） 是唯一可用的位置。 此外，此頁面會顯示特定語言的清單和已加入該日期/時間的狀態。 您必須定義 marketplace 詳細資料 （提供項目名稱、 描述、 搜尋詞彙等） 為每個語言 / 市場。

#### <a name="plan-listing-details"></a>計劃清單詳細資料

選取其中一種計畫語言將會顯示**計劃清單**的詳細資訊，包括**名稱**和**描述。**

- **名稱**：預先填入根據您的預覽版**新的計劃**項目，並會顯示為您供應項目的 「 軟體的計劃 」 在 marketplace 中顯示的標題。
- **說明：** 這項描述會說明此軟體方案的唯一性的機會和從其他軟體計劃，您的供應項目內的任何差異。 可能包含最多 500 個字元。

選取 **儲存**完成這些欄位之後。

#### <a name="plan-pricing-and-availability"></a>方案價格與可用性

**價格與可用性** 索引標籤可讓您設定此方案將可在市場所需的營收模型、 價格和計費的詞彙。 此外，您可以指定是否要將方案設為顯示給所有人，或是只適用於特定客戶 （私用對象）。

#### <a name="markets"></a>市場

- **編輯市場**（選擇性）

至少一個市場中必須存在的每項計畫。 選取您想要將此方案提供的任何市場位置的核取方塊。 搜尋方塊和按鈕來選取"稅務免稅 」 國家/地區，Microsoft remits 銷售和代表您使用稅的以幫助。 


如果您已經設定價格方案在 United States 美元 (USD)，並新增其他市場的位置，新的市場的價格會根據目前的匯率計算。 您一律應該檢閱每個發行前的市場的價格。 您可以檢閱定價，儲存變更後使用 [匯出價格 (xlsx)] 連結。

#### <a name="pricing"></a>價格

- **定價模式**：固定費率或基礎的座位

**固定費率：** 啟用存取您的供應項目，月繳或年繳的單一價格價費率價格。 這有時候稱為站台為基礎的價格。

**基礎的授權：** 啟用存取您的供應項目與使用者存取供應項目或佔用的數字為基礎的價格*基座*。 這個以基座為基礎的模型可讓您設定最小值和允許的基座數目上限會根據價格。 如此一來，不同的資料點，您可以設定設定多個計劃，根據使用者數目。  這些欄位為選擇性。 如果保留空白，會為無限制 （最小為 1） 和最大值視系統可支援解譯的基座數目。 可編輯這些欄位的更新您的計劃的一部分。

發行之後，就無法變更計費的定價模型選擇。 此外，相同的供應項目的所有方案必須都共用相同的定價模型。

- **計費詞彙**:每月或年度

選取的客戶必須支付所列價格的頻率。 必須提供至少一個每月或年度的價格，或這兩個選項可提供給客戶。

- **價格**:美元，每個月或每年的美元

價格以當地貨幣的集合 (USD = 美國貨幣) 轉換為當地貨幣的所有選取的市場，使用在安裝期間提供最新的 exchange 費率。 匯出價格試算表和檢閱每個市場的價格，以驗證這些發行前的價格。 如果您想要在個別的市場中設定自訂的價格，請修改並匯入價格試算表。 您是負責驗證此價格，並且擁有這些設定。
**您必須先儲存您提供的定價資料匯出的定價變更。*

因為有一些限制發行計劃之後可以變更的功能，請檢閱您發佈，請先仔細的價格：

- 一旦發行方案時，就無法變更計價模式。
- 計費的詞彙發行計劃之後, 即無法移除更新版本。
- 一旦發行計劃中的市場的價格，就無法變更更新版本。

### <a name="plan-audience"></a>計劃對象

您可以選擇設定要顯示給所有人，或是特定對象您選擇的每個計劃。 您可以指派此限制使用 Azure AD 租用戶識別碼的對象中的成員資格。

#### <a name="privacy"></a>隱私權聲明

- **這是私用方案**（選擇性的核取方塊）

選取此方塊即可讓您的計劃，私用和只對您所選擇的受限制的對象為可見。 一旦發行為私用方案，您可以更新對象，或選擇要此計劃提供給每個人。 一旦為每個人都可以看到發佈方案時，它就必須保留以供其他人。 （此計劃不能設定為私用方案一次）。

- **限制對象 （租用戶識別碼）**

將指派可存取此私用方案的對象。 存取指派使用的選項中的租用戶識別碼，包含指派給每個租用戶識別碼的描述。 可以新增最多 10 個租用戶識別碼，或如果.csv 試算表檔案中匯入 20,000 名客戶租用戶識別碼。

租用戶是代表做為 GUID （全域唯一識別碼，用來識別資源的 128 位元的整數數字） 識別碼，在組織的身分。 它是組織或應用程式開發人員在與 Microsoft 建立關係 (例如註冊 Azure、Microsoft Intune 或 Microsoft 365) 時收到的 Azure AD 專屬執行個體。 每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。 若要檢查租用戶，請登入 Azure 入口網站，而且您想要用來管理您的應用程式的帳戶。 如果您有租用戶，系統會自動將您登入，而且您會在您帳戶名稱正下方看到租用戶名稱。 將滑鼠停留在 Azure 入口網站右上方的帳戶名稱上，以查看您的姓名、電子郵件、目錄/租用戶識別碼 (一個 GUID) 與您的網域。 如果帳戶與多個租用戶相關聯，您可以選取帳戶名稱開啟功能表，以切換租用戶。 每個租用戶都有自己的租用戶識別碼。 您也可以查閱使用網域名稱的 URL，在貴組織的租用戶識別碼： [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)。

SaaS 供應項目會使用租用戶識別碼，來定義私用的對象，而其他供應項目類型可能會使用 Azure 訂用帳戶識別碼 （這也以 Guid 表示）。

> [!NOTE]
> 私用對象 （或受限制的對象） 與不同的預覽對象。 在  **[預覽](#preview)** 索引標籤上，您可以定義預覽對象。 預覽對象允許存取您的供應項目*先前*即時在 marketplace 中發佈的供應項目。 預覽對象時指定的私用對象僅適用於特定的計劃，可以檢視所有的計劃 (私用或未)，但是只有在有限的預覽期間，雖然計劃是進行測試和驗證。


## <a name="test-drive"></a>試用產品

**試用** 索引標籤可讓您設定示範 （或 「 測試磁碟機 」） 這可讓客戶試用您的供應項目之前認可來購買它。 進一步了解本文[什麼是試用？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。 如果您不再想要為您的供應項目提供試用產品，返回**[提供安裝程式](#offer-setup)** 頁面上，並取消核取**啟用試用**。

### <a name="technical-configuration"></a>技術設定
下列類型的試用產品可供使用，每個都有自己的技術設定需求。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不需要的技術設定）

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>技術設定 Azure Resource Manager 的測試

部署範本，其中包含構成方案的所有 Azure 資源。 符合此案例中的產品使用只有 Azure 資源。 深入了解設定[Azure Resource Manager 試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **區域**（必要）：目前有 26 個 Azure 支援的區域，您的試用產品可供。 一般而言，您會想要試用提供在中，您預期客戶，最大數目的區域，讓他們可以選取最接近的區域，為了達到最佳效能。 您必須先確定您的訂用帳戶允許部署所有的每個您選取的區域中所需的資源。

- **Instances**：選取類型 （經常性存取或冷） 和數字的可用執行個體，將會乘以您供應項目可使用的區域數目。

**熱**:這種類型的執行個體是已部署且正在等待每個所選區域的存取。 客戶可以立即存取*經常性*測試磁碟機，而不需要等待部署的執行個體。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少一個*經常性*執行個體，因為大部分的客戶不想等候完整部署，導致客戶使用量下車，如果沒有*經常性存取層*執行個體可用。

**冷**:這種類型的執行個體表示可能是每個區域部署的執行個體的總數。 冷的執行個體需要整個測試磁碟機 Resource Manager 範本來部署，當客戶要求試用產品，因此*冷*執行個體是載入時間比要慢很多*經常性*執行個體。 缺點是，您只需要支付的試用產品持續時間，就*不*一律執行為您 Azure 訂閱加上*經常性*執行個體。

- **測試磁碟機 Azure Resource Manager 範本**:上傳包含您的 Azure Resource Manager 範本的.zip。  深入了解快速入門文章中建立的 Azure Resource Manager 範本[建立及使用 Azure 入口網站部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- **試用產品期限**（必要）：輸入試用產品會保持作用中、 在數小時的時間的長度。 在此持續時間過後，試用產品將會自動終止。 這段期間可能只採用組依時數的整數 （例如："2"的時數，「 1.5"是無效的）。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>技術設定 Dynamics 365 測試

Microsoft 可以移除所裝載及維護服務佈建和部署使用這種類型的測試設定測試的複雜性。 這種類型的裝載的試用產品的組態是不論是否測試磁碟機的目標 Business Central、 客戶的參與或作業的對象相同。

- **最大並行測試磁碟機**（必要）：設定可以一次使用您的試用產品的客戶的最大數目。 每個並行使用者會使用 Dynamics 365 授權，試用作用中時，因此您必須確定您有足夠的授權可用來支援所設定的最大限制。 建議值為 3-5 個。

- **試用產品期限**（必要）：輸入試用產品所定義的時數會持續作用的時間的長度。 之後這幾個小時，工作階段會結束，並不會再使用其中一個您授權。 我們建議您 2-24 小時，視您的供應項目複雜度而定的值。 這段期間可能只採用組依時數的整數 （例如："2"的時數，「 1.5"是無效的）。  如果它們用盡時間，而且想要再次存取試用產品，使用者可以要求新的工作階段。

- **執行個體 URL** （必要）：客戶將會開始其測試磁碟機的位置 URL。 通常您已安裝的範例資料以執行您的應用程式的 Dynamics 365 執行個體的 URL (例如 https://testdrive.crm.dynamics.com)。

- **執行個體的 Web API URL** （必要）：擷取登入您的 Microsoft 365 帳戶，然後瀏覽至您 Dynamics 365 執行個體的 Web API URL**設定** \&gt;**自訂** \&gt;**開發人員資源** \&gt;**執行個體 (服務根目錄 URL) 的 Web API**，複製此處找到的 URL (例如 https://testdrive.crm.dynamics.com/api/data/v9.0)。

- **角色名稱**（必要）：提供您已定義自訂的 Dynamics 365 測試磁碟機中的安全性角色名稱。 這會在其測試磁碟機 （例如測試-訂餐角色） 期間指派給使用者。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用程式試用產品的技術設定

任何自訂產品應該使用這種類型的測試磁碟機部署範本以包含各種複雜的方案架構。 如需有關設定邏輯應用程式測試磁碟機，請瀏覽[Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)並[Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) GitHub 上。

- **區域**（需要的單一選取下拉式清單）：目前有 26 個 Azure 支援的區域，您的試用產品可供。 在 選取的區域中，將會部署邏輯應用程式的資源。 如果您的邏輯應用程式有任何特定區域中儲存的自訂資源，請確定此處所選取該區域。 若要執行這項操作，最好是完整部署邏輯應用程式在本機上您在入口網站中的 Azure 訂用帳戶，並確認正確運作再進行此選取項目。

- **最大並行測試磁碟機**（必要）：設定可以一次使用您的試用產品的客戶的最大數目。 這些磁碟機已部署，讓客戶能夠立即存取它們，而不需等待部署的測試。

- **試用產品期限**（必要）：輸入試用產品會保持作用中、 在數小時的時間的長度。 這段期間結束後，會自動終止測試磁碟機。

- **Azure 資源群組名稱**（必要）：請輸入[Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)儲存您的邏輯應用程式試用產品的名稱。

- **Azure 邏輯應用程式名稱**（必要）：輸入邏輯應用程式指派給使用者的試用產品的名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

- **取消佈建邏輯應用程式名稱**（必要）：輸入客戶完成後，將測試磁碟機取消佈建的邏輯應用程式名稱。 此邏輯應用程式必須儲存在上述的 Azure 資源群組中。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>不需要 Power BI 試用產品的技術設定

想要示範互動式 Power BI 視覺效果使用內嵌的連結來共用作為其測試磁碟機，不再需要的技術設定的自訂建置的儀表板的產品。 深入了解設定[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用程式。

### <a name="deployment-subscription-details"></a>部署的訂用帳戶詳細資料

若要部署測試磁碟機，代表您，請建立，並提供個別的唯一 Azure 訂用帳戶即可。 （沒有這項需要 Power BI 試用產品）。

- **Azure 訂用帳戶識別碼**（需要 Azure Resource Manager 與 Logic apps）：輸入要授與對資源使用狀況報告和計費的 Azure 帳戶服務的存取權的訂用帳戶識別碼。 我們建議您考慮[建立個別的 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-create-subscription)来用於試用產品，如果您還沒有。 您可以尋找您的 Azure 訂用帳戶 ID 登入[Azure 入口網站](https://portal.azure.com/)並瀏覽至**訂用帳戶**的左側功能表的索引標籤。 選取 [] 索引標籤會顯示您的訂用帳戶識別碼 (例如"a83645ac-1234年-5ab6-6789-1h234g764ghty")。

- **Azure AD 租用戶識別碼**（必要）：輸入您 Azure Active Directory (AD)[租用戶識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取 [Active Directory] 索引標籤的左側功能表中，選取**屬性**，然後尋找**目錄識別碼**列出數目 （例如：50c464d3-4930-494c-963c-1e951d15360e)。 您也可以查看使用您的網域名稱 URL，在貴組織的租用戶識別碼： [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)。

- **Azure AD 租用戶名稱**（Dynamic 365 所需）：輸入您 Azure Active Directory (AD) 的名稱。 若要尋找此名稱，請登入[Azure 入口網站](https://portal.azure.com/)，右上角您的租用戶名稱將會列在您的帳戶名稱。

- **Azure AD 應用程式識別碼**（必要）：輸入您 Azure Active Directory (AD)[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取 [Active Directory] 索引標籤的左側功能表中，選取**應用程式註冊**，然後尋找**應用程式識別碼**數目列出 (例如 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 應用程式金鑰**（必要）：輸入您 Azure Active Directory (AD)[應用程式金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取 [Active Directory] 索引標籤的左側功能表中，選取**應用程式註冊**，然後選取**設定** > **金鑰**。

請記得**儲存**再繼續進行下一節 ！

### <a name="test-drive-listings-optional"></a>測試磁碟機清單 （選擇性）

**測試磁碟機清單**選項底下找到**試用** 索引標籤會顯示可使用，您的試用產品的語言 （和市場） 目前的英文 （美國） 是唯一可用的位置. 此外，此頁面會顯示特定語言的清單和已加入該日期/時間的狀態。 您必須定義的測試磁碟機詳細資料 （描述、 使用者手冊、 影片等） 針對每個語言/市場。

- **描述**（必要）：描述您的試用產品，項目將會示範，目標使用者體驗、 功能，可探索，和任何相關的資訊，協助使用者決定是否要取得您的供應項目。 您可以在此欄位中最多 3,000 個字元的文字輸入。 

- **存取資訊**（所需的 Azure Resource Manager 和邏輯測試磁碟機）：說明必須知道能存取和使用此試用產品的客戶。 逐步進行案例，以使用您的供應項目和完全客戶應該知道什麼存取整個測試磁碟機的功能。 您可以在此欄位中最多 10,000 個字元的文字輸入。

- **使用者手冊**（必要）：您的測試磁碟機體驗的深入逐步解說。 使用者手冊應涵蓋所要的客戶獲得發生測試磁碟機，並做為參考，如有可能會有任何問題。 檔案必須以 PDF 格式，而且之後上傳命名 （255 個字元上限）。

- **影片：將影片新增**（選擇性）：可以上傳至 YouTube 或 Vimeo 影片，和使用連結和縮圖映像 （533x324 像素） 此處參考，好讓客戶可以檢視的逐步解說資訊，協助他們深入了解試用產品，包括如何使用的功能，成功的程式提供，並了解反白顯示其權益的案例。
  - **名稱**（必要）
  - **URL （YouTube 或 Vimeo 只）** （必要）
  - **縮圖 (533 x 324px)**:映像檔必須是 PNG 格式。

選取 **儲存**完成這些欄位之後。

## <a name="publish"></a>發佈

#### <a name="submit-offer-to-preview"></a>提交提供預覽

當您完成供應項目的所有必要的區段時，選取**發佈**入口網站右上角。 您將會重新導向**檢閱並發佈**頁面。 

如果這是您第一次發佈此供應項目，您可以：

- 請參閱每個供應項目區段的完成狀態。
    - *未啟動*– 表示區段未觸及與必須完成。
    - *不完整*– 表示的區段會有需要修正的錯誤，或需要提供的詳細資訊。 請返回區段，並加以更新。
    - *完成*– 表示一節已完成、 已提供所有必要的資料並沒有任何錯誤。 所有區段的供應項目都必須處於完成狀態，才可提交供應項目。
- 提供測試指示以確保您的應用程式會正確，測試，除了有助於了解您的應用程式的任何增補便箋認證團隊。
- 選取提交進行發佈的供應項目**送出**。 我們會傳送給您電子郵件，以讓您知道何時將預覽版的供應項目可供您檢閱並核准。 您必須返回 合作夥伴中心，並選取**go-live**您供應項目發佈到公用 （或如果私用供應項目，以私用的對象） 的供應項目。

## <a name="next-steps"></a>後續步驟

- [更新現有商業的 marketplace 供應項目](./update-existing-offer.md)
