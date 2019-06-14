---
title: Azure SQL Database Machine Learning 服務 （預覽） 的主要差異
description: 本主題說明 Azure SQL Database 機器學習服務 (搭配 R) 和 SQL Server 機器學習服務的主要差異。
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725046"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL Database （預覽） 中的機器學習服務和 SQL Server 的主要差異

Azure SQL Database Machine Learning 服務 （使用 R) （預覽） 中的功能大致[SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)。 以下是一些主要差異。

> [!IMPORTANT]
> Azure SQL Database 機器學習服務目前是公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="language-support"></a>語言支援

SQL Server 透過[擴充性架構](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)支援 R 和 Python。 SQL Database 兩種語言皆不支援。 主要差異包括：

- R 是 SQL Database 中唯一支援的語言， 目前不支援 Python。
- R 版本為 3.4.4。
- 不需要透過 `sp_configure` 設定 `external scripts enabled`。 [註冊](sql-database-machine-learning-services-overview.md#signup)後，您 SQL 資料庫的機器學習就會啟用。

## <a name="package-management"></a>封裝管理

SQL Database 和 SQL Server 對於 R 套件的管理和安裝方式有所不同。 這些差異包括：

- R 套件是透過 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安裝。
- 套件無法執行輸出網路呼叫。 這項限制是類似[Machine Learning 服務的預設防火牆規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)在 SQL Server，但無法變更 SQL Database 中。
- 對於依賴外部執行階段 (如 Java) 或需要 OS API 安裝或使用存取權的套件並不支援。

## <a name="resource-governance"></a>資源管理

無法透過 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部資源集區限制 R 資源。

公開預覽期間，R 資源設定為最多 20%的 SQL Database 資源，且取決在您選擇哪個服務層上。 如需詳細資訊，請參閱 [Azure SQL Database 購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。

### <a name="insufficient-memory-error"></a>記憶體不足的錯誤

如果沒有適用於 R 的記憶體不足，就會出現錯誤訊息。 常見的錯誤訊息如下：

- 無法要求識別碼的 'R' 指令碼的執行階段與通訊: * * *。 請檢查 'R' 執行階段需求
- 執行 'sp_execute_external_script' hresult 0x80004004 時發生 'R' 指令碼錯誤。 ...發生外部指令碼錯誤: 「...無法配置 C 函式 'R_AllocStringBuffer' 中的記憶體 (也就是 0 Mb) 」
- 發生外部指令碼錯誤：錯誤： 無法配置大小的向量。

使用方式取決於多少記憶體可在 R 指令碼和正在執行的平行查詢的數目。 如果您收到上述錯誤，您可以調整您的資料庫以較高的服務層，以解決此問題。

## <a name="next-steps"></a>後續步驟

- 概觀，請參閱 < [Azure SQL 資料庫機器學習服務使用 R （預覽）](sql-database-machine-learning-services-overview.md)。
- 若要了解如何使用 R 來查詢 Azure SQL Database Machine Learning 服務 （預覽），請參閱[快速入門指南](sql-database-connect-query-r.md)。
- 若要開始使用一些簡單的 R 指令碼，請參閱[建立及執行簡單的 R 指令碼，在 Azure SQL Database Machine Learning 服務 （預覽） 中](sql-database-quickstart-r-create-script.md)。
