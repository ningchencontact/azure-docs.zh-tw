---
title: Azure 受控應用程式中的檢視定義的概觀 |Microsoft Docs
description: 說明建立 Azure 受控應用程式的檢視定義的概念。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478741"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure 受控應用程式中的檢視定義成品

檢視表定義具中 Azure 受控應用程式的選擇性成品。 它可讓自訂 [概觀] 頁面，並加入更多的檢視，例如計量與自訂資源。

本文章提供檢視定義成品的概觀，以及其功能。

## <a name="view-definition-artifact"></a>檢視定義成品

必須命名為檢視定義成品**viewDefinition.json**並放在相同的層級**createUiDefinition.json**並**mainTemplate.json**中將.zip建立受控應用程式定義的封裝。 若要了解如何建立.zip 套件並發佈受控應用程式定義，請參閱[發佈 Azure 受控應用程式定義](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>檢視定義結構描述

**ViewDefinition.json**檔案有只有一個頂層`views`屬性，這是檢視的陣列。 每一個檢視表所示的受管理的應用程式使用者介面為個別的功能表項目資料表中的內容。 每個檢視有`kind`設定的檢視類型的屬性。 它必須設定為下列值之一：[概觀](#overview)，[計量](#metrics)， [CustomResources](#custom-resources)。 如需詳細資訊，請參閱目前[viewDefinition.json 的 JSON 結構描述](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

如需檢視定義的範例 JSON:

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>概觀

`"kind": "Overview"`

當您提供的這個檢視中**viewDefinition.json**，它會覆寫在 managed 應用程式中的 [預設概觀] 頁面。

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|屬性|必要項|描述|
|---------|---------|---------|
|頁首|否|[概觀] 頁面的標頭。|
|description|否|您受管理的應用程式的描述。|
|命令|否|陣列的其他工具列按鈕的 [概觀] 頁面中，請參閱[命令](#commands)。|

## <a name="metrics"></a>度量

`"kind": "Metrics"`

[計量] 檢視可讓您收集和彙總資料，您受管理的應用程式中的資源[Azure 監視器計量](../azure-monitor/platform/data-platform-metrics.md)。

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|屬性|必要項|描述|
|---------|---------|---------|
|displayName|否|此檢視顯示的標題。|
|version|否|用來呈現檢視的平台版本。|
|圖表|是|圖表的 [度量] 頁面的陣列。|

### <a name="chart"></a>圖表

|屬性|必要項|描述|
|---------|---------|---------|
|displayName|是|圖表顯示的標題。|
|chartType|否|要用於此圖表的視覺效果。 根據預設，它會使用折線圖。 支援的圖表類型： `Bar, Line, Area, Scatter`。|
|metrics|是|此圖表上繪製的度量資訊的陣列。 若要深入了解在 Azure 入口網站中支援的計量，請參閱[支援 Azure 監視器的度量](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>計量

|屬性|必要項|描述|
|---------|---------|---------|
|name|是|計量的名稱。|
|aggregationType|是|若要使用此計量彙總類型。 支援的彙總類型： `none, sum, min, max, avg, unique, percentile, count`|
|namespace|否|若要判斷正確的計量提供者時所使用的其他資訊。|
|resourceTagFilter|否|資源標記的陣列 (會以分隔`or`word) 會顯示涉及哪些計量。 適用於資源類型篩選之上。|
|resourceType|是|計量會顯示資源類型。|

## <a name="custom-resources"></a>自訂資源

`"kind": "CustomResources"`

您可以定義此類型的多個檢視。 每個檢視代表**唯一**自訂資源類型的自訂提供者中定義**mainTemplate.json**。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](custom-providers-overview.md)\(英文\)。

在此檢視，您可以執行 GET、 PUT、 刪除和發佈您的自訂資源類型的作業。 POST 作業可能是自訂的全域動作或在內容中的自訂資源類型的自訂動作。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|屬性|必要項|描述|
|---------|---------|---------|
|displayName|是|此檢視顯示的標題。 標題應該**唯一**中的每個 CustomResources 檢視您**viewDefinition.json**。|
|version|否|用來呈現檢視的平台版本。|
|resourceType|是|自訂資源類型。 必須是**唯一**自訂提供者的自訂資源類型。|
|createUIDefinition|否|建立 UI 定義結構描述建立自訂資源的命令。 如需建立 UI 定義，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)|
|命令|否|陣列的其他工具列按鈕的 CustomResources 檢視中，請參閱[命令](#commands)。|
|columns|否|自訂資源的資料行的陣列。 如果未定義`name`預設會顯示資料行。 資料行必須有`"key"`和`"displayName"`。 針對索引鍵，提供要在檢視中顯示之屬性的索引鍵。 如果巢狀，使用點做為分隔符號，例如`"key": "name"`或`"key": "properties.property1"`。 顯示名稱 中，提供要在檢視中顯示之屬性的顯示名稱。 您也可以提供`"optional"`屬性。 當設為 true，資料行預設為隱藏在檢視中。|

## <a name="commands"></a>命令

命令會顯示在頁面上的其他工具列按鈕的陣列。 每個命令則代表從 Azure 自訂提供者中定義的後置動作**mainTemplate.json**。 如需自訂提供者，請參閱 < [Azure 自訂提供者概觀](custom-providers-overview.md)。

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|屬性|必要項|描述|
|---------|---------|---------|
|displayName|是|命令按鈕的顯示的名稱。|
|path|是|自訂提供者的動作名稱。 動作必須定義於**mainTemplate.json**。|
|圖示|否|命令按鈕的圖示。 支援的圖示清單中定義[JSON 結構描述](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。|
|createUIDefinition|否|建立 UI 定義命令的結構描述。 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。|

## <a name="next-steps"></a>後續步驟

- 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](overview.md)。
- 如需自訂提供者，請參閱 < [Azure 自訂提供者概觀](custom-providers-overview.md)。
