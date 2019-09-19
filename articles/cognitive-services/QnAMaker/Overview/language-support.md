---
title: 語言支援 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 知識庫的語言會影響 QnA Maker 從來源自動擷取問題和答案的能力，以及 QnA Maker 為回應使用者查詢所提供之結果的相關性。 QnA Maker 針對您的知識庫支援的文化特性、自然語言清單。 請勿在相同知識庫中混用語言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066671"
---
# <a name="language-support-for-qna-maker"></a>QnA Maker 的語言支援

知識庫的語言會影響 QnA Maker 從[來源](../Concepts/data-sources-supported.md)自動擷取問題和答案的能力，以及 QnA Maker 為回應使用者查詢所提供之結果的相關性。

## <a name="auto-extraction"></a>自動擷取
QnA Maker 支援在任何語言頁面中擷取問題/答案，但下列語言的擷取有效性會較高，因為 QnA Maker 使用關鍵字來識別問題。

|支援的語言| 地區設定|
|-----|----|
|英文|en-*|
|法文|fr-*|
|義大利文|it-*|
|德文|de-*|
|西班牙文|es-*|

## <a name="primary-language-detection"></a>主要語言偵測

當第一個檔或 URL 加入至第一個知識庫時，會針對 QnA Maker 資源和在該資源上建立的所有知識庫，設定用來偵測的主要語言。 無法變更語言。 

如果使用者計畫支援多種語言，則每種語言都必須有新的 QnA Maker 資源。 瞭解如何[建立以語言為基礎的 QnA Maker 知識庫](../how-to/language-knowledge-base.md)。  

請使用下列步驟來驗證主要語言：

1. 登入 [Azure 入口網站](https://portal.azure.com)。  
1. 尋找並選取 QnA Maker 資源中建立的 Azure 搜尋服務資源。 Azure 搜尋服務資源名稱會以與 QnA Maker 資源相同的名稱開始，且會有類型**搜尋服務**。 請記住，只有一個 QnA Maker 資源可以連結到一個 Azure 搜尋服務資源。
1. 從搜尋資源的 [**總覽**] 頁面中，選取 [**索引**]。 
1. 選取 **testkb** 索引。
1. 選取 [**欄位**] 索引標籤。 
1. 查看 [**問題**] 和 [**解答**] 欄位的 [**分析器**] 資料行。 


## <a name="query-matching-and-relevance"></a>查詢比對和相關性
QnA Maker 需要 Azure 搜尋服務中的[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)才能提供結果。 特殊的重新排名功能適用於 En-* 語言，可改善相關性。

雖然 Azure 搜尋服務功能與支援的語言同等重要，但 QnA Maker 有位於上述 Azure 搜尋結果的其他順位排定程式。 在此 ranker 模型中，我們會使用一些特殊的語義和以字為基礎的英文功能，但其他語言尚無法使用。 我們不會提供這些功能，因為它們屬於 QnA Maker ranker 的內部工作。 

QnA Maker 會在建立期間[自動偵測知識庫的語言](#primary-language-detection)，並據此設定分析器。 您可以建立下列語言的知識庫。 

|支援的語言|
|-----|
|阿拉伯文|
|亞美尼亞文|
孟加拉文|
|巴斯克文|
|保加利亞文|
|卡達隆尼亞文|
|簡體中文|
|繁體中文|
|克羅埃西亞文|
|捷克文|
|丹麥文|
|荷蘭文|
|英文|
|愛沙尼亞文|
|芬蘭文|
|法文|
|加利西亞文|
|德文|
|希臘文|
|古吉拉特文|
|Hebrew|
|Hindi|
|匈牙利文|
|冰島文|
|印尼文|
|愛爾蘭文|
|義大利文|
|日文|
|坎那達文|
|韓文|
|拉脫維亞文|
|立陶宛文|
|馬來亞拉姆文|
|馬來文|
|挪威文|
|波蘭文|
|葡萄牙文|
|旁遮普文|
|羅馬尼亞文|
|俄文|
|塞爾維亞文_斯拉夫|
|塞爾維亞文_拉丁|
|斯洛伐克文|
|斯洛維尼亞文|
|西班牙文|
|瑞典文|
|坦米爾文|
|特拉古文|
|泰文|
|土耳其文|
|烏克蘭文|
|烏都文|
|越南文|
