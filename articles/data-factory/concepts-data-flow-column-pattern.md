---
title: Azure Data Factory 對應資料流程資料行模式
description: Azure Data Factory 對應資料流程資料行模式用來建立一般化範本模式，適用於在資料流程中轉換欄位而不考慮基礎結構描述中繼資料
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 53d3300ea11a86c34909ba6ce0fd6c8c0c38b4b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269559"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Azure Data Factory 對應資料流程概念

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

數個 Azure Data Factory 資料流程轉換支援「資料行模式」的概念，因此您可以根據模式建立範本資料行，而不是硬式編碼的資料行名稱。 您可以在運算式產生器中使用此功能來定義符合要轉換之資料行的模式，而不需要進行擷取特定的欄位名稱。 模式在連入的來源欄位經常變更時非常實用，特別是在變更文字檔或 NoSQL 資料庫中的資料行時。 這有時候稱為「結構描述漂移」。

![資料行模式](media/data-flow/columnpattern2.png "資料行模式")

資料行模式可用於處理結構描述漂移案例以及一般案例。 這對於您在無法完全得知每個資料行名稱的情況時非常實用。 您可以針對資料行名稱和資料行資料類型進行模式比對，並建立轉換運算式，其將針對符合您 `name` & `type` 模式之資料流中的任何欄位執行該作業。

將運算式新增至接受模式的轉換時，請選擇 [新增資料行模式]。 資料行模式允許符合模式的結構描述漂移資料行。

建置範本資料行模式時，在運算式中使用 `$$` 以代表每個相符欄位的參考都來自輸入資料流。

如果您選擇使用某個運算式產生器 Regex 函式，可以接著連續使用 $1、$2、$3 等來參考與您 Regex 運算式相符的子模式。

資料行模式案例的範例是使用 SUM 搭配一系列的輸入欄位。 彙總 SUM 計算是在「彙總」轉換中。 您可以接著針對每個相符的欄位類型 (符合 "integer") 使用 SUM，然後使用 $$ 參考運算式中的每個相符項目。
