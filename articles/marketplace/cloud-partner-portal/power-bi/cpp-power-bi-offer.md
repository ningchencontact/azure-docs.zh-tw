---
title: Power BI 應用程式供應項目-Azure Marketplace |Microsoft Docs
description: 如何將 Power BI 應用程式發佈到 Microsoft AppSource marketplace。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009711"
---
# <a name="power-bi-app-offer"></a>Power BI 應用程式供應項目

|              |                                |
|--------------|--------------------------------|
| 這篇文章說明如何將 Power BI 應用程式發佈至 Microsoft [AppSource marketplace](https://appsource.microsoft.com/)。  Power BI 應用程式封裝可自訂的 Power BI 內容，包括資料集、 報表和儀表板。 然後可以將應用程式部署到其他的 Power BI 租用戶透過 AppSource、 執行調整和自訂開發人員所允許的項目並將它連接到您自己的資料。 | ![Power BI 圖示](./media/powerbi-icon.png) |


本文分為三個主要部分：

-   [必要條件](./cpp-prerequisites.md)。 提供建立及發佈 Power BI 應用程式的技術和商務需求。
-   [建立 Power BI 應用程式供應項目](./cpp-create-offer.md)。 如何建立 Power BI 應用程式供應項目的項目利用[Cloud Partner 入口網站](https://cloudpartner.azure.com)。
-   [發佈 Power BI 應用程式供應項目](./cpp-publish-offer.md)。 如何提交至 AppSource 的新供應項目進行發佈，以及如何更新現有的供應項目。


## <a name="publishing-steps"></a>發佈步驟

以下是發佈 Power BI 應用程式供應項目的概要步驟：

![Power BI 應用程式提供發佈步驟](media/publishing-steps.png)

以下是 Power BI 應用程式供應項目發佈程序：

1. Power BI 中建立的範本應用程式。 這個動作會產生套件安裝 URL，表示主要的技術資產的供應項目。 也在這個階段中，升級進入生產階段前測試封裝。 如需詳細資訊，請參閱 <<c0> [ 什麼是 Power BI 範本應用程式？](https://docs.microsoft.com/power-bi/service-template-apps-overview)。 
2. 收集或建立供應項目的行銷資料，包括： 正式名稱、 描述、 標誌等。 
3. 收集或建立供應項目的法律，而且支援文件：*使用條款*，*隱私權原則*，*支援原則*，使用者說明等。
4. 建立供應項目： 使用 Cloud Partner 入口網站設定的供應項目，包括供應項目描述中，行銷資料、 法律資訊、 支援資訊，以及資產規格詳細資料。  完整指定供應項目之後，請將它提交進行發佈。
5. 監視發佈的程序，在 Cloud Partner 入口網站中。  在此步驟中，AppSource 上架小組將測試、 驗證以及認證您的應用程式。 
6. 應用程式中成功認證後，請檢閱其測試環境中，並釋放它。 
7. Power BI 應用程式會列在 AppSource （它 」 會即時"）。
8. 在 Power BI 中，將升階到生產環境進入生產階段前套件。 如需詳細資訊，請參閱 <<c0> [ 管理範本的應用程式發行](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)。


## <a name="next-steps"></a>後續步驟

建立您的 Power BI 應用程式供應項目，並將它發佈至 AppSource 之前，您必須符合[需求](./cpp-prerequisites.md)Power BI 應用程式發佈至 AppSource。
