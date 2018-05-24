---
title: Azure 父代資源錯誤 | Microsoft Docs
description: 描述當使用父代資源時如何解決錯誤。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c996a644f206051cb58522065f87f95a4058cdee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-parent-resources"></a>解決父代資源的錯誤

這篇文章描述當部署相依於父代資源的資源時，可能會遇到的錯誤。

## <a name="symptom"></a>徵狀

部署是另一個資源子系的資源時，您可能會遇到下列錯誤：

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>原因

當某資源為另一個資源的子系資源時，父代資源必須在建立子系資源之前就存在。 子資源的名稱包含父系名稱。 例如，SQL Database 可能定義如下︰

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

不過，如果您未在伺服器上指定相依性，可能可以在部署伺服器之前啟動資料庫部署。

## <a name="solution"></a>解決方法

若要解決這個錯誤，請包含相依性。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

如需詳細資訊，請參閱[定義 Azure Resource Manager 範本中部署資源的順序](resource-group-define-dependencies.md)。