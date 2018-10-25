---
title: 將 Azure 受控應用程式發行到 Azure Marketplace
description: 將 Azure 受控應用程式發行到 Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bc044c8b59c939163336ecab01546fc26a7a2643
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806403"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>將 Azure 受控應用程式發行到 Azure Marketplace 
========================================================

此文章列出將受控應用程式供應項目發行到到 Azure Marketplace 的各個步驟。

<a name="pre-requisites-for-publishing-a-managed-application"></a>發行受控應用程式的先決條件 
---------------------------------------------------

列於 Azure Marketplace 的先決條件

1.  技術需求

    -   [編寫 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Azure 快速入門範本：

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   建立 UI 定義

        -   [建立使用者介面定義檔](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  非技術性需求 (商務需求)

    -   您的公司 (或其子公司) 必須位於 Azure Marketplace 支援的銷售來源國家/地區

    -   您的產品授權，必須與 Azure Marketplace 支援的計費模式相容

    -   您必須負責為客戶提供技術支援：免費、付費，或透過社群支援。

    -   您必須負責為您的軟體和任何協力廠商軟體相依性進行授權。

    -   為了使您的供應項目可列於 Azure Marketplace 和 Azure 管理入口網站中，您提供的內容必須符合標準

    -   您必須同意 Azure Marketplace 參與原則和 發行者合約中的條款

    -   您必須同意遵守使用條款、Microsoft 隱私權聲明以及 Microsoft Azure 認證方案合約。

<a name="how-to-create-a-new-azure-application-offer"></a>如何建立新的 Azure 應用程式供應項目 
-------------------------------------------

符合所有先決條件之後，您就準備好開始撰寫您的受控應用程式供應項目。 開始之前，請先快速概觀供應項目和 SKU

**供應項目**

Azure 應用程式供應項目對應至發行者提供的產品分類供應項目。 如果您有新的解決方案/應用程式要在 Azure Marketplace 中發行，則新增供應項目是最好的選擇。 供應項目 SKU 的集合。 每個供應項目會以個別實體出現在 Azure Marketplace。

**SKU**

SKU 是供應項目的最小購買單位。 在同一個產品類別 (供應項目) 內，SKU 讓您可以區分不同的支援功能、供應項目是否受控，以及計費模式。

如果您想要支援不同的計費模式：例如自備授權 (BYOL)、隨用隨付 (PAYG) 等，請新增多種 SKU。 

當每個 SKU 支援不同功能組，且計費方式皆不同時，則新增多種 SKU。

SKU 會出現在 Azure Marketplace 中父供應項目下，它會顯示為 azure.com 中的可購買實體。

1.  登入 [Cloud Partner 入口網站](http://cloudpartner.azure.com)。

2.  從左側導覽列，按一下 [新增供應項目]\"\" 並選取 [Azure 應用程式]\"\"。

    ![新增供應項目](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  接著會開啟新供應項目的 [編輯器] 檢視，您可以開始撰寫。

4.  在左側 [編輯器] 檢視中，可以看到需要填寫的「表單」。 每個「表單」都包含需要填寫的欄位。必填欄位會標示紅色星號 (\*)。

    > 針對撰寫受控應用程式，有 4 個主要表單

    -   供應項目設定

    -   SKU

    -   Marketplace

    -   支援

<a name="how-to-fill-out-the-offer-settings-form"></a>如何填寫供應項目設定表單 
---------------------------------------

供應項目設定表單是指定供應項目設定的基本表單。
下列說明各個不同欄位。

**供應項目識別碼**

發行者設定檔內供應項目的唯一識別碼。
此識別碼會顯示在產品的 URL，Resource Manager 範本和計費報告中。 只能使用小寫英數字元或連字號 (-)。 此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。

**發行者識別碼**

此下拉式表單，可讓您選擇您想要在什麼發行者設定檔之下發佈此供應項目。 供應項目上架後，此欄位便會鎖住。

**名稱**

您供應項目的顯示名稱。 此名稱會顯示在 Azure Marketplace 與 Azure 入口網站中。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 請勿包含您的公司名稱，除非這是您的行銷方式。 如果您在自己的網站行銷此供應項目，請確認名稱與您網站的名稱相同。

按一下 [儲存] 以儲存您的進度。 下一個步驟是為您的供應項目新增 SKU。

<a name="how-to-create-skus"></a>如何建立 SKU。 
------------------

按一下 [SKU] 表單。 這裡您可以看到 [新增 SKU] 的選項，按一下該選項後，您可以輸入「SKU 識別碼」。

![新增供應項目 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

[SKU 識別碼]\"\" 是在供應項目內 SKU 的唯一識別碼。 此識別碼會顯示在產品的 URL、ARM 範本與計費報告中。 此識別碼只能包含小寫英數字元或連字號 (-)。
此識別碼不能以連字號結尾，且不能超過 50 個字元。 供應項目上架後，此欄位便會鎖住。 您可以在一個供應項目內建立多個 SKU。 您需要針對您預計發佈的每個映像，提供 SKU。

新增 SKU 後，它將出現在「SKU」表單中的 SKU 清單。 按一下 SKU 名稱，以取得特定 SKU 的細節資訊。 以下是若干欄位的細節資訊。

選取 [新 SKU] 之後，您必須填寫下列表單：

![新增供應項目 - 新 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>如何填寫 SKU 詳細資料區段 

**標題**：提供此 SKU 的標題。 這是此項目將顯示在資源庫的內容。

**摘要**：提供此 SKU 的簡短摘要。 此文字會顯示在標題底下。

**描述**：提供有關 SKU 的詳細描述。

**SKU 類型**：允許的值包括 [受控應用程式] 和 [解決方案範本]。 此案例中，您要選取 [受控應用程式]。

### <a name="how-to-fill-package-details-section"></a>如何填寫套件詳細資料區段 

套件區段具有下列需要填寫的欄位

![新增供應項目 - 新 SKU 套件](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**目前的版本**：提供您將上傳的套件版本。
其格式應該如下。

**套件檔案**：套件包含壓縮成 .zip 檔案的下列檔案。

applianceMainTemplate.json - 用來部署解決方案/應用程式並建立當中定義之資源的部署範本檔案。 您可以在這裡找到如何撰寫部署範本檔案的詳細資訊：<https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json - azure.com 會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 您可以在這裡找到如何建立此檔案的詳細資訊：<https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json - 只包含 Microsoft.Solution/appliances 資源的範本檔案。 關於這個資源，要注意的主要屬性如下：

-   \"kind\"：在 Marketplace 受控的應用程式案例中，這個值應為 \"Marketplace\"。
-   \"ManagedResourceGroupId\"：客戶訂用帳戶中的資源群組，當中會部署 applianceMainTemplate.json 中所定義的所有資源。
-   \"PublisherPackageId\"：唯一識別套件的字串。 

此值必須以下列方式建構：它是 \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\] 的串連。
您可以依下列所示來取得每個值。

此套件應該包含要成功佈建此應用程式所需的任何其他巢狀範本或指令碼。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 必須存在於根資料夾中。

**Authorizations**：這個屬性會定義誰可以存取客戶訂用帳戶中的資源以及其存取層級。 這樣可讓發行者代表客戶管理應用程式。

-   PrincipalId：在客戶訂用帳戶中的資源上，使用者、使用者群組或將被授與特定權限 (如角色定義所述) 之應用程式的 Azure Active Directory 識別碼。

-   角色定義：Azure AD 所提供的所有內建 RBAC 角色的清單。 您可以選取最適合的角色，也就是可讓您代表客戶管理資源的角色。

可以新增多個授權。 不過，建議要建立 Active Directory 使用者群組，並在 \"PrincipalId" 中指定其識別碼。  如此可新增更多使用者到使用者群組，而不需要更新 SKU。

您可以在這裡找到 RBAC 的詳細資訊：<https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Marketplace 表單
----------------

Azure 應用程式供應項目中的 Marketplace 表單所需要填寫的欄位，將顯示於 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)。 以下是若干欄位的細節資訊。

#### <a name="preview-subscription-ids"></a>預覽訂用帳戶識別碼

在此輸出 Azure 訂用帳戶識別碼清單，這些訂用帳戶可以在供應項目發行後存取供應項目。 這些經允許的訂用帳戶，可以在供應項目上架前，讓您測試預覽供應項目。 合作夥伴入口網站可讓您列出最多 100 個訂用帳戶。

#### <a name="suggested-categories"></a>建議的類別

從提供的清單中，選擇最能與您的供應項目建立關聯的五種類別。 選取的類別係用來將您提供的供應項目對應至 [Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 入口網站](https://portal.azure.com/)中提供的產品類別。

以下是您在此表單上提供的資料所出現的地方。

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>azure.com 上的入口網站

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>標誌指導方針

Cloud Partner 網站中所上傳的所有標誌都應該遵循下列指導方針︰

-   Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。

-   azure.com 入口網站的佈景主題色彩是白色與黑色。 避免使用這些色彩做為您標誌的背景色彩。 請使用會讓您的標誌在 azure.com 入口網站顯得突出的某些色彩。
    建議您使用簡單的主要色彩。 **如果您使用透明背景，請確定標誌和文字不是白色、黑色或藍色。**

-   請不要在標誌上使用漸層背景。

-   避免在標誌上放置文字 (甚至是公司或品牌名稱)。
    您標誌的外觀與風格應該是「一般」，而且應該避免漸層。

-   避免自動縮放標誌。

##### <a name="hero-logo"></a>主圖標誌

主圖標誌為選用項。 發行者可以選擇不上傳主圖標誌。 但主圖圖示一旦上傳，即無法刪除。 在該情況下，合作夥伴必須遵循主圖圖示的 Azure Marketplace 指導方針。

###### <a name="guidelines-for-the-hero-logo-icon"></a>主圖標誌圖示的指導方針

-   發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型色彩顯示。 避免在主圖圖示的背景使用淺色系。 主圖圖示不允許使用黑色、白色與透明背景。

-   供應項目列出之後，會以程式設計方式將發行者顯示名稱、方案標題、供應項目完整摘要與 [建立] 按鈕內嵌在主圖標誌內。 您在設計主圖標誌時不需要輸入任何文字。 在右側保留空白空間以供發行者顯示名稱、方案標題、供應項目完整摘要等使用。它們會以程式設計方式納入。
    文字右側的空白空間應為 415 x 100，並從左邊開始位移 370px。

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>支援表單
------------

下一步要填寫的表單是支援表單。 此表單要求輸入您公司的支援連絡人。  一些範例是您的工程連絡資訊和客戶支援連絡資訊。

<a name="how-to-publish-an-offer"></a>如何發佈供應項目
-----------------------

現在您已經製作好供應項目的草稿，下一步便是將供應項目發行至 Azure Marketplace。 按一下 [發行] 以開始讓供應項目上線的程序。
