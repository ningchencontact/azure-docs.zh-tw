---
title: Azure 受控應用程式視圖定義成品參考
description: 提供 Azure 受控應用程式視圖定義成品的範例。 檔案名為 viewDefinition. json。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 1f56f6c9c519bd29423d92a8dc8b8ce5904b523c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332626"
---
# <a name="reference-view-definition-artifact"></a>參考：視圖定義成品

本文是 Azure 受控應用程式中*viewDefinition*的參考。 如需撰寫 views 設定的詳細資訊，請參閱[View definition](concepts-view-definition.md)成品。

## <a name="view-definition"></a>檢視定義

下列 JSON 顯示 Azure 受控應用程式的*viewDefinition json*檔案範例：

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>後續步驟

- [教學課程：使用自訂動作和資源建立受控應用程式](tutorial-create-managed-app-with-custom-provider.md)
- [參考：使用者介面專案成品](reference-createuidefinition-artifact.md)
- [參考：部署範本成品](reference-main-template-artifact.md)