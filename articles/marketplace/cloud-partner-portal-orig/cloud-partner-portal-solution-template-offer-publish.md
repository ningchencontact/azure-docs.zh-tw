---
title: 發行解決方案範本 | Microsoft Docs
description: 將解決方案範本發行到 Azure Marketplace。
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: 5320b8d5ca7456a6f1b0fdd1372c9f39ac1edfb0
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219404"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>將解決方案範本發行到 Azure Marketplace

此文章提供將解決方案範本供應項目發行到 Azure Marketplace 的步驟。

## <a name="prerequisites"></a>必要條件

下列技術與非技術先決條件適用於在 Azure Marketplace 上陳列解決方案範本。

### <a name="technical"></a>技術需求

- [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Azure 快速入門範本：

    - [Azure 快速入門範本文件](https://azure.microsoft.com/documentation/templates/)

    - [GitHub 上的 Azure 快速入門文件](https://github.com/azure/azure-quickstart-templates)

 - [建立 Azure 入口網站使用者介面檔案](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>非技術性需求 (商務需求)

-   您的公司 (或其子公司) 必須位於 Azure Marketplace 支援的銷售來源國家/地區。

-   您的產品授權，必須與 Azure Marketplace 支援的計費模式相容。

-   您必須以合乎商業行為的方式 (包括免費、付費或透過社群支援)，負責為客戶提供技術支援。

-   您必須負責為您的軟體和任何第三方廠商相依性進行授權。

-   為了使您的供應項目可列於 Azure Marketplace 和 Azure 管理入口網站中，提供的內容必須符合標準。

-   同意 Azure Marketplace 參與原則和發行者合約中的條款。

-   同意遵守使用條款、Microsoft 隱私權聲明以及 Microsoft Azure 認證方案合約。

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

2.  在左側瀏覽列上，選取 [+ 新增供應項目]，然後選取 [Azure 應用程式]。

    ![建立新的供應項目](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  在 [新增供應項目] 下，選取 [編輯器]。

    ![新增供應項目編輯器](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  在 [編輯器] 下，您必須在下列檢視中提供資訊：
    - 供應項目設定
    - SKU
    - Marketplace
    - 支援

每個檢視都包含供您填寫的一組欄位。必填欄位會標示紅色星號 (\*)

## <a name="to-configure-offer-settings"></a>設定供應項目設定

1. 在 [供應項目設定] 中設定下列 [供應項目身分識別] 欄位。

    **供應項目識別碼**

     發行者設定檔內供應項目的唯一識別碼。 此識別碼會顯示在產品的 URL、ARM 範本與計費報告中。 您只能使用小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 
    >[!Note]
    >供應項目上架後，此欄位便會鎖住。

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
針對您的供應項目。

## <a name="to-create-skus"></a>建立 SKU
------------------

1. 選取 [SKU]。 

    ![新 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU 識別碼是供應項目內 SKU 的唯一識別碼。 此識別碼會顯示在產品的 URL、ARM 範本與計費報告中。 SKU 識別碼：
    - 最多不可超過 50 個字元。
    - 只能包含小寫英數字元或連字號 (-)。
    - 識別碼不能以連字號結尾。

    >[!Note]
    >新增 SKU 之後，它會出現在 SKU 檢視的 SKU 清單中。 若要查看 SKU 詳細資料，請選取 SKU 名稱。 

2. 選取 [新 SKU] 以提供下列螢幕擷取畫面中顯示的資訊。 

    ![SKU 詳細資料](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>SKU 設定

提供下列 SKU 設定。

- **標題** - SKU 的標題。 此標題顯示在此項目的資源庫中。

- **摘要** - SKU 的簡短摘要描述。 (長度上限是 100 個字元。)

- **描述** - SKU 的詳細描述。

- **SKU 類型** - 具有下列值的下拉式清單：「受控應用程式 (預覽)」與「解決方案範本」。 針對此案例，請選取 [解決方案範本]。

- **雲端可用性** - SKU 的位置。 預設值是「公用 Azure」。

### <a name="package-details"></a>套件詳細資料

完成 SKU 設定之後，請提供下列套件詳細資料。

![套件詳細資料](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **目前的版本** - 您將上傳之套件的版本。 它的格式應該是 - ..

- **套件檔案** - 此套件包含下列檔案，且這些檔案儲存在 .zip 檔中。

    -   MainTemplate.json - 用來部署解決方案/應用程式並建立針對解決方案定義之資源的部署範本檔案。 如需詳細資訊，請參閱[如何製作部署範本檔案](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json - Azure 入口網站會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >此套件應該包含佈建此應用程式所需的任何其他巢狀範本或指令碼。 mainTemplate.json 與 createUIDefinition.json 必須位於根資料夾。

## <a name="to-configure-the-marketplace"></a>設定 Marketplace

使用 Marketplace 檢視來設定針對 [Azure Marketplace](https://azuremarketplace.microsoft.com) 上與 [Azure Portal](https://portal.azure.com/) 上的供應項目顯示的欄位。

### <a name="preview-subscription-ids"></a>預覽訂用帳戶識別碼

在供應項目發佈後，可用於存取供應項目的 Azure 訂用帳戶識別碼清單。 這些經允許的訂用帳戶，可以在供應項目上架前，讓您測試預覽供應項目。 合作夥伴入口網站可讓您列出最多 100 個訂用帳戶。

### <a name="suggested-categories"></a>建議的類別

從提供的清單中，選擇最能與您的供應項目互相關聯的五種類別 選取的類別係用來將您提供的供應項目對應至 [Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)中提供的產品類別。

下列範例顯示 Azure Marketplace 與 Azure 入口網站中的 Marketplace 資訊。

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure 入口網站**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


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

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>設定支援

使用支援檢視提供下列資訊：

- 您公司的支援連絡人，例如工程師。
- 客戶支援連絡人。

## <a name="to-publish-the-offer"></a>發佈供應項目

最後一個步驟是發佈供應項目。 選取 [發佈] 。
