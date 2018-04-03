---
title: Azure Resource Manager 範本範例 - Event Grid | Microsoft Docs
description: Event Grid 的 Azure Resource Manager 範本範例
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 的 Azure Resource Manager 範本

下表包含 Event Grid 的 Azure Resource Manager 範本連結。

| | |
|-|-|
|**Event Grid 訂用帳戶**||
| [使用 WebHook 端點的自訂主題和訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| 部署 Event Grid 自訂主題。 為使用 WebHook 端點的自訂主題建立訂用帳戶。 |
| [使用 EventHub 端點的自訂主題訂用帳戶](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| 為已存在的自訂主題建立 Event Grid 訂閱帳戶。 訂用帳戶會使用端點的事件中樞。 |
| [資源群組訂用帳戶](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| 訂閱資源群組的事件。 在部署期間指定為目標的資源群組為事件來源。 訂用帳戶會使用端點的事件中樞。 |
| [Blob 儲存體帳戶和訂用帳戶](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| 部署 Azure Blob 儲存體帳戶及訂閱該儲存體帳戶的事件。 |
| | |
