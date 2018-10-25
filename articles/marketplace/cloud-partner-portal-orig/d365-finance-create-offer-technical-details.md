---
title: 如何填寫技術資訊設定表單 | Microsoft Docs
description: 說明如何針對新的 Dynamics 365 Business Central 應用程式輸入 [技術資訊] 表單上的欄位值。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
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
ms.openlocfilehash: cb8e5074d0cd99f5c7cb2130e6f9181eb5382015
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805590"
---
<a name="how-to-fill-out-the-technical-info-form"></a>如何填寫技術資訊表單
===========================================

1.  在 [選擇應用程式類型] 區段中，上傳您的延伸模組套件檔案 (.app) 與您的延伸模組所相依的任何延伸模組套件檔案。

    ![應用程式套件資訊](./media/d365-financials/image015.png)

-   **延伸模組套件檔案** -- 必要 - 延伸模組套件檔案 (.app)。

-   **相依性套件檔案** -- 若應用程式相依於 AppSource 中發行的另一個應用程式，則為必要。 這是已在 AppSource 中發行之延伸模組的 .app 檔案，而且目前的應用程式相依於該檔案。 

-   **程式庫套件檔案** - 若應用程式相依於未在 AppSource 中發行的另一個應用程式，則為必要。 這是現有應用程式的 .app 檔案，此應用程式未在且將不會在 AppSource 中發行。

-   **應用程式測試自動化** -- 必要 - 您必須為自動化延伸模組測試建立的 VS 程式碼撰寫測試套件。

2.  在 [延伸模組的額外資訊] 區段中，上傳您延伸模組的額外資訊。 此資訊將會在驗證期間使用。

    ![應用程式延伸模組表單的額外資訊](./media/d365-financials/image016.png)


-   **產品文件的 URL** -- 必要 - 延伸模組文件的 URL。

-   **主要使用案例** -- 必要 - 列出延伸模組逐步設定方式與使用方式詳細資料的文件。 您可以在[使用者案例文件](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/) \(英文\) 一文中找到範例。

-   **目標發行版本** -- 必要 - 選取要在其上部署應用程式的發行版本。 選取 [最新] 以在最新的市場版本上部署。 選取 [下一個次要] 以隨著將發行的下一個次要版本部署。 選取 [下一個主要] 以隨著將發行的下一個主要版本部署。

-   **需要進階 SKU** -- 選擇性 -- 若要用成視需要進階 SKU，請選取 [進階] 按鈕。 「服務管理與製造」只在進階版中提供。 您可以在[變更要部署的功能](https://docs.microsoft.com/dynamics365/financials/ui-experiences) \(英文\) 一文中找到有關基本版與進階版之比較的詳細資訊。

-   **程式碼分析錯誤說明** -- 選擇性 -- 列出並說明不符合需求之任何程式碼清單的文件。

-   **受影響程式碼功能說明** -- 選擇性 -- 列出並說明受限於延伸模組之程式碼功能的文件。

-   **測試帳戶** -- 選擇性 -- 用於遠端服務與網站等，且必須要有才能完成端對端使用測試的使用者帳戶。

-   **UX 需求例外** -- 選擇性 -- 列出並說明延伸模組不符合之任何使用者體驗需求的文件。

下一個步驟是為您的供應項目新增店面詳細資料。
