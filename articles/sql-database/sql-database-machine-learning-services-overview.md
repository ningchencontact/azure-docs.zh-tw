---
title: Azure SQL Database (預覽) 中的機器學習服務 (搭配 R) 概觀
description: 本主題說明 Azure SQL Database 機器學習服務 (搭配 R) 及其運作方式。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 02/06/2019
ms.openlocfilehash: b50fd21e4d3325875134d2e2e9caeed9f8db75d0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875598"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Azure SQL Database (預覽) 中的機器學習服務 (搭配 R)

機器學習服務是 Azure SQL Database 的功能，用來執行資料庫內 R 指令碼。 此功能包含高效能預測性分析和機器學習服務的 Microsoft R 套件。 透過預存程序、包含 R 陳述式的 T-SQL 指令碼或包含 T-SQL 的 R 程式碼，關聯式資料可在 R 指令碼中使用。

> [!IMPORTANT]
> Azure SQL Database 中的機器學習服務 (搭配 R) 目前對於在**一般用途**和**商務關鍵**服務層中使用虛擬核心購買模型的單一資料庫和彈性集區提供公開預覽。 在此初始公開預覽版中，**超大規模**服務層和**受控執行個體**部署選項皆不受支援。 目前，R 是唯一支援的語言。 目前不支援 Python。 
>
> 預覽目前適用於以下區域：西歐、北歐、美國西部 2、美國東部、美國中南部、美國中北部、加拿大中部、東南亞、印度南部及澳大利亞東南部。 
>
> 請至下方[註冊預覽版](#signup)。

## <a name="what-you-can-do-with-r"></a>R 的用途

使用 R 語言的強大功能可提供在資料庫內部進行的進階分析和機器學習。 此功能可讓您在資料所在之處導入計算和處理功能，而無須透過網路提取資料。 此外，運用企業 R 套件的強大功能可提供大規模的進階分析。

機器學習服務包含 R 的基底散發套件，並與來自 Microsoft 的企業 R 套件覆疊。 Microsoft 的 R 函式和演算法同時適用於大規模和公用程式，以提供預測性分析、統計模型、資料視覺效果，以及頂尖的機器學習演算法。

### <a name="r-packages"></a>R 套件

最常見的開放原始碼 R 套件已預先安裝在機器學習服務中。 下列來自 Microsoft 的 R 套件也包含在內：

| R 封裝 | 說明|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open 是 Microsoft 提供的增強版 R。 這是一個完整的統計分析和資料科學開放原始碼平台， 以 R 為基礎並百分之百與 R 相容，而且包含提升效能與重現性的額外功能。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR 是擴充 R 的主要程式庫，此程式庫的函式最廣泛受到使用。 在這些程式庫中，可以找到資料轉換和操作、統計摘要、視覺效果，以及多種形式的模型與分析。 此外，這些程式庫中的函式會自動將工作負載發散到可用的核心進行平行處理，且能夠在計算引擎協調與管理的資料區塊上運作。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML 新增機器學習演算法，可用來建立文字分析、影像分析和情緒分析的自訂模型。 |

除了預先安裝的套件之外，您還可以[安裝額外的套件](sql-database-connect-query-r.md#add-package)。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>註冊預覽版

若要註冊公開預覽版，請遵循下列步驟：

1. 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

2. 請經由 [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) 將電子郵件傳送至 Microsoft，以註冊公開預覽版。 依預設不會在 SQL Database 中啟用公開預覽版的機器學習服務 (搭配 R)。

當您在計畫中註冊後，Microsoft 即會將您加入公開預覽版，並為您現有或新的資料庫啟用 R。

在公開預覽期間，請勿將搭配 R 的機器學習服務用於生產工作負載。

## <a name="next-steps"></a>後續步驟

- 請參閱[與 SQL Server 機器學習服務的主要差異](sql-database-machine-learning-services-differences.md)
- 若要了解如何在 Azure SQL Database 中使用機器學習服務 (搭配 R)，請參閱[快速入門指南](sql-database-connect-query-r.md)。
- 觀看 [SQL Server R 語言教學課程](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)以深入了解
