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
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161722"
---
# <a name="paper-entity"></a>論文實體

<sub>* 以下是紙張實體特有的屬性。（Ty = ' 0 '）</sub>

Name | 描述 | Type | Dynamics 365
--- | --- | --- | ---
AA.AfId | 作者服務機關識別碼 | Int64 | 等於
AA.AfN | 作者服務機關名稱 | String | Equals、StartsWith
AA.AuId | 作者識別碼 | Int64 | 等於
AA.AuN | 正規化的作者名稱 | String | Equals、StartsWith
AA.DAuN | 原始作者名稱 | String | None
AA.DAfN | 原始的聯盟名稱 | String | None
AA.S | 作者清單中的數位位置 | Int32 | 等於
CC | 引用計數 | Int32 | None  
C.CId | 會議系列識別碼 | Int64 | 等於
C.CN | 會議系列名稱 | String | Equals、StartsWith
D | 以 YYYY-MM-DD 格式發佈的日期 | 日期 | Equals、IsBetween
E | 擴充中繼資料 (請參閱下表) | String | N/A  
ECC | 估計的引文計數 | Int32 | None
F. DFN | 研究用的原始欄位名稱 | String | None
F.FId | 研究領域識別碼 | Int64 | 等於
F.FN | 標準化的研究領域名稱稱 | String | Equals、StartsWith
識別碼 | 紙張識別碼 | Int64 | 等於
J.JId | 期刊識別碼 | Int64 | 等於
J.JN | 期刊名稱 | String | Equals、StartsWith
Pt | 發行集類型（0：未知，1：日誌文章，2：專利，3：會議紙張，4：書籍章節，5：書籍，6：書籍參考專案，7：資料集，8：存放庫 | String | 等於
RId | 參考的紙張識別碼清單 | Int64[] | 等於
Ti | 正規化標題 | String | Equals、StartsWith
W | 標題中的唯一單字 | String[] | 等於
Y | 發行年份 | Int32 | Equals、IsBetween

## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

Name | 描述               
--- | ---
BT | BibTex 檔案類型（' a '：日誌發行項，' b '：書籍，' c '：書籍章節，' p '：會議論文）
BV | BibTex 場地名稱
CC | 引文內容–參考的紙張識別碼清單，以及紙張中對應的內容（例如 [{123： ["棕色北極狐，已知為123中所參考的跳躍）。」延遲狗是歷程 misnomer，如紙張 123"]} 所示）
DN | 原始紙張標題
DOI | 數位對象識別碼
FP | 發行中紙張的第一頁
I | 發行問題
IA | 反向摘要
IA.IndexLength | 索引中的項目數目 (摘要的字數統計)
IA.InvertedIndex | 在原始抽象中的抽象單字及其對應位置的清單（例如 [{"the"： [0，15，30]}，{"棕色"： [1]}，{"fox"： [2]}]）
LP | 發行中紙張的最後一頁
PB | 發行者
S | 來源 - 論文的 Web 來源清單，依靜態順位排序
S.Ty | 來源類型 (1：HTML、2：Text、3：PDF、4：DOC、5：PPT、6：XLS、7：PS)
S.U | 來源 URL
V | 發行集磁片區
VFN | 日誌或會議場地的完整名稱
VSN | 日誌或會議場地的簡短名稱
