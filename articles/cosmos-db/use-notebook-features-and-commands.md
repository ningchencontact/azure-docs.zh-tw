---
title: 在 Azure Cosmos DB 中使用內建的筆記本命令和功能
description: 瞭解如何使用內建的命令和功能，使用 Azure Cosmos DB 的內建筆記本進行一般作業。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310343"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用內建的筆記本命令和功能

Azure Cosmos DB 中的內建 Jupyter 筆記本可讓您從 Azure 入口網站分析資料並將其視覺化。 本文說明如何使用內建的筆記本命令和功能來執行一般作業。

## <a name="install-a-new-package"></a>安裝新套件
啟用 Azure Cosmos 帳戶的筆記本支援之後，您可以開啟新的筆記本並安裝套件。

在新的程式碼資料格中，插入並執行下列程式碼``PackageToBeInstalled`` ，以所需的 Python 套件取代。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
此套件將可從 Azure Cosmos 帳戶中的任何筆記本使用。 

## <a name="run-a-sql-query"></a>執行 SQL 查詢

您可以使用``%%sql``魔術命令，針對您帳戶中的任何容器執行[SQL 查詢](sql-query-getting-started.md)。 使用語法：

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- 將``{database_id}`` 和``{container_id}``取代為您 Cosmos 帳戶中的資料庫和容器名稱。 如果未提供``--container``和自變數，則會在[預設的資料庫和容器](#set-default-database-for-queries)上執行查詢。 ``--database``
- 您可以在 Azure Cosmos DB 中執行有效的任何 SQL 查詢。 查詢文字必須在新行上。

例如: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
在```%%sql?```儲存格中執行，以查看筆記本中 sql 魔術命令的說明文件。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>執行 SQL 查詢並輸出至 Pandas 資料框架

您可以將``%%sql``查詢的結果輸出至[Pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)。 使用語法： 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- 將``{database_id}`` 和``{container_id}``取代為您 Cosmos 帳戶中的資料庫和容器名稱。 如果未提供``--container``和自變數，則會在[預設的資料庫和容器](#set-default-database-for-queries)上執行查詢。 ``--database``
- 取代``{outputDataFrameVar}``為將包含結果之資料框架變數的名稱。
- 您可以在 Azure Cosmos DB 中執行有效的任何 SQL 查詢。 查詢文字必須在新行上。 

例如:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>設定查詢的預設資料庫
您可以設定將用於筆記本```%%sql```的預設資料庫命令。 將```{database_id}```取代為您的資料庫名稱。

```bash
%database {database_id}
```
在```%database?```儲存格中執行，以查看筆記本中的檔。

## <a name="set-default-container-for-queries"></a>設定查詢的預設容器
您可以設定將用於筆記本```%%sql```的預設容器命令。 以```{container_id}```您的容器名稱取代。

```bash
%container {container_id}
```
在```%container?```儲存格中執行，以查看筆記本中的檔。

## <a name="use-the-built-in-python-sdk"></a>使用內建的 Python SDK
適用于[SQL API 的 Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)第4版已安裝，並包含在 Cosmos 帳戶的筆記本環境中。

使用內``cosmos_client``建的實例來執行任何 SDK 作業。 

例如:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
請參閱[PYTHON SDK 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)。 

> [!IMPORTANT]
> 只有 SQL （核心） API 帳戶支援內建的 Python SDK。 針對其他 Api，您將需要安裝對應至 API 的[相關 Python 驅動程式](#install-a-new-package)。 

## <a name="create-a-custom-instance-of-cosmos_client"></a>建立的自訂實例``cosmos_client``
如需更多彈性，您可以建立的自``cosmos_client``定義實例，以便：

- 自訂連線[原則](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- 針對不同于您所在的 Cosmos 帳戶執行作業

您可以透過[環境變數](#access-the-account-endpoint-and-primary-key-env-variables)來存取目前帳戶的連接字串和主要金鑰。 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>存取帳戶端點和主要金鑰 env 變數
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 和``COSMOS_KEY``環境變數只適用于 SQL API。 針對其他 Api，請在您的 Cosmos 帳戶中，于 [**連接字串**] 或 [**金鑰**] 分頁中尋找端點和金鑰。  

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點
- 瞭解適用于[SQL API 的 Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
