---
title: 論文實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配論文實體使用的屬性。
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123073"
---
# <a name="paper-entity"></a>論文實體

> [!NOTE]
> 以下是論文實體特有的屬性。 （Ty = ' 0 '）

名稱 | 描述 | 在系統提示您進行確認時，輸入 | 作業
--- | --- | --- | ---
AA.AfId | 作者服務機關識別碼 | Int64 | Equals
AA.AfN | 作者服務機關名稱 | 字串 | Equals、StartsWith
AA.AuId | 作者識別碼 | Int64 | Equals
AA.AuN | 正規化的作者名稱 | 字串 | Equals、StartsWith
AA.DAuN | 原始作者名稱 | 字串 | 無
AA.DAfN | 原始的聯盟名稱 | 字串 | 無
AA.S | 作者清單中的數位位置 | Int32 | Equals
BT | BibTex 檔案類型（' a '：日誌發行項，' b '：書籍，' c '：書籍章節，' p '：會議論文） | 字串 | 無
BV | BibTex 場地名稱 | 字串 | 無
C.CId | 會議系列識別碼 | Int64 | Equals
C.CN | 會議系列名稱 | 字串 | Equals、StartsWith
CC | 引用計數 | Int32 | 無  
CitCon | 引文內容</br></br>參考的紙張識別碼清單，以及紙張中的對應內容（例如 [{123： ["棕色北極狐，已知是123中所參考的跳躍，因為「延遲狗」是歷程 misnomer，如紙張 123"]} 所示） | 自訂 | 無
D | 以 YYYY-MM-DD 格式發佈的日期 | Date | Equals、IsBetween
DN | 原始紙張標題 | 字串 | 無
DOI | 數位對象識別碼 | 字串 | Equals、StartsWith
E | 延伸的中繼資料</br></br>**重要**事項：此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA） | [段](#extended) | 無
ECC | 估計的引文計數 | Int32 | 無
F. DFN | 研究用的原始欄位名稱 | 字串 | 無
F.FId | 研究領域識別碼 | Int64 | Equals
F.FN | 標準化的研究領域名稱稱 | 字串 | Equals、StartsWith
FP | 發行中紙張的第一頁 | 字串 | Equals
I | 發行問題 | 字串 | Equals
IA | 反向抽象 | [InvertedAbstract](#invertedabstract) | 無
id | 紙張識別碼 | Int64 | Equals
J.JId | 期刊識別碼 | Int64 | Equals
J.JN | 期刊名稱 | 字串 | Equals、StartsWith
LP | 發行中紙張的最後一頁 | 字串 | Equals
PB | 發佈者 | 字串 | 無
Pt | 發行集類型（0：未知，1：日誌文章，2：專利，3：會議紙張，4：書籍章節，5：書籍，6：書籍參考專案，7：資料集，8：存放庫 | 字串 | Equals
RId | 參考的紙張識別碼清單 | Int64[] | Equals
S | 紙張的來源 Url 清單（依相關性排序） | [來源](#source)[] | 無
Ti | 正規化標題 | 字串 | Equals、StartsWith
V | 發行集磁片區 | 字串 | Equals
VFN | 日誌或會議場地的完整名稱 | 字串 | 無
VSN | 日誌或會議場地的簡短名稱 | 字串 | 無
W | 標題中的唯一單字 | String[] | Equals
Y | 發行年份 | Int32 | Equals、IsBetween

## <a name="extended"></a>段
> [!IMPORTANT]
> 此屬性已被取代，而且只支援繼承應用程式。 個別要求此屬性（亦即，attribute = Id，Ti，E）會導致在*序列化 JSON 字串*中傳回所有擴充中繼資料屬性</br></br>延伸中繼資料中包含的所有屬性現在都可做為最上層屬性，並可依此方式要求（也就是屬性 = Id，Ti，DOI，IA）

> [!IMPORTANT]
> 支援使用 "E" 來要求個別的擴充屬性。 範圍，亦即 "E. DN" 已被取代。 雖然這在技術上仍受到支援，但使用 "E" 來要求個別的擴充屬性。 範圍會導致屬性值在 JSON 回應中的兩個位置傳回，做為 "E" 物件的一部分，並做為最上層屬性。

名稱 | 描述 | 在系統提示您進行確認時，輸入 | 作業
--- | --- | --- | ---
BT | BibTex 檔案類型（' a '：日誌發行項，' b '：書籍，' c '：書籍章節，' p '：會議論文） | 字串 | 無
BV | BibTex 場地名稱 | 字串 | 無
CC | 引文內容</br></br>參考的紙張識別碼清單，以及紙張中的對應內容（例如 [{123： ["棕色北極狐，已知是123中所參考的跳躍，因為「延遲狗」是歷程 misnomer，如紙張 123"]} 所示） | 自訂 | 無
DN | 原始紙張標題 | 字串 | 無
DOI | 數位對象識別碼 | 字串 | 無
FP | 發行中紙張的第一頁 | 字串 | 無
I | 發行問題 | 字串 | 無
IA | 反向摘要 | [InvertedAbstract](#invertedabstract) | 無
LP | 發行中紙張的最後一頁 | 字串 | 無
PB | 發佈者 | 字串 | 無
S | 紙張的來源 Url 清單（依相關性排序） | [來源](#source)[] | 無
V | 發行集磁片區 | 字串 | 無
VFN | 日誌或會議場地的完整名稱 | 字串 | 無
VSN | 日誌或會議場地的簡短名稱 | 字串 | 無

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> InvertedAbstract 屬性不能直接要求為傳回屬性。 如果您需要個別的屬性，您必須要求最上層的 "IA" 屬性，亦即取得 IA。IndexLength 要求屬性 = IA

名稱 | 描述 | 在系統提示您進行確認時，輸入 | 作業
--- | --- | --- | ---
IndexLength | 索引中的項目數目 (摘要的字數統計) | Int32 | 無
InvertedIndex | 在原始抽象中的抽象單字及其對應位置的清單（例如 [{"the"： [0，15，30]}，{"棕色"： [1]}，{"fox"： [2]}]） | 自訂 | 無

## <a name="source"></a>來源

> [!IMPORTANT]
> 無法直接要求來源屬性做為傳回屬性。 如果您需要個別的屬性，您必須要求最上層 "S" 屬性，亦即取得 S. U 要求屬性 = S

名稱 | 描述 | 在系統提示您進行確認時，輸入 | 作業
--- | --- | --- | ---
Ty | 來源 URL 類型（1： HTML、2：文字、3： PDF、4： DOC、5： PPT、6： XLS、7： PS） | 字串 | 無
U | 來源 URL | 字串 | 無
