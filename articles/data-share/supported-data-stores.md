---
title: Azure 資料共用中支援的資料存放區
description: 瞭解支援使用 Azure 資料共用的資料存放區。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438680"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 資料共用中支援的資料存放區

Azure 資料共用提供開放且彈性的資料共用，包括能夠從和到不同的資料存放區共用。 資料提供者可以共用一種資料存放區的資料，而且其資料取用者可以選擇要接收資料的資料存放區。 

在本文中，您將瞭解 Azure 資料共用中支援的一組豐富 Azure 資料存放區。 您也可以找到資料提供者和資料取用者可利用的資料存放區組合的相關資訊。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 資料共用支援哪些資料存放區？ 

下表詳細說明 Azure 資料共用支援的資料來源。 

| 資料存放區 | 以快照集為基礎的共用 | 就地共用 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |公開預覽 | |
| Azure Synapse 分析（先前稱為 Azure SQL DW） |公開預覽 | |
| Azure 資料總管 | |[有限預覽](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>資料存放區支援矩陣

Azure 資料共用可在決定資料存放區以接受中的資料時，為數據取用者提供彈性。 例如，從 Azure SQL Database 共用的資料可以接收到 Azure Data Lake 存放區 Gen2、Azure SQL Database 或 Azure Synapse 分析。 客戶可以在設定接收的資料共用時，選擇要接收資料的格式。 

下表詳細說明資料取用者在接受和設定其資料共用時所擁有的不同組合和選擇。 如需如何設定資料集對應的詳細資訊，請參閱[如何設定資料集](how-to-configure-mapping.md)對應。

|  | Azure Blob 儲存體 | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL Database |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
