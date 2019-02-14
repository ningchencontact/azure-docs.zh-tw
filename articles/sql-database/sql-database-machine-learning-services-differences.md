---
title: Azure SQL Database (預覽) 中機器學習服務 (搭配 R) 的主要差異概觀
description: 本主題說明 Azure SQL Database 機器學習服務 (搭配 R) 和 SQL Server 機器學習服務的主要差異。
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
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237479"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Azure SQL Database 和 SQL Server 機器學習服務的主要差異

Azure SQL Database 中的機器學習服務 (搭配 R) 功能類似於 [SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)。 以下是兩者的一些主要差異。

## <a name="language-support"></a>語言支援

SQL Server 透過[擴充性架構](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)支援 R 和 Python。 SQL Database 兩種語言皆不支援。 主要差異包括：

- R 是 SQL Database 中唯一支援的語言， 目前不支援 Python。
- R 版本為 3.4.4。
- 不需要透過 `sp_configure` 設定 `external scripts enabled`。 [註冊](sql-database-machine-learning-services-overview.md#signup)後，您 SQL 資料庫的機器學習就會啟用。

## <a name="package-management"></a>封裝管理

SQL Database 和 SQL Server 對於 R 套件的管理和安裝方式有所不同。 這些差異包括：

- R 套件是透過 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安裝。
- 套件無法執行輸出網路呼叫。 此限制類似 SQL Server 中的 [機器學習服務預設防火牆規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)，但無法在 SQL Database 中變更。
- 對於依賴外部執行階段 (如 Java) 或需要 OS API 安裝或使用存取權的套件並不支援。

## <a name="resource-governance"></a>資源管理

無法透過 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部資源集區限制 R 資源。 R 的資源是特定百分比的 SQL Database 資源，取決於您所選擇的服務層。 如需詳細資訊，請參閱 [Azure SQL Database 購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。

## <a name="security-isolation"></a>安全性隔離：

在 Azure SQL Database 中，SQL 平台抽象層 (SQLPAL) 提供外部處理程序的隔離。 此隔離會提供多一層的安全性來執行 R 指令碼。

## <a name="next-steps"></a>後續步驟

- 如需一般資訊，請參閱 [SQL Server 機器學習服務](https://docs.microsoft.com/sql/advanced-analytics)文件
- 若要了解如何在 Azure SQL Database 中使用機器學習服務 (搭配 R)，請參閱[快速入門指南](sql-database-connect-query-r.md)。
- 觀看 [SQL Server R 語言教學課程](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)以深入了解