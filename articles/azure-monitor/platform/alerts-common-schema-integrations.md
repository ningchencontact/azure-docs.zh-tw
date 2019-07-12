---
title: 如何使用 Logic Apps 整合的常見的警示結構描述
description: 了解如何建立邏輯應用程式，運用常見的警示結構描述來處理所有警示。
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734897"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何使用 Logic Apps 整合的常見的警示結構描述

這篇文章會示範如何建立邏輯應用程式，運用常見的警示結構描述來處理所有警示。

## <a name="overview"></a>總覽

[常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)提供標準化、 可延伸的 JSON 結構描述所有您不同警示類型。 常見的警示結構描述是利用程式設計的方式 – 透過 webhook、 runbook 以及邏輯應用程式時最有用的。 在本文中，我們會示範如何撰寫單一邏輯應用程式，來處理所有警示。 相同的原則可以套用至其他以程式設計的方式。 這篇文章中所述的邏輯應用程式會建立定義完善的變數，如['基本' 欄位](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)，，同時描述如何處理[警示類型](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields)特定邏輯。


## <a name="prerequisites"></a>先決條件 

本文假設讀者已熟悉 
* 設定警示規則 ([公制](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)，[記錄](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，[活動記錄檔](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* 設定[動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 啟用[常見的警示結構描述](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)從動作群組內

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>建立邏輯應用程式利用常見的警示結構描述

1. 請遵循[來建立邏輯應用程式所概述的步驟](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)。 

1.  選取觸發程序：**收到 HTTP 要求時**。

    ![邏輯應用程式觸發程序](media/action-groups-logic-app/logic-app-triggers.png "邏輯應用程式觸發程序")

1.  選取 [編輯]  以變更 HTTP 要求觸發程序。

    ![HTTP 要求觸發程序](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 要求觸發程序")


1.  複製並貼上下列結構描述：

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. 選取 [+ 新增步驟]   ，然後選擇 [新增動作]  。

    ![新增動作](media/action-groups-logic-app/add-action.png "新增動作")

1. 在這個階段，您可以新增各種不同的連接器 （Microsoft Teams、 Slack、 Salesforce 等） 根據特定的業務需求。 您可以使用 '必要欄位'--蜪鎏。 

    ![必要的欄位](media/alerts-common-schema-integrations/logic-app-essential-fields.png "必要欄位")
    
    或者，您可以撰寫使用 'Expression' 選項的警示類型為基礎的條件式邏輯。

    ![邏輯應用程式的運算式](media/alerts-common-schema-integrations/logic-app-expressions.png "邏輯應用程式的運算式")
    
     ['MonitoringService' 欄位](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields)可讓您用來唯一識別警示的類型，以在您可以建立條件式邏輯。

    
    例如，以下程式碼片段會檢查是否 Application Insights 型記錄警示，並警示的話會列印搜尋結果。 否則，它會列印 'NA'。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     深入了解[撰寫邏輯應用程式的運算式](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)。

    


## <a name="next-steps"></a>後續步驟

* [深入了解動作群組](../../azure-monitor/platform/action-groups.md)。
* [深入了解常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)。

