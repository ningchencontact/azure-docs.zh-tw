---
title: 適用於 PostgreSQL 的 Azure 資料庫中支援的版本
description: 描述適用於 PostgreSQL 的 Azure 資料庫中支援的版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702315"
---
# <a name="supported-postgresql-database-versions"></a>支援的 PostgreSQL 資料庫版本
Microsoft 計劃在適用於 PostgreSQL 的 Azure 資料庫服務中支援 n-2 版本的 PostgreSQL 引擎。 版本會是 Azure 上目前的主要版本 (n) 和兩個先前的主要版本 (-2)。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列版本：

## <a name="postgresql-version-107"></a>PostgreSQL 版本 10.7
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/10/static/release-10-7.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9612"></a>PostgreSQL 版本 9.6.12
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9516"></a>PostgreSQL 版本 9.5.16
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
適用於 PostgreSQL 的 Azure 資料庫會自動管理次要版本修補程式。 目前不支援主要版本升級。 例如，不支援從 PostgreSQL 9.5 升級至 PostgreSQL 9.6。 如果您希望升級至下個主要版本，請建立資料庫[傾印並還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本建立的伺服器。

## <a name="next-steps"></a>後續步驟
如需不同 PostgreSQL 擴充功能的支援資訊，請參閱 [PostgreSQL 擴充功能](concepts-extensions.md)。
