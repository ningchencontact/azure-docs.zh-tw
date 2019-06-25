---
title: 從 Office 365 使用 Azure Data Factory 複製資料 |Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Office 365 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475661"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Office 365 中的資料複製到 Azure 中使用 Azure Data Factory

Azure Data Factory 整合[Microsoft Graph 的資料連接](https://docs.microsoft.com/graph/data-connect-concept-overview)、 可讓您將組織的資料，在 Office 365 租用戶至 Azure，可調整的方式，並建置分析應用程式的豐富及擷取為基礎的深入解析這些重要的資料資產。 與 Privileged Access Management 整合可針對 Office 365 中的重要策劃資料提供安全的存取控制。  請參閱[此連結](https://docs.microsoft.com/graph/data-connect-concept-overview)如需 Microsoft Graph 資料的概觀連接和指[此連結](https://docs.microsoft.com/graph/data-connect-policies#licensing)如需授權資訊。

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Office 365 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能
ADF Office 365 連接器和 Microsoft Graph 資料的可讓在不同類型的資料集的規模擷取則會從啟用的 Exchange 電子郵件信箱，包括通訊錄的連絡人、 行事曆事件、 電子郵件訊息，使用者資訊、 信箱設定連線和等等。  請參閱[此處](https://docs.microsoft.com/graph/data-connect-datasets)若要查看可用的資料集的完整清單。

現在，在單一複製活動中只可**的資料複製到 Office 365 [Azure Blob 儲存體](connector-azure-blob-storage.md)， [Azure Data Lake 儲存體 Gen1](connector-azure-data-lake-store.md)，和[Azure Data Lake 儲存體 Gen2](connector-azure-data-lake-storage.md) JSON 格式**（輸入 setOfObjects）。 如果您想要將 Office 365 載入其他類型的資料存放區，或以其他格式載入，可以將第一個複製活動與後續的複製活動鏈結，進一步將資料載入任何[支援的 ADF 目的地存放區](copy-activity-overview.md#supported-data-stores-and-formats) (請參閱「支援的資料存放區和格式」資料表中的「支援作為接收器」)。

>[!IMPORTANT]
>- 包含資料處理站和接收資料存放區的 Azure 訂用帳戶必須與 Office 365 租用戶位於相同的 Azure Active Directory (Azure AD) 租用戶下。
>- 請確定用於複製活動的 Azure Integration Runtime 地區以及目的地與 Office 365 租用戶使用者的信箱所在區域相同。 若要了解如何判斷 Azure IR 位置，請參閱[這裡](concepts-integration-runtime.md#integration-runtime-location)。 如需支援的 Office 區域和對應的 Azure 區域清單，請參閱[以下資料表](https://docs.microsoft.com/graph/data-connect-datasets#regions)。
>- 服務主體驗證是唯一的驗證機制，支援 Azure Blob 儲存體、 Azure Data Lake 儲存體 Gen1 和 Azure Data Lake 儲存體 Gen2 做為目的地存放區。

## <a name="prerequisites"></a>必要條件

若要將資料從 Office 365 複製到 Azure，您必須完成下列必要步驟：

- 您的 Office 365 租用戶系統管理員必須完成上架動作，如[此處](https://docs.microsoft.com/graph/data-connect-get-started)所述。
- 在 Azure Active Directory 中建立和設定 Azure AD Web 應用程式。  如需指示，請參閱[建立 Azure AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。
- 請記下以下的值，您可以使用這些值來定義 Office 365 的連結服務：
    - 租用戶識別碼。 如需相關指示，請參閱[取得租用戶識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。
    - 應用程式識別碼和驗證金鑰。  如需相關指示，請參閱[取得應用程式識別碼和驗證金鑰](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。
- 新增使用者身分識別，該使用者會以 Azure AD Web 應用程式擁有者身分進行資料存取要求 (從 [Azure AD Web 應用程式] > [設定] > [擁有者] > [新增擁有者])。 
    - 使用者身分識別必須位於要從中取得資料的 Office 365 組織中，而且不可以是來賓使用者。

## <a name="approving-new-data-access-requests"></a>核准新的資料存取要求

如果這是您第一次要求此內容 (結合正在存取的資料資料表、正在載入資料的目的地帳戶，以及進行資料存取要求的使用者身分識別) 的資料，您會看到複製活動狀態為「進行中」，且僅在您按一下 [[動作] 底下的 [詳細資料] 連結](copy-activity-overview.md#monitoring)時，會看到狀態為 “RequestingConsent”。  資料存取核准者群組的成員必須核准 Privileged Access Management 中的要求，資料擷取才能繼續。

請參閱[這裡](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal)了解核准者如何核准資料存取要求，然後參考[這裡](https://docs.microsoft.com/graph/data-connect-pam)，了解 Privileged Access Management 整體整合的相關說明，包括如何設定資料存取核准者群組。

## <a name="policy-validation"></a>原則驗證

如果建立 ADF 作為受控應用程式的一部分，並且對管理資源群組內的資源進行 Azure 原則指派，則 ADF 會檢查每個複製活動執行，以確定原則指派已強制執行。 請參閱[這裡](https://docs.microsoft.com/graph/data-connect-policies#policies)取得支援的原則清單。

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

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Office365** | 是 |
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

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**Office365Table** | 是 |
| tableName | 擷取自 Office 365 的資料集名稱。 如需可供擷取的 Office 365 資料集清單，請參閱[這裡](https://docs.microsoft.com/graph/data-connect-datasets#datasets)。 | 是 |
| allowedGroups | 群組選取述詞。  您可以使用這個屬性來選取最多 10 個使用者群組，將其擷取的資料。  如果沒有指定任何群組，則會在整個組織傳回的資料。 | 否 |
| userScopeFilterUri | 當`allowedGroups`未指定屬性，您可以使用會套用至整個租用戶，以擷取從 Office 365 的特定資料列篩選述詞運算式。 述詞的格式應該符合查詢的格式 Microsoft Graph Api，例如`https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`。 | 否 |
| dateFilterColumn | 日期時間篩選條件資料行的名稱。 您可以使用這個屬性來限制哪些 Office 365 擷取資料的時間範圍。 | 如果資料集有一或多個日期時間資料行，[是]。 請參閱[此處](https://docs.microsoft.com/graph/data-connect-filtering#filtering)如需要這個日期時間篩選器的資料集的清單。 |
| startTime | 開始日期時間值，做為篩選條件。 | [是] 如果`dateFilterColumn`指定 |
| endTime | 結束日期時間值，做為篩選條件。 | [是] 如果`dateFilterColumn`指定 |

**範例**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
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
