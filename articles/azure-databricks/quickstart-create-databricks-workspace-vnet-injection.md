---
title: 在 虛擬網路中建立 Azure Databricks 工作區
description: 本文說明如何將 Azure Databricks 部署至您的虛擬網路。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770517"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>快速入門：在 虛擬網路中建立 Azure Databricks 工作區

本快速入門示範如何在虛擬網路中建立 Azure Databricks 工作區。 您也會建立該工作區內的 Apache Spark 叢集。

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 在 Azure 入口網站中，選取**建立資源** > **網路** > **虛擬網路**。

2. 底下**建立虛擬網路**，套用下列設定： 

    |設定|建議值|說明|
    |-------|---------------|-----------|
    |名稱|databricks-quickstart|選取您的虛擬網路的名稱。|
    |位址空間|10.1.0.0/16|以 CIDR 標記法的虛擬網路的位址範圍。|
    |訂用帳戶|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |資源群組|databricks-quickstart|選取 **新建**並輸入您的帳戶的新資源群組名稱。|
    |位置|\<選取最接近使用者的區域\>|選取地理位置，您可以在其中裝載您的虛擬網路。 使用最靠近您的使用者的位置。|
    |子網路名稱|預設值|選取虛擬網路中的預設子網路的名稱。|
    |子網路位址範圍|10.1.0.0/24|採用 CIDR 標記法的子網路位址範圍。 它必須包含虛擬網路的位址空間。 目前使用中的子網路的位址範圍不能編輯。|

    ![在 Azure 入口網站建立虛擬網路](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 部署完成之後，瀏覽至您的虛擬網路，然後選取**位址空間**下方**設定**。 在方塊中，指出*新增其他位址範圍*，插入`10.179.0.0/16`，然後選取**儲存**。

    ![Azure 虛擬網路位址空間](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

1. 在 Azure 入口網站中，選取**建立資源** > **Analytics** > **Databricks**。

2. 底下**Azure Databricks 服務**，套用下列設定：

    |設定|建議值|描述|
    |-------|---------------|-----------|
    |工作區名稱|databricks-quickstart|選取您的 Azure Databricks 工作區的名稱。|
    |訂用帳戶|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |資源群組|databricks-quickstart|選取您用於虛擬網路的相同資源群組。|
    |位置|\<選取最接近使用者的區域\>|選擇與您的虛擬網路相同的位置。|
    |定價層|選擇標準或進階。|如需有關定價層的詳細資訊，請參閱 < [Databricks 定價頁面](https://azure.microsoft.com/pricing/details/databricks/)。|
    |部署虛擬網路中的 Azure Databricks 工作區|是|此設定可讓您部署虛擬網路中的 Azure Databricks 工作區。|
    |虛擬網路|databricks-quickstart|選取您在上一節中建立的虛擬網路。|
    |公用子網路名稱|public-subnet|使用預設公用子網路名稱。|
    |公用子網路 CIDR 範圍|10.179.64.0/18|此子網路的 CIDR 範圍應該介於 /18 和/26。|
    |私人子網路名稱|private-subnet|使用預設的私人子網路名稱。|
    |私人子網路 CIDR 範圍|10.179.0.0/18|此子網路的 CIDR 範圍應該介於 /18 和/26。|

    ![在 Azure 入口網站建立 Azure Databricks 工作區](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 部署完成之後，瀏覽至 Azure Databricks 的資源。 請注意，停用虛擬網路對等互連。 也請注意在 [概觀] 頁面中的受控的資源群組與資源群組。 

    ![在 Azure 入口網站中的 azure Databricks 概觀](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    受控的資源群組中包含的儲存體帳戶 (DBFS)，背景工作-sg （網路安全性群組），背景工作-vnet （虛擬網路） 的實體位置。 它也是虛擬機器、 磁碟、 IP 位址和網路介面建立所在的位置。 根據預設，鎖定此資源群組不過當叢集已啟動的虛擬網路中，則會建立網路介面的受控的資源群組中的背景工作角色 vnet 與 「 中樞 」 的虛擬網路之間。

    ![Azure Databricks 受控的資源群組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>建立叢集

> [!NOTE]
> 若要使用免費帳戶建立 Azure Databricks 叢集，在建立叢集之前，請先移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。

1. 返回您的 Azure Databricks 服務並選取**啟動工作區**上**概觀**頁面。

2. 選取 **叢集** >  **+ 建立叢集**。 然後建立叢集名稱，例如*databricks 快速入門叢集*，並接受其餘預設設定。 選取 [建立叢集]  。

    ![建立 Azure Databricks 叢集](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 當叢集執行時，返回 Azure 入口網站中的受管理的資源群組。 請注意新的虛擬機器、 磁碟、 IP 位址和網路介面。 每個 IP 位址的公用和私人子網路中建立網路介面。  

    ![在叢集建立後的 azure Databricks 受控的資源群組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. 返回您的 Azure Databricks 工作區，然後選取您建立的叢集。 然後瀏覽至**執行程式**索引標籤上**Spark UI**頁面。 請注意，此驅動程式和執行程式的位址在私人子網路範圍內。 在此範例中，驅動程式是 10.179.0.6，並執行程式是 10.179.0.4 和 10.179.0.5。 您的 IP 位址可能不同。

    ![Azure Databricks Spark UI 執行程式](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>清除資源

在完成本文後，您可以終止叢集。 若要這樣做，請從 Azure Databricks 工作區的左窗格中選取 [叢集]  。 對於您想要終止的叢集，將游標移到 [動作]  資料行底下的省略符號上，然後選取 [終止]  圖示。 這樣會阻止叢集。

如果您不手動終止叢集，叢集將會自動停止，但前提是您已在建立叢集時選取 [在停止活動 \_\_ 分鐘後終止]  核取方塊。 在這種情況下，叢集將會在停止運作達指定時間後自動停止。

如果您不想要重複使用的叢集，您可以刪除您在 Azure 入口網站中建立的資源群組。

## <a name="next-steps"></a>後續步驟

在本文中，您會建立在您部署到虛擬網路的 Azure Databricks 中的 Spark 叢集。 請前往下一篇文章，以了解如何查詢的 SQL Server Linux Docker 容器中的虛擬網路，使用 JDBC 從 Azure Databricks notebook。  

> [!div class="nextstepaction"]
>[查詢的 SQL Server Linux Docker 容器中的虛擬網路，從 Azure Databricks notebook](vnet-injection-sql-server.md)
