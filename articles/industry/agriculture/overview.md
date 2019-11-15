---
title: Azure FarmBeats 概觀
description: 提供 Azure FarmBeats 的概觀
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797154"
---
# <a name="overview-of-azure-farmbeats"></a>Azure FarmBeats 概觀

Azure FarmBeats 是 Azure 服務和功能的集合，旨在協助您於快速建置農業相關的智慧型資料驅動解決方案。 Azure FarmBeats 是一個 Azure Marketplace 供應項目，可讓您從各種來源 (例如，感應器、無人機、攝影機、衛星) 取得、彙總及處理農業相關的資料，而不需投資大量的資料工程資源。

> [!NOTE]
> Azure FarmBeats 目前處於公開預覽狀態。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 Azure Blockchain 目前沒有服務等級合約。 請使用 [Azure FarmBeats 論壇](https://aka.ms/FarmBeatsMSDN ) \(英文\) 來取得支援。

使用 Azure FarmBeats，您將能夠從各種來源 (例如，感應器、衛星、無人機) 取得資料，這些資料全都位於農場 (感興趣的地理區域) 內。

您將能夠：

- 彙總來自各家供應商的農業資料集
- 藉由融合不同的資料集，快速建置人工智慧/機器語言 (AI/ML) 模型

Azure FarmBeats 為您提供強固且簡單的方式來執行下列動作：

- 使用 GeoJSON 檔案來建立農場的地圖。
- 根據衛星影像，使用植被指數和水量指數來評估農場的健康情況。
- 取得要使用多少部感應器及要將它們置於何處的建議。
- 藉由將來自各家感應器廠商之感應器所收集的地面資料視覺化，來追蹤農場狀況。
- 根據衛星和感應器資料的融合來取得土壤水分圖。
- 在彙總的資料集之上建置 AI/ML 模型，來取得可行的見解。
- 提供農場健康情況諮詢，來建置或增強您的數位農業解決方案。

此文章的下列各節將討論 Azure FarmBeat 元件。

## <a name="data-hub"></a>資料中樞

API 層，可跨供應商對各種不同的農業資料集進行彙總、正規化及脈絡化。 從此預覽開始，您可以利用兩家感應器供應商 ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) \(英文\)、[Teralytic](https://teralytic.com/) \(英文\))、一家衛星圖像供應商 ([Sentinel-2](https://sentinel.esa.int/web/sentinel/home) \(英文\))，以及兩家無人機圖像供應商 ([senseFly](https://www.sensefly.com/) \(英文\)、[SlantRange](https://slantrange.com/) \(英文\))。 資料中樞旨在作為 API 平台，而我們正與更多供應商合作來整合 Azure FarmBeats，讓您在建置解決方案時有更多選擇。

## <a name="accelerator"></a>加速器

建置於資料中樞之上的範例解決方案，可快速開始使用您的使用者介面和模型開發。 此 Web 應用程式會利用 API，以圖表形式示範內嵌感應器資料的視覺效果，並以地圖形式示範模型輸出的視覺效果。 例如，您可以使用加速器來快速建立農場，並輕鬆取得該農場的植被指數圖或感應器放置圖。

## <a name="resources"></a>資源

請造訪 FarmBeats 的[部落格](https://aka.ms/AzureFarmBeats) \(英文\) 和[論壇](https://aka.ms/FarmBeatsMSDN) \(英文\)。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure FarmBeats，請造訪 [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) \(英文\) 以進行部署。
