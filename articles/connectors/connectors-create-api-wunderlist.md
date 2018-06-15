---
title: Azure Logic Apps 中的 Wunderlist 連接器 | Microsoft Docs
description: 建立 Wunderlist 連線，並使用此連線在 Logic Apps 中建置工作流程。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 1ed9b19700157abca6e5ac4265f1e8c99a3d846d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296368"
---
# <a name="get-started-with-the-wunderlist-connector"></a>開始使用 Wunderlist 連接器
Wunderlist 是可協助使用者完成其工作的待辦事項清單和工作管理員。  無論是與他人共用購物清單、處理專案，還是計劃假期，Wunderlist 都能讓您輕鬆擷取、共用及完成清單項目。 Wunderlist 立即同步處理您的電話、平板電腦及電腦，讓您可在任何地方存取工作。

立即開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-wunderlist"></a>建立 Wunderlist 的連線
若要使用 Wunderlist 建立邏輯應用程式，您必須先建立**連接**，然後提供下列屬性的詳細資料：

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| token |yes |提供 Wunderlist 認證 |

建立連線後，您就可以用它執行動作，並接聽觸發程序。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/wunderlist/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。