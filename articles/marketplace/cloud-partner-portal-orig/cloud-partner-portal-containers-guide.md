---
title: 容器 | Microsoft Docs
description: 發佈容器映像的步驟。
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806379"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>在 Cloud Partner 入口網站發佈容器映像
========================================================

此文章明如何在 Cloud Partner 入口網站中發佈新的容器映像。

使用下列步驟來發佈新的容器映像。

1. 選取 [新增供應項目] 然後選取 [容器]

    ![選取容器選項以新增項目](media/cpp-containers-guide/azure-container-offer.png)

2. 在 [供應項目設定] 索引標籤中的 [供應項目身分識別] 底下，輸入**供應項目識別碼**、**發行者識別碼**和**名稱**。

    ![供應項目身分識別](media/cpp-containers-guide/containers-offer-settings.png)

3. 在 [SKU] 索引標籤中，選取 [新增 SKU]。
    >[!NOTE]
    >您可以新增多個 SKU。

    ![新增 SKU 提示](media/cpp-containers-guide/containers-sku-settings.png)

4. 提供 SKU 和容器的資訊。 每個 SKU 都會對應至容器映像。 SKU 有兩個部分：

    -   SKU 中繼資料
    -   容器中繼資料

    SKU 中繼資料包含容器映像的顯示資訊。

    ![SKU 中繼資料](media/cpp-containers-guide/containers-sku-details.png)

    容器中繼資料擁有 Azure Container Registry (ACR) 中映像存放庫詳細資料的參考資訊。 Azure Marketplace 接著會將此映像複製到公用 Marketplace 登錄，然後在認證之後提供給客戶使用。 Azure 使用者取用容器映像的所有要求，都是從 Marketplace 容器登錄提供。

   ![容器中繼資料](media/cpp-containers-guide/containers-image-repository.png)

    上一個螢幕擷取畫面中的 [映像存放庫詳細資料] 包含下列欄位：

    -   **訂用帳戶識別碼**：ACR 登錄所在的 Azure 訂用帳戶識別碼。
    -   **資源群組名稱**：ACR 登錄的資源群組名稱。
    -   **登錄名稱**：ACR 登錄名稱。
    -   **存放庫名稱**：存放庫名稱。 一旦設定，稍後就無法變更此值。 您應該提供唯一名稱，如此一來您帳戶底下就不會有其他相同名稱的供應項目。
    -   **使用者名稱**：與 ACR 相關聯的使用者名稱 (系統管理員使用者名稱)。
    -   **密碼**：與 ACR 相關聯的密碼。

    >[!NOTE]
    >必須提供使用者名稱和密碼，以確保合作夥伴在發佈程序中能存取先前所述的 ACR。

    發佈容器映像的同時，您也可以提供一或多個映像標籤。 除了映像標籤，合作夥伴也可提供 SHA 摘要。 請務必將**測試標記**新增到您的映像，以便您在測試中識別映像。

5. 在 [Marketplace] 索引標籤中，新增您的行銷專屬內容。

    ![Marketplace 資訊](media/cpp-containers-guide/containers-marketplace-tab.png)

6. 在 [支援] 索引標籤中，輸入您的工程連絡人和客戶支援資訊。

   ![支援資訊](media/cpp-containers-guide/containers-support-tab.png)

7. 選取 [發佈] 以發佈供應項目。 在您選取 [發佈] 之後，您會看到顯示發佈您容器映像相關步驟的時間表。
