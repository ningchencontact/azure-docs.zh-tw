---
title: 使用 Azure Data Factory 將資料複製到 Oracle 及從該處複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源存放區複製到 Oracle 資料庫，或從 Oracle 複製到支援的接收存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 3fa7612b9e4cd8a714e60879229bd0d39349494f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405920"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Oracle 複製資料及將資料複製到該處
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-onprem-oracle-connector.md)
> * [目前的版本](connector-oracle.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Oracle 資料庫複製資料及將資料複製到該處。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Oracle 資料庫。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Oracle 連接器支援下列版本的 Oracle 資料庫。 它也支援基本或 OID 驗證：

- Oracle 12c R1 (12.1)
- Oracle 11g R1、R2 (11.1、11.2)
- Oracle 10g R1、R2 (10.1、10.2)
- Oracle 9i R1、R2 (9.0.1、9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> 不支援 Oracle Proxy 伺服器。

## <a name="prerequisites"></a>必要條件

若要從不可公開存取的 Oracle 資料庫複製資料，以及將資料複製到該處，您必須設定一個「自我裝載 Integration Runtime」。 如需整合執行階段的詳細資訊，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)。 整合執行階段提供內建的 Oracle 驅動程式。 因此，當您從 Oracle 複製資料或將資料複製到該處時，不需要手動安裝驅動程式。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Oracle 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Oracle 連結服務所支援的屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | type 屬性必須設定為 **Oracle**。 | 是 |
| connectionString | 指定連線到 Oracle 資料庫執行個體所需的資訊。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 <br><br>**支援的連線類型**：您可以使用 [Oracle SID] 或 [Oracle 服務名稱] 來識別您的資料庫：<br>- 如果您使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果您使用服務名稱：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!TIP]
>如果您遇到指出「ORA-01025:UPI 參數超出範圍」的錯誤，而且您的 Oracle 版本為 8i，請將 `WireProtocolMode=1` 新增至連接字串並再試一次。

**若要啟用 Oracle 連線加密**，您有兩個選項：

1.  若要使用**三重 DES 加密 (3DES) 和進階加密標準 (AES)**，在 Oracle 伺服器端，移至 Oracle 進階安全性 (OAS) 並設定加密設定，請參閱[這裡](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) \(英文\) 的詳細資料。 ADF Oracle 連接器會自動協商加密方法，以使用建立 Oracle 連線時您在 OAS 中設定的方法。

2.  若要使用 **SSL**，請遵循下列步驟：

    1.  取得 SSL 憑證資訊。 取得您 SSL 憑證的 DER 編碼憑證資訊，並將輸出 (----- Begin Certificate … End Certificate -----) 儲存為文字檔。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **範例：** 從 DERcert.cer 擷取憑證資訊，接著將輸出儲存到 cert.txt

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  建置金鑰儲存區或信任存放區。 下列命令會建立信任存放區檔案，但不一定要使用 PKCS 12 格式的密碼。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **範例：** 會建立名為使用密碼的 MyTrustStoreFile PKCS12 truststore 檔案

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  將信任存放區檔案放置於自我裝載 IR 機器上，例如在 C:\MyTrustStoreFile 上。
    4.  在 ADF 中，使用 `EncryptionMethod=1` 和對應的 `TrustStore`/`TrustStorePassword` 值來設定 Oracle 連接字串，例如 `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`。

**範例：**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存密碼**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Oracle 資料集所支援的屬性清單。

若要從 Oracle 複製資料及將資料複製到該處，請將資料集的類型屬性設定為 **OracleTable**。 以下是支援的屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 **OracleTable**。 | 是 |
| tableName |Oracle 資料庫中連結服務所參照的資料表名稱。 | 是 |

**範例：**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Oracle 來源和接收所支援的屬性清單。

### <a name="oracle-as-a-source-type"></a>Oracle 作為來源類型

若要從 Oracle 複製資料，請將複製活動中的來源類型設定為 **OracleSource**。 複製活動的 [來源] 區段支援下列屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 **OracleSource**。 | 是 |
| oracleReaderQuery | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。 | 否 |

如果您未指定 "oracleReaderQuery"，就會使用資料集的 "structure" 區段中定義的資料行，來建構要針對 Oracle 資料庫執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle 作為接收類型

若要將資料複製到 Oracle，請將複製活動中的接收器類型設定為 **OracleSink**。 複製活動的 [接收] 區段支援下列屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 **OracleSink**。 | 是 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中<br/>允許的值為整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 00:30:00 (30 分鐘)。 | 否 |
| preCopyScript | 指定一個供複製活動在每次執行時將資料寫入到 Oracle 前執行的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Oracle 的資料類型對應

從 Oracle 複製資料及將資料複製到該處時，會使用下列從 Oracle 資料類型對應到 Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Oracle 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(僅 Oracle 10g 及更高版本可支援) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (如果精確度 > 28) |
| INTEGER |Decimal, String (如果精確度 > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (如果精確度 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 不支援 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND 資料類型。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
