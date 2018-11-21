---
title: 適用於 PostgreSQL 的 Azure 資料庫中支援的版本
description: 描述適用於 PostgreSQL 的 Azure 資料庫中支援的版本。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/12/2018
ms.openlocfilehash: 04e47e15206e22e7965121c6c277e4032ea04ddf
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621049"
---
# <a name="supported-postgresql-database-versions"></a>支援的 PostgreSQL 資料庫版本
Microsoft 計劃在適用於 PostgreSQL 的 Azure 資料庫服務中支援 n-2 版本的 PostgreSQL 引擎。 版本會是 Azure 上目前的主要版本 (n) 和兩個先前的主要版本 (-2)。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列版本：

## <a name="postgresql-version-105"></a>PostgreSQL 10.5 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/10/static/release-10-5.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9610"></a>PostgreSQL 9.6.10 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9514"></a>PostgreSQL 9.5.14 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
適用於 PostgreSQL 的 Azure 資料庫會自動管理次要版本修補程式。 目前不支援主要版本升級。 例如，不支援從 PostgreSQL 9.5 升級至 PostgreSQL 9.6。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本所建立的伺服器。

## <a name="next-steps"></a>後續步驟
如需不同 PostgreSQL 擴充功能的支援資訊，請參閱 [PostgreSQL 擴充功能](concepts-extensions.md)。
