---
title: "連線到 Azure Cosmos DB 作為 Azure Machine Learning Workbench 中的資料來源| Microsoft Docs"
description: "此文件提供的範例說明如何透過 Azure Machine Learning Workbench 連線到 Azure Cosmos DB"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 22e4aedef1c00add17a9c3c4d0ed3822de155a6d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>連線到 Azure Cosmos DB 作為資料來源
此文章包含的 Python 範例可讓您連線到 Azure Machine Learning Workbench 中的 Cosmos DB。

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>將 Azure Cosmos DB 資料載入資料準備

建立新的指令碼型資料流程，然後使用下列指令碼從 Azure Cosmos DB 載入資料。 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>其他資料來源連線
如需其他範例，請參閱[範例其他來源資料連線](data-prep-appendix8-sample-source-connections-python.md)
