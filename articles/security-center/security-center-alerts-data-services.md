---
title: Azure 資訊安全中心中的資料服務威脅偵測 |Microsoft Docs
description: 本文提供 Azure 資訊安全中心中提供的資料服務警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665729"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 資訊安全中心中的資料服務威脅偵測

 Azure 資訊安全中心會分析資料儲存體服務的記錄，並在偵測到資料資源的威脅時觸發警示。 本文列出資訊安全中心針對下列服務產生的警示：

* [Azure SQL Database 和 Azure SQL Data Warehouse](#data-sql)
* [Azure 儲存體](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database 和 SQL 資料倉儲<a name="data-sql"></a>

Azure SQL Database 的先進威脅防護會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。

當有可疑的資料庫活動、潛在弱點或 SQL 插入式攻擊，以及異常的資料庫存取和查詢模式時，您會看到警示。

適用于 Azure SQL Database 和 SQL 的先進威脅防護屬於先進的 SQL 安全性功能的[先進資料安全性（ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)整合套件，涵蓋 Azure SQL 資料庫、Azure SQL Database 受控實例、Azure SQL 資料倉儲資料庫和 azure 虛擬機器上的 SQL server。

如需詳細資訊，請參閱：

* [如何為 Azure SQL Database 啟用先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何為 Azure 上的 SQL server 啟用先進的威脅防護虛擬機器](security-center-iaas-advanced-data.md)
* [SQL Database 和 SQL 資料倉儲的威脅防護警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure 儲存體<a name="azure-storage"></a>

儲存體的先進威脅防護（目前僅適用于 Blob 儲存體）會偵測到不尋常且可能有害的嘗試存取或惡意探索儲存體帳戶。 這一層保護可讓您解決威脅，而不需要您成為安全性專家，並協助您管理安全性監視系統。

>[!NOTE]
>適用于儲存體的先進威脅防護目前無法在 Azure 政府和主權雲端區域中使用。

如需詳細資訊，請參閱：

* [如何為 Azure 儲存體啟用先進的威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 儲存體的威脅防護警示清單](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB 警示是由不尋常且可能有害的嘗試存取或惡意探索 Azure Cosmos DB 帳戶所產生。

如需詳細資訊，請參閱：

* [Azure Cosmos DB 的先進威脅防護（預覽）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威脅防護警示清單（預覽）](alerts-reference.md#alerts-azurecosmos)
