---
title: 建立 IoT Edge 模組供應項目 | Microsoft Docs
description: 如何在 Marketplace 中發行新的 IoT Edge 模組。
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805491"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>如何在 Cloud Partner 入口網站中發行新的 IoT Edge 模組

此文章說明發行新的 IoT Edge 模組供應項目的步驟。

## <a name="prerequisites"></a>先決條件

下列先決條件適用於在 Azure Marketplace 中發行 IoT Edge 模組。

-   [Cloud Partner 入口網站 (CPP)](https://cloudpartner.azure.com/#alloffers) 的存取權。 如需詳細資訊，請參閱[發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

-   將您的 IoT Edge 模組裝載在 Azure 容器登錄中。

-   備妥您的 IoT Edge 模組中繼資料 (包括非完整清單)：

    -   標題

    -   描述 (基本 HTML 格式)

    -   png 影像格式和下列尺寸的標誌：40 像素 x 40 像素、90 像素 x 90 像素、115 像素 x 115 像素、255 個像素 x 115 像素。

    -   使用規定及隱私權原則

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>在 CPP 中準備您的 IoT Edge 模組清單
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>建立 IoT Edge 模組類型的新的供應項目 

請依照下列步驟準備您的 IoT Edge 模組清單：

-   登入您的 [CPP 帳戶](https://cloudpartner.azure.com/)。

>[!Note]
>如需 Cloud Partner 入口網站的一般資訊，您可以查看[了解文件](https://cloudpartner.azure.com/#learn)

-   選取 [新增供應項目]，然後選取 [IoT Edge 模組]。

>[!NOTE]
>IoT Edge 模組是一個容器，專門用於在 IoT Edge 上執行。 IoT Edge 模組所處理的案例必須在合理的 IoT Edge 內容中。 它還包括預設組態設定，以便直接部署到 IoT Edge 裝置。 容器還可以包含 IoT Edge 模組 SDK，以啟用與 edgeHub 和 IoT 中樞的通訊。

### <a name="define-your-offer-settings"></a>定義您的供應項目設定

在 [供應項目設定] 索引標籤中，輸入供應項目的資訊。

![供應項目身分識別](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   **供應項目識別碼**會在 CPP 中唯一識別您的供應項目，可用於客戶面向的 URL。

-   只有您在 CPP 中參考此供應項目時才能看到**名稱**。

### <a name="create-one-or-more-skus"></a>建立一或多個 SKU

每個 SKU 都會對應至容器映像。 您必須至少有一個 SKU，而且可以加入多個 SKU。 SKU 有兩個部分：

-   SKU 中繼資料

-   容器中繼資料

**建立 SKU：**

選取 [SKU] 索引標籤，然後選取 [新 SKU]。

![新 SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

SKU 中繼資料包含下列欄位，這些是必要欄位：
- SKU 識別碼 - 唯一識別碼。
- 標題 - SKU 標題，最多 50 個字元。
- 摘要 - 簡短描述，最多 100 個字元。
- 描述 - 詳細的描述。
- 隱藏此 SKU - 預設值為 [否]。
   
![SKU 詳細資料](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge 模組中繼資料和容器登錄

IoT Edge 模組中繼資料具有儲存在 Azure 容器登錄 (ACR) 的映像參考資訊。 Azure Marketplace 會將該映像複製到公用 Marketplace 登錄，然後在認證之後提供給客戶使用。 使用 IoT Edge 模組映像的所有使用者要求都是從 Marketplace 容器登錄提供的。

![容器映像](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**容器映像**

映像存放庫詳細資料具有下列必要欄位：

-   **訂用帳戶識別碼** – ACR 登錄所在的 Azure 訂用帳戶識別碼。

-   **資源群組名稱** – ACR 登錄的資源群組名稱。

-   **登錄名稱** – ACR 登錄名稱。

-   **存放庫名稱** – 存放庫名稱。 一旦設定，稍後就無法變更此值。 該名稱應該是唯一的，如此一來您帳戶底下就不會有其他相同名稱的供應項目。

-   **使用者名稱** – 與 ACR 相關聯的使用者名稱 (系統管理員使用者名稱)。

-   **密碼** – 與 ACR 相關聯的密碼。

    >[!Note]
    >必須提供使用者名稱和密碼，以確保合作夥伴在發行程序中能存取先前所描述的 ACR。

當您要發行 IoT Edge 模組映像時，可以提供一或多個映像標記。 請務必將 'latest' 標記 (預設值) 新增至您的模組，以便在測試期間輕鬆地識別模組。

您還可以指定下列的 IoT Edge 模組細節：

-   **createOptions** - 要傳遞的預設 createOptions，以便立即啟動此 IoT Edge 模組。

-   **twin:** - 要傳遞的預設對應項，以便在使用 IoT 模組 SDK 時，可以立即啟動此 IoT Edge 模組。

-   **routes:** - 要傳遞的預設路由，以便在使用 IoT 模組 SDK 時，可以立即啟動此 IoT Edge 模組。

### <a name="describe-your-iot-edge-module-for-your-customers"></a>為您的客戶描述您的 IoT Edge 模組

在 [Marketplace] 索引標籤中，新增您的行銷專屬內容。 此資訊將在 Azure Marketplace 上公開顯示並列出。

![IoT Edge 模組概觀](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **標題** - 公開的 IoT Edge 模組標題。

-   **摘要** - 在大部分的頁面中公開的 IoT Edge 模組摘要，例如瀏覽頁面。

-   **完整摘要** - 當模組為精選時公開的 IoT Edge 模組摘要。 

-   **描述** - 產品詳細資料頁面上公開的 IoT Edge 模組描述。 (您可以使用基本 HTML 標記來設定描述的格式。)

-   **行銷識別碼** - 用於建立產品 URL 的唯一識別碼。 此 URL 採用下列格式：    *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*。

-   **預覽訂用帳戶識別碼** - 有權存取這些訂用帳戶的使用者，將能夠在認證步驟之後和上線之前看到 IoT Edge 模組。

-   **實用連結** - 您最多可以新增 10 個連結，這些連結將顯示在您的產品詳細資料頁面上。

-   **建議類別** - 最多可選擇五個類別。 它們將顯示在您的產品詳細資料頁面上。 目前，所有的 IoT Edge 模組都顯示在瀏覽頁面中的「物聯網 \> IoT Edge 模組」類別下。

-   **標誌** - 以 PNG 格式上傳您的 IoT Edge 模組標誌影像。 請確切使用下列尺寸：40 像素 x 40 像素、90 像素 x 90 像素、115 像素 x 115 像素、255 個像素 x 115 像素。

-   **螢幕擷取畫面** - 螢幕擷取畫面顯示在您的產品詳細資料頁面上。 它們適合用來以視覺化方式傳達 IoT Edge 模組的功能以及其運作方式。 例如，您可以顯示架構圖表，或針對執行個體使用案例圖。

-   **影片** - 影片會顯示在您的產品詳細資料頁面上。 它們適合用來以視覺化方式傳達 IoT Edge 模組的功能以及其運作方式。

-   **潛在客戶管理** - 您可以選擇一個系統來收集對您的產品感興趣的所有潛在客戶。

-   **隱私權** - 您必須具備指向您的隱私權原則的 URL。

-   **使用規定** - 您必須具備使用規定。 您可以使用 HTML 標記格式化此頁面，或指向您其中一個其他頁面。

### <a name="enter-your-support-contact-information"></a>輸入您的支援連絡人資訊

在 [支援] 索引標籤中，提供您的**工程連絡人**和**客戶支援**資訊。

![支援連絡人](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>認證您的 IoT Edge 模組

提供所需的所有資訊之後，選取 [發行] 來傳送 IoT Edge 模組以進行認證。 您將看到一個時間軸顯示認證程序中的步驟。

**模組驗證**

您的模組已通過我們的認證團隊驗證。 模組通過驗證後，您將收到私人連結來測試您的模組。 如果您需要在測試後進行變更，請編輯供應項目中繼資料，並將模組重新提交給認證團隊。 

## <a name="publish-your-iot-edge-module"></a>發行您的 IoT Edge 模組

完成測試並準備發行後，選取 [上線] 以發行您的 IoT Edge 模組。

>[!Important]
>如果您想在 Ignite 事件中宣布您的 IoT Edge 模組，您的模組必須在 2018 年 09 月 23 日之前公開。
