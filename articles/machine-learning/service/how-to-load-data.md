---
title: 載入：資料準備 Python SDK
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 資料準備 SDK 來載入資料。 您可以載入不同類型的輸入資料、指定資料類型與參數，或使用 SDK 智慧型讀取功能來自動偵測檔案類型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 4bd6605a6a217d6bcdd243ff1d4f7bb6017ca0ef
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036057"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure Machine Learning 資料準備 SDK 來載入和讀取資料
在本文中, 您會瞭解使用 Azure Machine Learning 資料準備 SDK 來載入資料的不同方法。  SDK 支援多個資料擷取功能，包括：

* 使用剖析參數推斷 (編碼、分隔符號、標頭)，從許多檔案類型載入
* 在檔案載入期間使用推斷進行類型轉換
* MS SQL Server 和 Azure Data Lake Storage 的連線支援

> [!Important]
> 如果您要建立新的解決方案, 請嘗試[Azure Machine Learning 資料集](how-to-explore-prepare-data.md)(預覽), 以進行資料探索和準備。 資料集是下一版的資料準備 SDK, 提供擴充功能來管理 AI 解決方案中的資料集。


下表顯示用來從一般檔案類型載入資料的函數選項。

| 檔案類型 | 函數 | 參考連結 |
|-------|-------|-------|
|Any|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|文字|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-lines-path--filepath--header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|固定寬度|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>自動載入資料

若要自動載入資料，但不指定檔案類型，請使用 `auto_read_file()` 函式。 系統會自動推斷所需的檔案類型及讀取檔案所需的引數。

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

此函式有助於自動偵測檔案類型、編碼及其他的剖析引數，全部從一個方便的進入點進行。 此函式也會自動執行下列步驟，這些步驟通常在載入分隔的資料時執行：

* 推斷和設定分隔符號
* 略過檔案頂端的空白記錄
* 推斷和設定的標頭列

或者, 如果您事先知道檔案類型, 而且想要明確地控制剖析的方式, 請使用檔案特有的函式。

## <a name="load-text-line-data"></a>載入文字行資料

若要在資料流程中讀取簡單的文字資料，請使用 `read_lines()`，但不指定選擇性參數。

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||行|
|----|-----|
|0|日期 \|\|  最低溫度 \|\|  最高溫度|
|1|2015-07-1 \|\|  -4.1 \|\|  10.0|
|2|2015-07-2 \|\|  -0.8 \|\|  10.8|


擷取資料之後，執行下列程式碼，將資料流程物件轉換成 Pandas 資料框架。

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>載入 CSV 資料

讀取分隔檔時，基礎執行階段可以推斷剖析參數 (分隔符號、編碼、是否要使用標頭等)。 執行下列程式碼以嘗試透過只指定 CSV 檔案的位置來讀取它。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|阿拉巴馬州|1|101710|Hale County|10171002158| |
|2|阿拉巴馬州|1|101710|Hale County|10171002162| |


若要在載入期間排除行，請定義 `skip_rows` 參數。 此參數將會略過 CSV 檔案中遞減載入的資料列 (使用以一為基底的索引)。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                       skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|阿拉巴馬州|1|101710|Hale County|10171002158|29|
|1|阿拉巴馬州|1|101710|Hale County|10171002162|40 |

執行下列程式碼以顯示資料行資料類型。

```python
dflow.dtypes
```
輸出：

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Azure Machine Learning 資料準備 SDK 預設不會變更您的資料類型。 您所讀取的資料來源是文字檔，因此 SDK 會以字串形式讀取所有值。 此範例中，數值資料行應該以數字的形式進行剖析。 將 `inference_arguments` 參數設定為 `InferenceArguments.current_culture()` 可自動推斷，並在檔案讀取期間轉換資料行類型。

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
輸出：

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


已將數個資料行正確偵測為數字，且其類型已設定為 `float64`。

## <a name="use-excel-data"></a>使用 Excel 資料

SDK 包含可載入 Excel 檔案的 `read_excel()` 函式。 根據預設，此函式將會載入活頁簿中的第一個工作表。 若要定義要載入的特定工作表，請使用工作表名稱的字串值定義 `sheet_name` 參數。

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|None|None|None|None|None|None|None|None|None| |
|1|None|None|None|None|None|None|None|None|None| |
|2|None|None|None|None|None|None|None|None|None| |
|3|順序|標題|Studio|全球|國內 / %|資料行 1|海外 / %|資料行 2|年^| |
|4|1|阿凡達|福斯|2788|760.5|0.273|2027.5|0.727|2009^|5|

輸出顯示第二個工作表中的資料在標頭之前有三個空白資料列。 `read_excel()` 函式包含選擇性參數，可略過資料列並使用標頭。 執行下列程式碼可略過前三個資料列，並使用第四個資料列作為標頭。

```python
dflow = dprep.read_excel(path='./data/excel.xlsx',
                         sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||順序|標題|Studio|全球|國內 / %|資料行 1|海外 / %|資料行 2|年^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|阿凡達|福斯|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|鐵達尼號|派拉蒙影業|2186.8|658.7|0.301|1528.1|0.699|1997^|

## <a name="load-fixed-width-data-files"></a>載入固定寬度的資料檔案

若要載入固定寬度的檔案, 請指定字元位移清單。 第一個資料行一律假設為從零位移開始。

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|BOGUS 挪威|否|NO_1|ENRS|資料行 7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS 挪威|否|否|ENSO||||
|1|010010|99999|揚馬延|否|JN|ENJA|+70933|-008667|+00090|


若要避免標頭偵測並剖析正確的資料，請將 `PromoteHeadersMode.NONE` 傳遞至 `header` 參數。

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                       header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS 挪威|否|NO_1|ENRS|資料行 7|Column8|Column9|
|1|010003|99999|BOGUS 挪威|否|否|ENSO||||


## <a name="load-sql-data"></a>載入 SQL 資料

SDK 也可以從 SQL 來源載入資料。 目前只支援 Microsoft SQL Server。 若要從 SQL server 讀取資料, 請建立[`MSSQLDataSource`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py)包含連接參數的物件。 的 password 參數`MSSQLDataSource`會[`Secret`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret)接受物件。 您能以兩種方式建置祕密物件：

* 向執行引擎註冊祕密與其值。
* 使用 `dprep.create_secret("[SECRET-ID]")` 建立只有 `id` (若祕密值已在執行環境中註冊) 的祕密。

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

在您建立資料來源物件之後，可以繼續從查詢輸出讀取資料。

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|Name|ProductNumber|色彩|StandardCost|ListPrice|Size|權數|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|黑色|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|紅色|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|紅色|13.0863|34.99|None|None|35|33|2005-07-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|


## <a name="use-azure-data-lake-storage"></a>使用 Azure Data Lake 儲存體

SDK 能以兩種方式取得必要 OAuth 權杖以存取 Azure Data Lake Storage：

* 從使用者 Azure CLI 登入的最近工作階段擷取存取權杖
* 使用服務主體 (SP) 與憑證做為祕密

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>使用來自最近 Azure CLI 工作階段的存取權杖

在您的本機電腦上，執行下列命令。

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> 若您的使用者帳戶是多個 Azure 租用戶的成員，您必須以 AAD URL 主機名稱格式指定該租用戶。

### <a name="create-a-service-principal-with-the-azure-cli"></a>使用 Azure CLI 建立服務主體

使用 Azure CLI 建立服務主體與對應的憑證。 這個特定服務主體設定為 `reader` 角色，而且其範圍縮減為僅限 Azure Data Lake Storage 帳戶 'dpreptestfiles'。

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

此命令會發出 `appId` 與憑證檔案的路徑 (通常位於主資料夾)。 .crt 檔案同時包含 PEM 格式的公開憑證與私密金鑰。

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

若要為 Azure Data Lake Storage 檔案系統設定 ACL，請使用使用者的 objectId。 在此範例中，會使用服務主體。

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

若要設定 Azure Data Lake Storage 檔案系統的 `Read` 和 `Execute` 存取，您要個別設定資料夾和檔案的 ACL。 這是因為基礎 HDFS ACL 模型不支援繼承。

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>取得 OAuth 存取權杖

使用 `adal` 套件 (`pip install adal`) 在 MSFT 租用戶上建立驗證內容，並取得 OAuth 存取權杖。 針對 ADLS, 權杖要求中的資源必須是 ' HTTPs:\//datalake.azure.net ', 這與大部分的其他 azure 資源不同。

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext(
    'https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate(
    'https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path=DataLakeDataSource(
    path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|網站|street|city|縣市|
|----|------|-----|----|----|----|----|
|0|1012063|喀里多尼亞農民市場協會 - 丹維爾|https://sites.google.com/site/caledoniafarmers.. ||丹維爾|喀里多尼亞|
|1|1011871|Stearns Homestead 農民市集|http://Stearnshomestead.com |6975 Ridge Road|帕爾馬|凱霍加郡|
|2|1011878|100 Mile Market|https://www.pfcmarkets.com |507 Harrison St|卡拉馬朱|卡拉馬朱|
|3|1009364|106 南主幹道農民市集|http://thetownofsixmile.wordpress.com/ |106 南主幹道|六英里|||
|4|1010691|第 10 街社區農民市集|https://agrimissouri.com/.. |10th Street and Poplar|拉馬爾|巴頓|

## <a name="next-steps"></a>後續步驟

* 如需詳細資訊, 請參閱 Azure Machine Learning 資料準備 SDK[參考檔](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#dataprep)。
