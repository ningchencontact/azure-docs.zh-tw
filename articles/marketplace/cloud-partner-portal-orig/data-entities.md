---
title: 資料實體
description: 資料實體的概觀。
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a382f9b3ce08bba266311c2cc1d5f868f1bc3143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934921"
---
# <a name="data-entities"></a>資料實體

此文章定義並提供資料實體的概觀。 其中包含的資訊包括資料實體的功能、支援的案例、使用資料實體的類別，以及建立資料實體的方法。

## <a name="overview"></a>總覽

資料實體是來自資料庫資料表實際實作的抽象概念。 例如，在正規化的資料表中，每個客戶的大部分資料可能都儲存在客戶資料表，然後其餘資料可能分散到較小的相關資料表集合。 在這種情況下，客戶的資料實體概念會顯示為一個反正規化檢視，其中每個資料列包含來自客戶資料表及其相關資料表的所有資料。 資料實體可將商務概念封裝成讓開發和整合更輕鬆的格式。 資料實體的抽象本質，可以簡化應用程式開發和自訂的過程。 稍後，抽象概念也會將應用程式程式碼從不同版本間必然混亂的實體資料表隔離開來。

總結：資料實體提供概念性的抽象概念和基礎資料表結構描述的封裝 (反正規化檢視)，來呈現重要的資料概念和功能。

## <a name="capabilities"></a>功能

資料實體具有下列功能：

- 它取代了 AXD、資料匯入/匯出架構 (DIXF) 實體分散又零碎的概念，並透過單一概念彙總查詢。
- 它提供單一堆疊以擷取商務邏輯，並可達成如匯入/匯出、整合和可程式性的案例。
- 它成為適用於應用程式生命週期管理 (ALM) 和示範資料案例匯出和匯入資料套件的主要機制。
- 它可以公開為 OData 服務，接著用於表格式同步整合案例和 Microsoft Office 整合。

如需詳細資訊，請參閱[資料實體](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities)。
