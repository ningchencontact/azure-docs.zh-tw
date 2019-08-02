---
title: 適用於 PostgreSQL 的 Azure 資料庫中支援的版本-單一伺服器
description: 描述適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中支援的版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551354"
---
# <a name="supported-postgresql-database-versions"></a>支援的 PostgreSQL 資料庫版本
Microsoft 的目標是在適用於 PostgreSQL 的 Azure 資料庫單一伺服器中支援 n-2 版的于 postgresql 引擎。 版本會是 Azure 上目前的主要版本 (n) 和兩個先前的主要版本 (-2)。

適用於 PostgreSQL 的 Azure 資料庫目前支援下列版本：

## <a name="postgresql-version-112"></a>于 postgresql 版本11。2
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/11/static/release-11-2.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-107"></a>于 postgresql 版本10。7
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/10/static/release-10-7.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9612"></a>于 postgresql 版本9.6.12
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="postgresql-version-9516"></a>于 postgresql 版本9.5.16
請參閱 [PostgreSQL 文件 (英文)](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)，以深入了解此次要版本中的改進功能與修正。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級
適用於 PostgreSQL 的 Azure 資料庫會自動管理次要版本修補程式。 目前不支援主要版本升級。 例如，不支援從 PostgreSQL 9.5 升級至 PostgreSQL 9.6。 如果您希望升級至下個主要版本，請建立資料庫[傾印並還原](./howto-migrate-using-dump-and-restore.md)至使用新引擎版本建立的伺服器。

> 請注意, 在於 postgresql 第10版之前,[于 postgresql 版本設定原則](https://www.postgresql.org/support/versioning/)會將_主要版本_升級視為一_或_第二個數字的增加 (例如, 9.5 到9.6 視為_主要_版本升級)。
> 從第10版開始, 只有第一個數位的變更會被視為主要版本升級 (例如, 10.0 到10.1 是_次要_版本升級, 而10到11是_主要_版本升級)。

## <a name="next-steps"></a>後續步驟
如需不同 PostgreSQL 擴充功能的支援資訊，請參閱 [PostgreSQL 擴充功能](concepts-extensions.md)。
