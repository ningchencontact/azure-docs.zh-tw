---
title: 論文實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在學術知識 API 中搭配論文實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704947"
---
# <a name="paper-entity"></a>論文實體

<sub> *下列屬性專屬於論文實體。(Ty = '0') </sub>


名稱    |描述                                        |Type       | 作業
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |實體 ID                                          |Int64      |等於
Ti      |論文標題                                        |String     |Equals,<br/>StartsWith
L       |以 "\@ \@"分隔的紙張語言代碼\@          |String     |等於
Y       |論文年度                                         |Int32      |Equals,<br/>IsBetween
D       |論文日期                                         |Date       |Equals,<br/>IsBetween
CC      |引用計數                                     |Int32      |無  
ECC     |預估引用計數                           |Int32      |無
AA.AuN  |作者姓名                                        |String     |Equals,<br/>StartsWith
AA.AuId |作者識別碼                                          |Int64      |等於
AA.AfN  |作者服務機關名稱                            |String     |Equals,<br/>StartsWith
AA.AfId |作者服務機關識別碼                              |Int64      |等於
AA.S    |論文的作者順序                         |Int32      |等於
F.FN    |研究領域名稱                                |String     |Equals,<br/>StartsWith
F.FId   |研究領域識別碼                                  |Int64      |等於
J.JN    |期刊名稱                                       |String     |Equals,<br/>StartsWith
J.JId   |期刊識別碼                                         |Int64      |等於
C.CN    |會議系列名稱                             |String     |Equals,<br/>StartsWith
C.CId   |會議系列識別碼                               |Int64      |等於
RId     |所參考論文的識別碼                              |Int64[]    |等於
W       |論文標題和摘要的文字                |String[]   |等於
E       |擴充中繼資料 (請參閱下表)                |String     |無  
        


## <a name="extended-metadata-attributes"></a>擴充中繼資料屬性 ##

名稱    | 描述               
--------|---------------------------    
DN      | 論文的顯示名稱 
S       | 來源 - 論文的 Web 來源清單，依靜態順位排序
S.Ty    | 來源類型 (1：HTML、2：Text、3：PDF、4：DOC、5：PPT、6：XLS、7：PS)
S.U     | 來源 URL
VFN     | 場地全名 - 期刊或會議的全名
VSN     | 場地簡短名稱 - 期刊或會議的簡短名稱
V       | 合訂本 - 期刊合訂本
BV      | 期刊名稱
BT      | 
PB      | 日誌縮寫
I       | 期號 - 期刊期號
FP      | FirstPage - 論文第一頁
LP      | LastPage - 論文最後一頁
DOI     | 數位對象識別碼
CC      | 引用內容 – 所參考論文識別碼和論文中對應內容的清單 (例如 [{123:[“brown foxes are known for jumping as referenced in paper 123”, “the lazy dogs are a historical misnomer as shown in paper 123”]})
IA      | 反向摘要
IA.IndexLength| 索引中的項目數目 (摘要的字數統計)
IA.InvertedIndex| 摘要文字和其在原始摘要中對應位置的清單 (例如 [{“the”:[0, 15, 30]}, {“brown”:[1]}, {“fox”:[2]}])
