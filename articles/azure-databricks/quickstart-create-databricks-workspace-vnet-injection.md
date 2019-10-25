---
title: 在虛擬網路中建立 Azure Databricks 工作區
description: 本文說明如何將 Azure Databricks 部署至您的虛擬網路。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 69afe2aab3c10707f7160d727b970ad73d59a952
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791548"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>快速入門：在虛擬網路中建立 Azure Databricks 工作區

本快速入門說明如何在虛擬網路中建立 Azure Databricks 的工作區。 您也會在該工作區中建立 Apache Spark 叢集。

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

> [!Note]
> 本教學課程不適用 **Azure 免費試用版的訂用帳戶**。
> 如果您有免費帳戶，請移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。 然後，為您所在區域的 vCPU [移除消費限制](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)並[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。 當您建立 Azure Databricks 工作區時，您可以選取 [試用版 (進階 - 14 天的免費 DBU)] 定價層，讓工作區可免費存取進階 Azure Databricks DBU 14 天。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 在 Azure 入口網站中，選取 **建立資源** > **網路** > **虛擬網路**。

2. 在 [**建立虛擬網路**] 下，套用下列設定： 

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |Name|databricks-快速入門|選取虛擬網路的 [名稱]。|
    |位址空間|10.1.0.0/16|CIDR 標記法中的虛擬網路位址範圍。|
    |Subscription|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |Resource group|databricks-快速入門|選取 **[新建]** ，然後為您的帳戶輸入新的資源組名。|
    |Location|\<選取最接近使用者的區域\>|選取您可以裝載虛擬網路的地理位置。 使用最靠近您的使用者的位置。|
    |子網路名稱|預設值|選取虛擬網路中預設子網的 [名稱]。|
    |子網路位址範圍|10.1.0.0/24|採用 CIDR 標記法的子網路位址範圍。 它必須包含在虛擬網路的位址空間內。 無法編輯使用中的子網位址範圍。|

    ![在 Azure 入口網站上建立虛擬網路](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 部署完成後，流覽至您的虛擬網路，然後選取 [**設定**] 底下的 [**位址空間**]。 在顯示 [*新增其他位址範圍*] 的方塊中，插入 `10.179.0.0/16` 並選取 [**儲存**]。

    ![Azure 虛擬網路位址空間](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

1. 在 [Azure 入口網站中，選取 [**建立資源**] > **分析** > **Databricks**]。

2. 在 [ **Azure Databricks 服務**] 底下，套用下列設定：

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |工作區名稱|databricks-快速入門|選取您 Azure Databricks 工作區的 [名稱]。|
    |Subscription|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |Resource group|databricks-快速入門|選取您用於虛擬網路的相同資源群組。|
    |Location|\<選取最接近使用者的區域\>|選擇與您的虛擬網路相同的位置。|
    |價格層次|選擇 [Standard] 或 [Premium]。|如需定價層的詳細資訊，請參閱[Databricks 定價頁面](https://azure.microsoft.com/pricing/details/databricks/)。|
    |在您的虛擬網路中部署 Azure Databricks 工作區|是|此設定可讓您在虛擬網路中部署 Azure Databricks 工作區。|
    |虛擬網路|databricks-快速入門|選取您在上一節中建立的虛擬網路。|
    |公用子網名稱|公用-子網|使用預設的公用子網名稱。|
    |公用子網 CIDR 範圍|10.179.64.0/18|此子網的 CIDR 範圍應介於/18 到/26 之間。|
    |私人子網名稱|私用-子網|使用預設的私人子網名稱。|
    |私人子網 CIDR 範圍|10.179.0.0/18|此子網的 CIDR 範圍應介於/18 到/26 之間。|

    ![在 Azure 入口網站上建立 Azure Databricks 工作區](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 部署完成後，流覽至 Azure Databricks 資源。 請注意，虛擬網路對等互連已停用。 另請注意 [總覽] 頁面中的 [資源群組] 和 [受控資源群組]。 

    ![Azure 入口網站中的 Azure Databricks 總覽](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    受控資源群組包含儲存體帳戶（DBFS）、工作者 sg （網路安全性群組）、工作者-vnet （虛擬網路）的實體位置。 它也是將建立虛擬機器、磁片、IP 位址和網路介面的位置。 預設會鎖定此資源群組;不過，在虛擬網路中啟動叢集時，會在受控資源群組和「中樞」虛擬網路的背景工作-vnet 之間建立網路介面。

    ![Azure Databricks 受控資源群組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>建立叢集

> [!NOTE]
> 若要使用免費帳戶建立 Azure Databricks 叢集，在建立叢集之前，請先移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。

1. 返回您的 Azure Databricks 服務，然後在 [**總覽**] 頁面上選取 [**啟動工作區**]。

2. 選取 **[** 叢集] > [ **+ 建立**叢集]。 然後建立叢集名稱，例如*databricks-快速入門-* 叢集，並接受其餘的預設設定。 選取 [建立叢集]。

    ![建立 Azure Databricks 叢集](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 叢集執行之後，請回到 Azure 入口網站中的受控資源群組。 請注意新的虛擬機器、磁片、IP 位址和網路介面。 在每個具有 IP 位址的公用和私人子網中，都會建立一個網路介面。  

    ![叢集建立後 Azure Databricks 受控資源群組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. 返回您的 Azure Databricks 工作區，然後選取您所建立的叢集。 然後流覽至**SPARK UI**頁面上的 [**執行] 索引**標籤。 請注意，驅動程式和執行程式的位址是在私人子網範圍內。 在此範例中，驅動程式為10.179.0.6，而執行程式為10.179.0.4 和10.179.0.5。 您的 IP 位址可能不同。

    ![Azure Databricks Spark UI 執行](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>清除資源

在完成本文後，您可以終止叢集。 若要這樣做，請從 Azure Databricks 工作區的左窗格中選取 [叢集]。 對於您想要終止的叢集，將游標移到 [動作] 資料行底下的省略符號上，然後選取 [終止] 圖示。 這會停止叢集。

如果您不手動終止叢集，叢集將會自動停止，但前提是您已在建立叢集時選取 [在停止活動 \_\_ 分鐘後終止] 核取方塊。 在這種情況下，叢集將會在停止運作達指定時間後自動停止。

如果您不想要重複使用叢集，可以刪除您在 Azure 入口網站中建立的資源群組。

## <a name="next-steps"></a>後續步驟

在本文中，您已在部署至虛擬網路的 Azure Databricks 中建立 Spark 叢集。 前往下一篇文章，以瞭解如何使用 Azure Databricks 筆記本中的 JDBC 查詢虛擬網路中的 SQL Server Linux Docker 容器。  

> [!div class="nextstepaction"]
>[從 Azure Databricks 筆記本查詢虛擬網路中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
