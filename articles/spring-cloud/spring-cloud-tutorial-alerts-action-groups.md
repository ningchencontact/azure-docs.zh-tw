---
title: 教學課程：使用警示和動作群組監視 Azure Spring Cloud 資源 | Microsoft Docs
description: 了解如何使用 Spring Cloud 警示。
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689563"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>教學課程：使用警示和動作群組監視 Spring 雲端資源

Azure Spring Cloud 警示支援根據可用的儲存體、要求率或資料使用量等條件來監視資源。 當比率或條件符合定義的規格時，警示就會傳送通知。

設定警示管線有兩個步驟： 
1. 設定動作群組，其中包含觸發警示時所要採取的動作，例如電子郵件、SMS、Runbook 或 Webhook。 動作群組可在不同的警示之間重複使用。
2. 設定警示規則。 規則會根據目標資源、計量、條件、時間彙總來繫結計量模式與動作群組。

## <a name="prerequisites"></a>必要條件
除了 Azure Spring 需求以外，本教學課程也需使用下列資源。

* 已部署的 Azure Spring Cloud 執行個體。  請遵循我們的[快速入門](spring-cloud-quickstart-launch-app-cli.md)以開始使用。

* 要監視的 Azure 資源，例如，在本文中實作的資料庫：[如何搭配使用 Spring Data Apache Cassandra API 和 Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
下列程序會同時初始化從 Spring Cloud 執行個體左側導覽窗格中的 [警示]  選項啟動的 [動作群組]  和 [警示]  。 (此程序也可以從 Azure 入口網站的 [監視器概觀]  頁面啟動。) 

從資源群組瀏覽至您的 Spring Cloud 執行個體。 選取左窗格中的 [警示]  ，然後選取 [管理動作]  ：

![入口網站資源群組頁面的螢幕擷取畫面](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>設定動作群組

若要開始初始化新的 [動作群組]  的程序，請選取 [+ 新增動作群組]  。

![入口網站新增動作群組的螢幕擷取畫面](media/alerts-action-groups/action-1.png)

在 [新增動作群組]  頁面上：

 1. 指定 [動作群組名稱]  和 [簡短名稱]  。

 1. 指定 [訂用帳戶]  和 [資源群組]  。

 1. 指定 [動作名稱]  。

 1. 選取 [動作類型]  。  這會在右側開啟另一個窗格，以定義將在啟用時採取的動作。

 1. 使用右窗格中的選項來定義動作。  此案例使用電子郵件通知。

 1. 在右側的動作窗格中，按一下 [確定]  。

 1. 在 [新增動作群組]  對話方塊中，按一下 [確定]  。 

  ![入口網站定義動作的螢幕擷取畫面](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>設定警示 

先前的步驟已建立使用電子郵件的 [動作群組]  。 您也可以使用電話通知、Webhook、Azure 函式等等。  

若要設定**警示**，請瀏覽回 [警示]  頁面，然後按一下 [管理警示規則]  。

  ![入口網站定義警示的螢幕擷取畫面](media/alerts-action-groups/alerts-2.png)

1. 選取警示的 [資源]  。

1. 按一下 [+ 新增警示規則]  。

  ![入口網站新增警示規則的螢幕擷取畫面](media/alerts-action-groups/alerts-3.png)

1. 在 [建立規則]  頁面上，指定 [資源]  、[條件]  和 [動作]  。  從 [動作]  窗格中選取先前定義的 [動作群組]  。

1. 在 [警示詳細資料]  底下，為警示規則命名。

1. 按一下 [建立警示規則]  。

  ![入口網站新增警示規則的螢幕擷取畫面](media/alerts-action-groups/alerts-4.png)

確認新的警示規則已啟用。

  ![入口網站新增警示規則的螢幕擷取畫面](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>後續步驟
* [在 Azure 入口網站中建立和管理動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [動作群組中的 SMS 警示行為](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [教學課程：搭配 Azure Spring Cloud 使用分散式追蹤](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
