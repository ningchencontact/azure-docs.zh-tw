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
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630568"
---
# <a name="supported-postgresql-database-versions"></a>支援的 PostgreSQL 資料庫版本
Microsoft 計劃在適用於 PostgreSQL 的 Azure 資料庫服務中支援 n-2 版本的 PostgreSQL 引擎，也就是目前發行的主要版本 (n) 和兩個先前的主要版本 (-2)。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列版本：

## <a name="postgresql-version-104"></a>PostgreSQL 10.4 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/10/static/release-10-4.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-969"></a>PostgreSQL 9.6.9 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9513"></a>PostgreSQL 9.5.13 版
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
適用於 PostgreSQL 的 Azure 資料庫會自動管理對於次要版本更新的修補。 目前不支援主要版本升級。 例如，不支援從 PostgreSQL 9.5 升級至 PostgreSQL 9.6。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本所建立的伺服器。

## <a name="next-steps"></a>後續步驟
如需不同 PostgreSQL 擴充功能的支援資訊，請參閱 [PostgreSQL 擴充功能](concepts-extensions.md)。
