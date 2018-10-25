---
title: 使用 Azure Data Factory 複製 Office 365 中的資料 (預覽) | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Office 365 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 82fb2241b5988bae9587807c03e7bec50e7c1677
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955368"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>使用 Azure Data Factory 將資料從 Office 365 複製到 Azure (預覽) 

Azure Data Factory 可讓您將 Office 365 租用戶中豐富的組織資料以可調式方法帶入 Azure，並建置分析應用程式，以及根據這些寶貴的資料資產擷取深入解析。 與 Privileged Access Management 整合可針對 Office 365 中的重要策劃資料提供安全的存取控制。  如需 Microsoft Graph 資料連線的相關資訊，請參閱[此連結](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki)。

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Office 365 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

現在，在單一複製活動內，您只能以 JSON 格式 (setOfObjects 類型) **將資料從 Office 365 複製到 [Azure Blob 儲存體](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)，和 [Azure Data Lake Storage Gen2 (預覽)](connector-azure-data-lake-storage.md)**。 如果您想要將 Office 365 載入其他類型的資料存放區，或以其他格式載入，可以將第一個複製活動與後續的複製活動鏈結，進一步將資料載入任何[支援的 ADF 目的地存放區](copy-activity-overview.md#supported-data-stores-and-formats) (請參閱「支援的資料存放區和格式」資料表中的「支援作為接收器」)。

>[!IMPORTANT]
>- 包含資料處理站和接收資料存放區的 Azure 訂用帳戶必須與 Office 365 租用戶位於相同的 Azure Active Directory (Azure AD) 租用戶下。
>- 請確定用於複製活動的 Azure Integration Runtime 地區以及目的地與 Office 365 租用戶使用者的信箱所在區域相同。 若要了解如何判斷 Azure IR 位置，請參閱[這裡](concepts-integration-runtime.md#integration-runtime-location)。 如需支援的 Office 區域和對應的 Azure 區域清單，請參閱[以下資料表](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions)。
>-  如果您將 Office 365 資料載入作為目的地的 **Azure Blob 儲存體**，請確定您在定義 Azure Blob 儲存體的連結服務時是使用**[服務主體驗證](connector-azure-blob-storage.md#service-principal-authentication)**，而不是使用[帳戶金鑰](connector-azure-blob-storage.md#account-key-authentication)、[共用的存取簽章](connector-azure-blob-storage.md#shared-access-signature-authentication)或是[適用於 Azure 資源的受控識別](connector-azure-blob-storage.md#managed-identity)驗證。
>-  如果您將 Office 365 資料載入作為目的地的 **Azure Data Lake Storage Gen1**，請確定您在定義 Azure Data Lake Storage Gen1 的連結服務時是使用[**服務主體驗證**](connector-azure-data-lake-store.md#using-service-principal-authentication)，而不是使用[適用於 Azure 資源的受控識別驗證](connector-azure-data-lake-store.md#managed-identity)。

## <a name="prerequisites"></a>必要條件

若要將資料從 Office 365 複製到 Azure，您必須完成下列必要步驟：

- 您的 Office 365 租用戶系統管理員必須完成上架動作，如[此處](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding)所述。
- 在 Azure Active Directory 中建立和設定 Azure AD Web 應用程式。  如需指示，請參閱[建立 Azure AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。
- 請記下以下的值，您可以使用這些值來定義 Office 365 的連結服務：
    - 租用戶識別碼。  如需相關指示，請參閱[取得租用戶識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id)。
    - 應用程式識別碼和驗證金鑰。  如需相關指示，請參閱[取得應用程式識別碼和驗證金鑰](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key)。
- 新增使用者身分識別，該使用者會以 Azure AD Web 應用程式擁有者身分進行資料存取要求 (從 [Azure AD Web 應用程式] > [設定] > [擁有者] > [新增擁有者])。

## <a name="approving-new-data-access-requests"></a>核准新的資料存取要求

如果這是您第一次要求此內容 (結合正在存取的資料資料表、正在載入資料的目的地帳戶，以及進行資料存取要求的使用者身分識別) 的資料，您會看到複製活動狀態為「進行中」，且僅在您按一下 [[動作] 底下的 [詳細資料] 連結](copy-activity-overview.md#monitoring)時，會看到狀態為 “RequestingConsent”。  資料存取核准者群組的成員必須核准 Privileged Access Management 中的要求，資料擷取才能繼續。

請參閱[這裡](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests)了解核准者如何核准資料存取要求，然後參考[這裡](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management)，了解 Privileged Access Management 整體整合的相關說明，包括如何設定資料存取核准者群組。

## <a name="policy-validation"></a>原則驗證

如果建立 ADF 作為受控應用程式的一部分，並且對管理資源群組內的資源進行 Azure 原則指派，則 ADF 會檢查每個複製活動執行，以確定原則指派已強制執行。 請參閱[這裡](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies)取得支援的原則清單。

## <a name="getting-started"></a>開始使用

>[!TIP]
>如需使用 Office 365 連接器的逐步解說，請參閱[從 Office 365 載入資料](load-office-365-data.md)文章。

您可以使用下列其中一個工具或 SDK，以建立內含複製活動的管線。 選取連結以移至逐步指示教學課程，以建立內含複製活動的管線。 

- [Azure 入口網站](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager 範本](quickstart-create-data-factory-resource-manager-template.md)。 

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Office 365 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 Office 365 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為：**Office365** | 是 |
| office365TenantId | Office 365 帳戶所屬的 Azure 租用戶識別碼。 | 是 |
| servicePrincipalTenantId | 指定您 Azure AD Web 應用程式所在的租用戶資訊。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 | 是 |
| connectVia | 用來連線到資料存放區的整合執行階段。  如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 |

>[!NOTE]
> **office365TenantId** 與 **servicePrincipalTenantId** 之間的差異以及要提供的對應值：
>- 如果您是企業開發人員，負責針對自己組織使用的 Office 365 資料開發應用程式，則您應該對這兩個屬性提供相同的租用戶識別碼，也就是貴組織的 AAD 租用戶識別碼。
>- 如果您是為客戶開發應用程式的 ISV 開發人員，則 office365TenantId 將會是您客戶的 (應用程式安裝程式) AAD 租用戶識別碼，而 servicePrincipalTenantId 將為貴公司的 AAD 租用戶識別碼。

**範例：**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Office 365 資料集所支援的屬性清單。

若要從 Office 365 複製資料，以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為：**Office365Table** | 是 |
| tableName | 擷取自 Office 365 的資料集名稱。 如需可供擷取的 Office 365 資料集清單，請參閱[這裡](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets)。 | 是 |
| 述詞 | 述詞運算式，可用來篩選要從 Office 365 中擷取的特定資料列。  請參閱[這裡](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters)，了解每個資料表中有哪些資料行可用於述詞篩選，以及篩選條件運算式的格式。 | 否<br>(如果沒有提供任何述詞，預設值是擷取過去 30 天的資料) |

**範例**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Office 365 來源所支援的屬性清單。

### <a name="office-365-as-source"></a>Office 365 作為來源

若要從 Office 365 複製資料，請將複製活動中的來源類型設定為 **Office365Source**。 複製活動的 **source** 區段中不支援其他屬性。

**範例：**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
