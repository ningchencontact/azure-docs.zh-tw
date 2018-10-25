---
title: 撰寫虛擬機器供應項目 | Microsoft Docs
description: 將虛擬機器發佈到 Azure Marketplace。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b046022990e95e65ed02880bd3fefbd78bcad28
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387346"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>發佈虛擬機器到 Azure Marketplace 

本文提供將虛擬機器供應項目發佈到 Azure Marketplace 所需的步驟。

## <a name="prerequisites"></a>必要條件

以下是在 Azure Marketplace 上發佈虛擬機器所適用的技術與非技術必要條件

### <a name="technical"></a>技術需求

-   [建立 Azure Marketplace 虛擬機器映像的技術性必要條件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [建立和上傳 Linux VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [從映像建立及測試 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [建立和上傳 Windows VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [從映像建立及測試 Windows VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [如何針對 VHD 建立常見問題進行疑難排解](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Azure Marketplace 映像的安全性建議](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>非技術性需求 (商務需求)

 -   您的公司 (或其子公司) 必須位於 Azure Marketplace 支援的銷售來源國家/地區

-   您的產品授權必須與 Azure Marketplace 支援的計費模式相容

-   您必須以合乎商業行為的方式，負責為客戶提供技術支援。 此支援可以是免費、付費或透過社群的支援。

-   您必須負責為您的軟體和任何第三方廠商相依性進行授權。

-   為了使您的供應項目可列於 Azure Marketplace 和 Azure 管理入口網站中，您提供的內容必須符合標準。

-   您同意 Azure Marketplace 參與原則和發行者合約中的條款。

-   您同意遵守[使用條款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隱私權聲明](http://www.microsoft.com/privacystatement/default.aspx)以及 [Microsoft Azure 認證方案合約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。

## <a name="before-you-begin"></a>開始之前

符合所有先決條件之後，您就可以開始製作您的解決方案範本供應項目。 開始之前，請檢閱下列供應項目與 SKU 資訊。

**供應項目**

Azure 應用程式供應項目對應至發行者提供的產品分類供應項目。 若您有想要在 Azure Marketplace 中提供的新解決方案/應用程式類型，新供應項目是最佳方式。 供應項目 SKU 的集合。 每個供應項目會以個別實體出現在 Azure Marketplace。

**SKU**

SKU 是供應項目的最小購買單位。 在同一個產品類別 (供應項目) 內，SKU 可讓您可以區分不同的支援功能。 例如，供應項目是受控或非受控，而且支援不同的計費模型。

在下列案例中新增多個 SKU：
- 您想要支援不同的計費模型，例如自備授權 (BYOL) 或隨用隨付 (PAYG)。
- 每個 SKU 都支援不同功能組，且每個功能組的計費方式都不同。

SKU 會出現在 Azure Marketplace 中父供應項目下，而且會顯示為 Azure 入口網站中的可購買實體。

## <a name="to-create-a-new-offer"></a>建立新的供應項目

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com/)。

2.  在左側瀏覽列上選取 [+ 新增供應項目]，然後選取 [虛擬機器]。

    ![新增虛擬機器供應項目](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  在 [新增供應項目] 下，選取 [編輯器]。

![新增供應項目編輯器](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  在 [編輯器] 下，您必須在下列檢視中提供資訊：
    - 供應項目設定
    - SKU
    - Marketplace
    - [支援每個] 檢視包含供您填寫的一組欄位。必填欄位會標示紅色星號 (\*)

## <a name="to-configure-offer-settings"></a>設定供應項目設定

1. 在 [供應項目設定] 中設定下列 [供應項目身分識別] 欄位。

    **供應項目識別碼**

     發行者設定檔內供應項目的唯一識別碼。 此識別碼會顯示在產品 URL、Resource Manager 範本和計費報告中。 您只能使用小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 例如，若發行者 **contoso** 發佈了供應項目識別碼為 **sample-vm** 的供應項目，它將會在 Azure Marketplace 中顯示為 **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >供應項目上架後，此欄位便會鎖定。

    **發行者識別碼**

    發行者設定檔的下拉式清單。 選擇您要在其下發行供應項目的設定檔。 
    >[!Note]
    >供應項目上架後，此欄位便會鎖定。

    **名稱**

    您供應項目的顯示名稱。 此名稱會顯示在 Azure Marketplace 與 Azure 入口網站中。 它最多不能超過 50 個字元。 供應項目名稱應遵循下列指導方針︰
    -  包含產品的可辨識品牌名稱。 
    - 請勿在此包含您的公司名稱，除非這是您供應項目的行銷方式。
    - 若您要在您自己的網站行銷此供應項目，請確定它與在您網站上的名稱相同。

2. 選取 [儲存] 以完成供應項目設定。

## <a name="to-create-a-sku"></a>建立 SKU

1. 選取 [SKU]。 
2. 選取 [新增 SKU] 以輸入 SKU 識別碼。 SKU 識別碼是在供應項目內 SKU 的唯一識別碼。 此識別碼會顯示在產品 URL、Resource Manager 範本和計費報告中。 SKU 識別碼：
    - 最多不可超過 50 個字元。
    - 只能包含小寫英數字元或連字號 (-)。
    - 識別碼不能以連字號結尾。

    ![新增 SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>設定 SKU 詳細資料

SKU 新增後，會出現在 SKU 檢視的 SKU 清單中。 若要查看 SKU 詳細資料，請選取 SKU 名稱。 您可以使用詳細資料檢視新增下列欄位中的資訊。

### <a name="hide-this-sku"></a>隱藏此 SKU

您可以使用此設定來管理 SKU 的可見性。 如果關閉 [隱藏此 SKU]，客戶將可在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)中看到此 SKU。 如果您只想要透過解決方案範本，而非單獨購買方式來提供此 SKU 的話，則可以隱藏此 SKU。

### <a name="cloud-availability"></a>雲端可用性

此欄位可讓您定義 SKU 在不同 Azure 雲端的可用性。

**公用 Azure**

此 SKU 可部署至已整合 Marketplace 的所有公用 Azure 區域中的客戶。

**Azure Government 雲端**

此 SKU 可部署在 Azure Government 雲端中。 發佈到 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) 前，我們建議發行者測試及驗證解決方案能如預期運作。 若要執行暫存和測試，請在[要求試用帳戶](https://azure.microsoft.com/offers/ms-azr-usgov-0044p)。 

>[!Note]
>Microsoft Azure Government 是政府社群雲端，US 聯邦政府、本地客戶或部落客戶，及符合資格可以為這些實體提供服務的合作夥伴，擁有此雲端的控管權限。

### <a name="countryregion-availability"></a>國家/區域可用性

此欄位會識別您的 SKU 可以在哪些地區購買。 您必須仔細考慮要公開發行您的 SKU 的國家/地區。 某些國家歸類為「Microsoft 已繳交稅金國家」。

-   在「Microsoft 已繳交稅金國家」，Microsoft 會收集客戶的稅款並繳納 (免除) 給政府的稅款。

-   在其他國家/地區，則是由合作夥伴負責收集客戶的稅款並繳納給政府。 如果您選擇在這些國家銷售，您必須有計算及繳納您所選國家稅款的能力。

![選取國家/區域可用性](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>價格

目前支援兩種定價模式。

#### <a name="bring-your-own-license-byol"></a>自備授權 (BYOL)

您管理在 VM 上執行之軟體的授權。 Microsoft 僅收取基礎架構成本。

#### <a name="usage-based-monthly-billed-sku"></a>依據用法每月計費的 SKU

依據發行者針對 VM 大小設定的費率，以每小時為基礎向客戶收費。 如果是 SKU 的 **每小時計費** 模式，總價格會是發行者收取的軟體成本和 Microsoft 收取的基礎結構成本的總和。 當客戶考慮購買時，總成本將顯示為每小時和每月價格以供客戶參考。 計費在此情況下會以月為基礎。

在依據用法的計費模式中，您需要進行額外設定。

**免費試用版**

您可以指定是否為為客戶提供免費試用。
此模式的客戶在 VM 部署後的前 30/90 天 (視選取項目而定) 不需負擔軟體成本 (免費)。 在免費試用期結束後，他們就必須依據發行者在每小時模式中設定的費率，以每小時為基礎付費。

**每核心價格**

您可以為 SKU 設定核心價格。 為此，您只需要為單一核心輸入基本價格，我們會自動計算剩餘核心的價格。 在入口網站中以美金為單位輸入價格，我們將自動計算其他地區的價格。 您可以使用 [匯出價格資料]，確認其他地區的價格

![每核心價格](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**離散價格**

如果您想要為每個核心分開定價，可以為每個核心設定價格。

![離散價格](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**匯出-匯入價格**

您可以匯出入口網站設定的價格，並以 excel 介面進行變更。 您也可以藉此確認每個地區以當地幣值顯示的價格。
按一下 [匯出價格]，下載預先填入價格詳細資訊的 excel 檔案。 您可以在 excel 內進行編輯，並使用 [匯入價格] 來匯入您所做的變更。
匯入的價格，將會反映在入口網站中。

在價格 excel 中，不同地區的價格會以當地幣值顯示。 我們使用的匯率，每天都會重新整理。

![使用匯率範例的匯出-匯入價格](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   供應項目上架之後即無法變更價格。 不過，您仍可以新增或移除支援的區域。
>-   此費用會在 [Azure 的虛擬機器價格](http://aka.ms/vmpricingdetails)以外向使用者另行收取。
>-   所有區域的價格會以設定價格當時所提供的費率，以當地幣值設定。
>-   若要個別設定或檢視每個地區的價格，請匯出價格試算表，然後匯入自訂價格。

## <a name="vm-images"></a>VM 映像

下一個需要完成的區段為 VM 映像區段。 在進入這一節前，您必須備妥您準備好發佈的 VHD。 以下是一些能幫助您建立 VHD 的連結︰

-   [建立 Azure Marketplace 虛擬機器映像的技術性必要條件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [建立及上傳 Linux VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [從映像建立及測試 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [建立及上傳 Windows VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [從映像建立及測試 Windows VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [如何針對 VHD 建立常見問題進行疑難排解](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

VHD 就緒後，您便可以開始填寫這一區段。
以下是若干欄位的細節資訊。

### <a name="recommended-vm-sizes"></a>建議的 VM 大小

最多選取六個建議的虛擬機器大小。 當客戶決定購買與部署您的映像時，這些大小是在 Azure Marketplace 和 Azure 入口網站的 [定價層] 刀鋒視窗中對其顯示的建議大小。 **這些只是建議大小。客戶可以選取任何可容納您映像中指定之磁碟的 VM 大小。**  下列螢幕擷取畫面顯示客戶將在 Azure 入口網站中看到的建議 VM 大小。


![建議的 VM 大小](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>開放連接埠

指定您想要開放和提供使用的連接埠。 在此 VM 部署期間，會開放這些連接埠。

### <a name="adding-vm-images"></a>新增 VM 映像

下一個步驟是為 SKU 新增 VM 映像。 每個 SKU，您最多可以新增 8 個磁碟版本。 Azure Marketplace 僅顯示特定 SKU 的最高磁碟版本編號。 其他版本編號須透過 API 來檢視。

在 [磁碟版本] 下方，選取 [+ 新增版本]。 此時會顯示您必須填寫的下列欄位。

#### <a name="vm-image-version"></a>VM 映像版本

VM 映像版本須遵循[語意版本](http://semver.org/)格式。 版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。 不同 SKU 中的映像可以有不同的主要和次要版本。 SKU 中的版本應該是累加變更，增加修補程式版本 (X.Y.Z 中的 Z)。

#### <a name="os-vhd-url"></a>作業系統 VHD URL

輸入為作業系統 VHD 建立的[共用存取簽章 URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images)。

如果有與此 SKU 相關聯的資料磁碟，您可以選取 [+ 新增資料磁碟] 連結，以選擇新增這些磁碟。 此動作會顯示您需填寫的其他欄位。

#### <a name="lun-vhd-url"></a>LUN VHD URL

為資料磁碟輸入[共用存取簽章 URI](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images)

#### <a name="lun-number"></a>LUN 編號

指派一個編號給 LUN。 這個編號將保留給此 SKU 中的這個資料磁碟。

>[!Note]
>在您發佈具有 VM 版本和資料磁碟的 SKU 後，這些資訊便會鎖定，無法修改。 如需新增另外的 VM 版本至 SKU，SKU 需要支援的資料磁碟數量無法變更。

#### <a name="common-sas-url-issues--fixes"></a>常見的 SAS URL 問題和修正

| 問題                                                                 | 訊息                                                                           | 修正                                                           |  文件的連結                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| 複製映像失敗 - 在 SAS URL 中找不到 "?"                | 失敗︰複製映像。 無法使用提供的 SAS URI 下載 Blob。       | 使用建議的工具更新 SAS Url                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 映像複製失敗 - SAS url 中未設定 “st” 和 “se” 參數   | 失敗︰複製映像。 無法使用提供的 SAS Uri 下載 blob。        | 更新 SAS Url，必須包含開始和結束日期             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 複製映像失敗 - SAS url 中沒有 “sp=rl”                    | 失敗︰複製映像。 無法使用提供的 SAS Uri 下載 blob         | 更新 SAS Url，將權限設定為「讀取」和「列出」     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 複製映像失敗 - SAS url 中的 vhd 名稱含有空格     | 失敗︰複製映像。 無法使用提供的 SAS Uri 下載 blob。        | 更新 SAS Url，不能含有空格                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 複製映像失敗 – SAS Url 授權錯誤的               | 失敗︰複製映像。 因為發生授權錯誤，無法下載 blob     | 重新產生 SAS Url                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>設定 Marketplace

使用 Marketplace 檢視來設定針對 [Azure Marketplace](https://azuremarketplace.microsoft.com) 上與 [Azure Portal](https://portal.azure.com/) 上的供應項目顯示的欄位。

### <a name="preview-subscription-ids"></a>預覽訂用帳戶識別碼

在供應項目發佈後，可用於存取供應項目的 Azure 訂用帳戶識別碼清單。 這些經允許的訂用帳戶，可以在供應項目上架前，讓您測試預覽供應項目。 合作夥伴入口網站可讓您列出最多 100 個訂用帳戶。

### <a name="suggested-categories"></a>建議的類別

從提供的清單中，選擇最能與您的供應項目互相關聯的五種類別 選取的類別係用來將您提供的供應項目對應至 [Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)中提供的產品類別。

下列範例顯示 Azure Marketplace 與 Azure 入口網站中的 Marketplace 資訊。

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure 入口網站**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>標誌指導方針

依照這些指導方針將標誌上傳到 Cloud Partner 入口網站：

-   Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。

-   Azure 入口網站的佈景主題色彩是白色與黑色。 避免使用這些色彩做為您標誌的背景色彩。 使用會讓您的標誌在 Azure 入口網站顯得突出的色彩。 建議您使用簡單的主要色彩。

    >[!Note] 
    >如果您使用透明背景，請確定標誌/文字不是白色、黑色或藍色。

-   請不要在標誌上使用漸層背景。

-   避免在標誌上放置文字。 這包括您的公司或品牌名稱。 您標誌的外觀與風格應該是*一般*，而且應該避免漸層。

-   標誌不應自動縮放。

#### <a name="hero-logo"></a>主圖標誌

主圖標誌為選用項。 發行者可以選擇不上傳主圖標誌。 不過，標誌在上傳後即無法刪除。 夥伴必須遵循 Azure Marketplace 的主圖圖示指導方針。

#### <a name="guidelines-for-the-hero-logo-icon"></a>主圖標誌圖示的指導方針

-   發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型顯示。 避免在背景中使用任何淺色。 主圖圖示不允許使用黑色、白色與透明背景。

-   供應項目列出之後，會以程式設計方式將發行者顯示名稱、方案標題、供應項目完整摘要與 [建立] 按鈕內嵌在主圖標誌內。 設計主圖標示時，不要輸入任何文字。 在標示右側保留空白空間。 此空間大小應該為 415 x 100 個像素，而且與左邊的位移距離應該為 370 px。

![主圖標誌範例](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>潛在客戶管理

若要設定供應項目的潛在客戶管理設定，請依照[這些指示](./cloud-partner-portal-get-customer-leads.md)操作。

## <a name="to-configure-support"></a>設定支援

使用支援檢視提供下列資訊：

- 您公司的支援連絡人，例如工程師。
- 客戶支援連絡人。

## <a name="to-publish-the-offer"></a>發佈供應項目

最後一個步驟是發佈供應項目。 請依照[這些指示將您的供應項目上架](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md)。
