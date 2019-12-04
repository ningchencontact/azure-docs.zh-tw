---
title: 連線至 GitHub
description: 使用 GitHub REST API 和 Azure Logic Apps 監視 GitHub 事件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789745"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>從 Azure Logic Apps 連接到 GitHub

GitHub 是網頁式 Git 存放庫主機服務，它提供 Git 中所有分散式版本控制和原始程式碼管理 (SCM) 功能，以及其他功能。

若要開始使用 GitHub 連接器，[請先建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-github"></a>建立 GitHub 的連線

若要在邏輯應用程式中使用 GitHub 連接器，您必須先建立「連線」，然後提供這些屬性的詳細資料： 

| 屬性 | 必要項 | 描述 | 
| -------- | -------- | ----------- | 
| token | 是 | 提供您的 GitHub 認證。 |

建立連線後，您就可以執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>連接器參考

如需有關觸發程式、動作和限制的技術詳細資料（由連接器的 OpenAPI （先前稱為 Swagger）描述所描述），請參閱[連接器的參考頁面](/connectors/github/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)