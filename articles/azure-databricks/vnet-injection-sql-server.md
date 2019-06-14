---
title: 查詢的 SQL Server Linux Docker 容器中的虛擬網路，從 Azure Databricks notebook
description: 本文說明如何將 Azure Databricks 部署至您的虛擬網路，也就是 VNet 插入式攻擊。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770541"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>教學課程：查詢的 SQL Server Linux Docker 容器中的虛擬網路，從 Azure Databricks notebook

本教學課程將教導您如何將 Azure Databricks 使用的 SQL Server Linux Docker 容器中的虛擬網路。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 將 Azure Databricks 工作區部署到虛擬網路
> * 在公用網路中安裝 Linux 虛擬機器
> * 安裝 Docker
> * Microsoft SQL Server 安裝於 Linux docker 容器
> * 查詢從 Databricks notebook 中使用 JDBC 的 SQL Server

## <a name="prerequisites"></a>必要條件

* 建立[Databricks 工作區中的虛擬網路](quickstart-create-databricks-workspace-vnet-injection.md)。

* 安裝[Windows 的 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。

* 下載 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="create-a-linux-virtual-machine"></a>建立 Linux 虛擬機器

1. 在 Azure 入口網站中，選取 圖示**虛擬機器**。 然後，選取 **+ 新增**。

    ![加入新的 Azure 虛擬機器](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 在 **基本概念**索引標籤上，選擇 Ubuntu Server 16.04 LTS。 將 VM 大小變更為 B1ms，其中具有一個 VCPU 和 2 GB 的 RAM。 Linux SQL Server Docker 容器的最低需求是 2 GB。 使用者名稱和密碼，請選擇 [以系統管理員]。

    ![新的虛擬機器組態的 [基本] 索引標籤](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. 瀏覽至**網路** 索引標籤。選擇虛擬網路和公用子網路，其中包含您的 Azure Databricks 叢集。 選取 **檢閱 + 建立**，然後**建立**來部署虛擬機器。

    ![新的虛擬機器組態的 [網路] 索引標籤](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 部署完成後，瀏覽至虛擬機器。 請注意公用 IP 位址和虛擬網路/子網路中**概觀**。 選取**公用 IP 位址**

    ![虛擬機器概觀](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. 變更**指派**要**靜態**，然後輸入**DNS 名稱標籤**。 選取 **儲存**，並重新啟動虛擬機器。

    ![公用 IP 位址組態](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. 選取  **Networking**索引標籤下**設定**。 請注意，在 Azure Databricks 部署期間建立的網路安全性群組與虛擬機器相關聯。 選取 **新增輸入連接埠規則**。

7. 新增規則以開啟 SSH 連接埠 22。 套用下列設定：
    
    |設定|建議值|描述|
    |-------|---------------|-----------|
    |source|IP 位址|IP 位址指定連入流量來自特定來源 IP 位址將允許或拒絕此規則。|
    |來源 IP 位址|< 您的公用 ip\>|輸入您的公用 IP 位址。 您可以找到您的公用 IP 位址，請造訪[bing.com](https://www.bing.com/)並搜尋 **「 我的 IP 」** 。|
    |來源連接埠範圍|*|允許從任何連接埠的流量。|
    |目的地|IP 位址|IP 位址會指定該特定來源 IP 位址將允許或拒絕此規則的連出流量。|
    |目的地 IP 位址|< 您的 vm 公用 ip\>|輸入您的虛擬機器的公用 IP 位址。 您可以找到這**概觀**您的虛擬機器的頁面。|
    |目的地連接埠範圍|22|開啟 SSH 連接埠 22。|
    |優先順序|290|指定規則的優先順序。|
    |Name|ssh-databricks-tutorial-vm|指定規則的名稱。|


    ![新增連接埠 22 的輸入的安全性規則](./media/vnet-injection-sql-server/open-port.png)

8. 新增規則以開啟 sql 連接埠 1433年，使用下列設定：

    |設定|建議值|描述|
    |-------|---------------|-----------|
    |source|IP 位址|IP 位址指定連入流量來自特定來源 IP 位址將允許或拒絕此規則。|
    |來源 IP 位址|10.179.0.0/16|輸入您的虛擬網路的位址範圍。|
    |來源連接埠範圍|*|允許從任何連接埠的流量。|
    |目的地|IP 位址|IP 位址會指定該特定來源 IP 位址將允許或拒絕此規則的連出流量。|
    |目的地 IP 位址|< 您的 vm 公用 ip\>|輸入您的虛擬機器的公用 IP 位址。 您可以找到這**概觀**您的虛擬機器的頁面。|
    |目的地連接埠範圍|1433|開啟 SQL Server 連接埠 22。|
    |優先順序|300|指定規則的優先順序。|
    |Name|sql-databricks-tutorial-vm|指定規則的名稱。|

    ![新增通訊埠 1433年的輸入的安全性規則](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>在 Docker 容器中執行 SQL Server

1. 開啟[Ubuntu 的 Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)，或任何其他工具，可讓您透過 ssh 連線到虛擬機器。 瀏覽至您的虛擬機器，在 Azure 入口網站，然後選取**Connect**取得連線所需的 SSH 命令。

    ![連線至虛擬機器](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. 在您的 Ubuntu 終端機中輸入命令，並輸入您設定虛擬機器時，您建立的系統管理員密碼。

    ![Ubuntu 終端機 SSH 登入](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 使用下列命令，在虛擬機器上安裝 Docker。

    ```bash
    sudo apt-get install docker.io
    ```

    確認安裝 Docker，使用下列命令：

    ```bash
    sudo docker --version
    ```

4. 安裝映像。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    請檢查映像。

    ```bash
    sudo docker images
    ```

5. 執行從映像的容器。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    確認容器正在執行。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

1. 開啟 SQL Server Management Studio 並連接至使用伺服器名稱和 SQL 驗證的伺服器。 登入使用者名稱**SA** ，密碼是在 Docker 命令中設定的密碼。 範例命令中的密碼是`Password1234`。

    ![連接到 SQL Server 使用 SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. 您已成功連線之後，選取**新的查詢**並輸入下列程式碼片段來建立資料庫時，資料表，然後在資料表中插入一些記錄。

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![若要建立 SQL Server 資料庫的查詢](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>查詢 SQL Server，從 Azure Databricks

1. 瀏覽至您的 Azure Databricks 工作區，並確認您建立的叢集，做為必要條件的一部分。 然後，選取**建立的 Notebook**。 提供 notebook 的名稱，然後選取*Python*作為語言，然後選取您建立的叢集。

    ![Databricks notebook 的新設定](./media/vnet-injection-sql-server/create-notebook.png)

2. 您可以使用下列命令來 ping SQL Server 虛擬機器的內部 IP 位址。 此 ping 應該會成功。 如果沒有，請確認容器正在執行，並檢閱網路安全性群組 (NSG) 設定。

    ```python
    %sh
    ping 10.179.64.4
    ```

    您也可以使用 nslookup 命令，以檢閱。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. 一旦您已成功 ping 到 SQL Server，您可以查詢資料庫和資料表。 執行下列 python 程式碼：

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、Azure Databricks 工作區和所有相關資源。 刪除作業可避免不必要的計費。 如果您計劃在未來使用 Azure Databricks 工作區，您可以停止叢集且稍後重新啟動。 如果您不再繼續使用此 Azure Databricks 工作區，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表，按一下 [資源群組]  ，然後按一下您所建立的資源群組名稱。

2. 在資源群組頁面上，選取 [刪除]  ，在文字方塊中輸入要刪除的資源名稱，然後再次選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何擷取、 轉換和載入使用 Azure Databricks 的資料。
> [!div class="nextstepaction"]
> [教學課程：使用 Azure Databrick 擷取、轉換和載入資料](databricks-extract-load-sql-data-warehouse.md)
