---
title: "使用 Azure Logic Apps 連線到 GitHub | Microsoft Docs"
description: "使用 Azure Logic Apps 自動化 GitHub 的工作流程"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-github"></a>連線至 GitHub

GitHub 是網頁式 Git 存放庫主機服務，它提供 Git 中所有分散式版本控制和原始程式碼管理 (SCM) 功能，以及其他功能。

若要開始使用 GitHub 連接器，[請先建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-github"></a>建立 GitHub 的連線

若要在邏輯應用程式中使用 GitHub 連接器，您必須先建立「連線」，然後提供這些屬性的詳細資料： 

| 屬性 | 必要 | 說明 | 
| -------- | -------- | ----------- | 
| token | yes | 提供您的 GitHub 認證。 |

建立連線後，您就可以執行動作，並接聽本文所述的觸發程序。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需 Swagger 中定義的觸發程序和動作及任何限制，請參閱[連接器詳細資料](/connectors/github/)。

## <a name="find-more-connectors"></a>尋找其他連接器

* 檢閱[連接器清單](apis-list.md)。