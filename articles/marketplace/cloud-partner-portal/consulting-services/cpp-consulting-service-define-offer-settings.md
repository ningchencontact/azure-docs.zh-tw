---
title: Azure 與 Dynamcis 365 諮詢服務供應項目 - 定義供應項目設定 | Microsoft Docs
description: 本指南會說明如何在 Cloud Partner 入口網站中定義 Azure 或 Dynamcis 365 諮詢服務的供應項目設定。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344925"
---
# <a name="offer-settings-tab"></a>供應項目設定索引標籤

在 [新增供應項目] 畫面上，首先要建立供應項目身分識別。 供應項目身分識別由三個部分所組成：**供應項目識別碼**、**發行者識別碼**以及**名稱**。 以下各節會分別說明上述各部分。

![建立新諮詢服務供應項目 - 供應項目設定索引標籤](media/consultingoffer-settings-tab.png)

*供應項目識別碼*

這個識別碼是您第一次提交供應項目時，所建立的唯一名稱。 它只能包含小寫英數字元、連字號或底線。 **供應項目識別碼**會顯示在 URL 中，並影響搜尋引擎結果。 例如，*yourcompanyname_exampleservice*

如範例中所示，**供應項目識別碼**會附加至您的發行者識別碼，以建立唯一識別碼。 這會公開為永久連結，且可由搜尋引擎預定和編製索引。

*供應項目上架之後，即無法更新其識別碼*

*發行者識別碼*

此識別碼與您的帳戶相關。 當您登入您的組織帳戶時，您的**發行者**識別碼會顯示在下拉式功能表中。

*名稱*

此字串是將在 AppSource 或 Azure Marketplace 上顯示為供應項目名稱的字串。 [名稱] 欄位上限為 50 個字元。  檢閱者可能需要編輯您的標題，才能將持續時間和供應項目類型附加到供應項目名稱內。

>[!Note]
>重要事項：此處僅供輸入實際的服務名稱。 請勿包含持續時間和服務類型。

Edgewater Fullscope 提供的下列範例顯示供應項目名稱的組合方式。 供應項目名稱會顯示為：

![建立新的諮詢服務供應項目](media/cppsampleconsultingoffer.png)

供應項目名稱由四個部分組成：

-   **持續時間：** 在編輯器的 [電子店面詳細資料] 索引標籤中定義。 持續時間可以用小時、天或週來表示。
-   **服務類型：** 在編輯器的 [電子店面詳細資料] 索引標籤中定義。 服務類型為 `Assessment`、`Briefing`、`Implementation`、`Proof of concept` 及 `Workshop`。
-   **前置詞：** 由檢閱者插入
-   **名稱：** 在 [供應項目設定] 頁面中定義。

>[!Note]
>[名稱] 欄位上限為 50 個字元。 您提交的名稱可能需由檢閱者編輯，才能將持續時間和供應項目類型附加到名稱上。

下列清單提供數個妥善命名的供應項目名稱：

-   專業服務的基本資訊：1 小時簡報
-   雲端移轉平台：1 小時簡報
-   PowerApps 和 Microsoft Flow：1 天研討會
-   Azure Machine Learning 服務：3 週概念證明
-   Brick and Click 零售解決方案：1 小時簡報
-   使用您自己的資料：1 週研討會
-   雲端分析：3 天研討會
-   Power BI 訓練：3 天研討會
-   銷售管理解決方案：1 週實作
-   CRM 快速入門：1 天研討會
-   Dynamics 365 for Sales：2 天評量

完成 [供應項目設定] 索引標籤之後，您可以儲存您的提交。 供應項目名稱現在會顯示在編輯器上方，您可以在 [所有供應項目] 中找到它。

**後續步驟**

現在您可以輸入[電子店面詳細資料，並決定要在 Azure Marketplace 還是 AppSource](./cpp-consulting-service-storefront-details.md) 中發佈。