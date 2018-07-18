---
title: 設定 Azure Blockchain Workbench SQL DB 防火牆
description: 了解如何設定 Azure Blockchain Workbench SQL DB 防火牆。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074944"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>設定 Azure Blockchain Workbench 資料庫防火牆

本文顯示如何使用 Azure 入口網站設定防火牆規則。 防火牆規則可讓外部用戶端或應用程式連線至 Azure Blockchain Workbench 資料庫。

## <a name="connect-to-the-blockchain-workbench-database"></a>連線至 Blockchain Workbench 資料庫

若要連線至您要設定規則的資料庫：

1. 使用具有 Azure Blockchain Workbench 資源**擁有者**權限的帳戶登入 Azure 入口網站。
2. 在左側導覽窗格中，選取 [資源群組]。
3. 為 Blockchain Workbench 部署選擇資源群組的名稱。
4. 選取 [類型] 排序資源清單，然後選擇您的 **SQL 伺服器**。
5. 下列螢幕擷取畫面中的資源清單範例顯示兩個資料庫：*master* 與 *lsgn-sdk*。 您可以在 *lsgn-sdk* 上設定防火牆規則。

![列出 Blockchain Workbench 資源](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>建立資料庫防火牆規則

若要建立防火牆規則：

1. 選擇對 "lsgn-sdk" 資料庫的連結。
2. 在功能表列上，選取 [設定伺服器防火牆]。

   ![設定伺服器防火牆](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. 若要為您的組織建立規則：

   * 輸入 [規則名稱]
   * 輸入位址範圍其 [起始 IP] 的 IP 位址
   * 輸入位址範圍其 [結束 IP] 的 IP 位址

   ![建立防火牆規則](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > 如果您只想要新增您電腦的 IP 位址，請選擇 [+ 新增用戶端 IP]。
        
1. 若要儲存您的防火牆設定，請選取 [儲存]。
2. 透過從應用程式或工具連線，測試您為資料庫設定的 IP 位址範圍。 例如，SQL Server Management Studio。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的資料庫檢視](blockchain-workbench-database-views.md)