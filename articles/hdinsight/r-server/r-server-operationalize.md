---
title: 讓 HDInsight 上的 R Server 能夠運作 - Azure | Microsoft Docs
description: 了解如何讓 Azure HDInsight 上的 R Server 能夠運作。
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 93957b7ee10527039bf2e96cc5470420cdef0651
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>讓 Azure HDInsight 上的 R Server 叢集能夠運作

當您使用 HDInsight 上的 R Server 叢集來完成資料模型之後，您可以讓該模型運作以做出預測。 本文提供如何執行此工作的相關指示。

## <a name="prerequisites"></a>先決條件

* **HDInsight 上的 R Server 叢集**：如需相關指示，請參閱[開始在 HDInsight 上使用 R Server](r-server-get-started.md)。

* **安全殼層 (SSH) 用戶端**：SSH 用戶端可用來從遠端連線至 HDInsight 叢集，並直接在叢集上執行命令。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>使用單一方塊設定來讓 R Server 叢集能夠運作

1. 透過 SSH 連線到邊緣節點。  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    如需如何使用 Azure HDInsight 上的 SSH 相關指示，請參閱[搭配使用 SSH 與 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 變更相關版本的目錄並對 dot net dll 進行 sudo： 

    - 對於 Microsoft R Server 9.1：

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - 對於 Microsoft R Server 9.0：

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. 您會看到可從中選擇的選項。 選擇第一個選項 (如下列螢幕擷取畫面所示) 來**設定要實作的 R Server**。

    ![one box op](./media/r-server-operationalize/admin-util-one-box-1.png)

4. 您現在會看到可選擇來讓 R Server 能夠運作的選項。 從顯示的選項中，輸入 **A** 來選擇第一項。

    ![one box op](./media/r-server-operationalize/admin-util-one-box-2.png)

5. 出現提示時，輸入並重新輸入本機系統管理員使用者的密碼。

6. 您應該會看到意味著作業已成功的輸出。 您也會看見從功能表中選取另一個選項的提示。 選取 E 以返回主功能表。

    ![one box op](./media/r-server-operationalize/admin-util-one-box-3.png)

7. 您也可以選擇性地執行診斷測試來執行診斷檢查，如下所示：

    a. 從主功能表中，選取 **6** 來執行診斷測試。

    ![one box op](./media/r-server-operationalize/diagnostic-1.png)

    b. 從 [診斷測試] 功能表中，選取 **A**。出現提示時，輸入您針對本機系統管理員使用者所提供的密碼。

    ![one box op](./media/r-server-operationalize/diagnostic-2.png)

    c. 確認輸出會顯示整體健康情況是良好的。

    ![one box op](./media/r-server-operationalize/diagnostic-3.png)

    d. 從顯示的功能表選項中，輸入 **E** 以返回主功能表，然後輸入 **8** 以結束管理公用程式。

### <a name="long-delays-when-consuming-web-service-on-spark"></a>在 Spark 上取用 Web 服務時長時間延遲

如果您在 Spark 計算環境中嘗試取用使用 mrsdeploy 函式建立的 Web 服務時，遇到長時間延遲，您可能需要新增一些遺漏的資料夾。 每當使用 mrsdeploy 函式從 Web 服務叫用 Spark 應用程式時，該應用程式會屬於名為 'rserve2' 的使用者。 若要解決此問題：

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


在此階段中，運算化的設定已完成。 現在您可以在 RClient 上使用 `mrsdeploy` 套件來連線至邊緣節點上的實作，並開始使用其功能，像是[遠端執行](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) \(英文\) 和 [Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) \(英文\)。 根據叢集是否設定在虛擬網路上，您可能必須設定透過 SSH 登入的連接埠轉送通道。 下列各節說明如何設定此通道。

### <a name="r-server-cluster-on-virtual-network"></a>虛擬網路上的 R Server 叢集

確定您允許流量通過連接埠 12800 到達邊緣節點。 這樣一來，您就可以使用 Edge 節點連線到實作功能。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


如果 `remoteLogin()` 無法連線到邊緣節點，但您可以透過 SSH 連線到邊緣節點，則必須確認是否已正確設定在連接埠 12800 上允許流量的規則。 如果您持續遇到此問題，您可以藉由設定透過 SSH 的連接埠轉送通道來處理此問題。 如需相關指示，請參閱下一節：

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>R Server 叢集未設定於虛擬網路上

如果您的叢集未設定於 vnet 上，或如果您在透過 vnet 連線時遇到問題，可以使用 SSH 連接埠轉送通道︰

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

SSH 工作階段變為作用中後，來自電腦連接埠 12800 的流量就會透過 SSH 工作階段轉送到邊緣節點的連接埠 12800。 請務必在 `remoteLogin()` 方法中使用 `127.0.0.1:12800`。 這會透過連接埠轉送登入邊緣節點的實作。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>調整 HDInsight 背景工作節點上實作的計算節點

若要調整計算節點，您需要先將背景工作節點解除委任，然後在已解除委任的背景工作節點上設定計算節點。

### <a name="step-1-decommission-the-worker-nodes"></a>步驟 1：將背景工作節點解除委任

R Server 叢集並非透過 YARN 來管理。 如果未將背景工作節點解除委任，YARN Resource Manager 就無法如預期般運作，因為它不會知道伺服器目前所佔用的資源。 為了避免這個狀況，建議您相應放大計算節點之前，將背景工作角色節點解除委任。

請遵循下列步驟來將背景工作節點解除委任：

1. 登入叢集的 Ambari 主控台，然後按一下 [Hosts] \(主機\) 索引標籤。

2. 選取背景工作節點 (以解除委任)。

3. 按一下 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [Hosts] \(主機\) > [Turn ON Maintenance Mode] \(開啟維護模式\)。 例如，在以下映像中，我們選取了要解除委任 wn3 和 wn4。  

   ![解除委任背景工作節點](./media/r-server-operationalize/get-started-operationalization.png)  

* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [DataNode] > 按一下 [Decommission] \(解除委任\)。
* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [NodeManagers] > 按一下 [Decommission] \(解除委任\)。
* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [DataNode] > 按一下 [Stop] \(停止\)。
* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [NodeManagers] > 按一下 [Stop] \(停止\)。
* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [Hosts] \(主機\) > 按一下 [Stop All Components] \(停止所有元件\)。
* 將背景工作節點取消選取，並選取前端節點。
* 選取 [Actions] \(動作\) > [Selected Hosts] \(選取的主機\) > [Hosts] \(主機\) > [Restart All Components] \(重新啟動所有元件\)。

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>步驟 2：在每個已解除委任的背景工作節點上設定計算節點

1. 透過 SSH 連線到每個已解除委任的背景工作角色節點。

2. 使用適用於您所擁有之 R Server 叢集的相關 DLL 來執行管理公用程式。 針對 R Server 9.1，執行下列動作：

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. 輸入 **1** 以選取選項 [Configure R Server for Operationalization] \(設定要實作的 R Server\)。

4. 輸入 **C** 以選取選項 `C. Compute node`。 這會設定背景工作角色節點上的計算節點。

5. 結束系統管理公用程式。

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>步驟 3：在 Web 節點上新增計算節點詳細資料

當所有已解除委任的背景工作節點皆已設定為執行計算節點後，請回到邊緣節點，然後在 R Server Web 節點的設定中新增已解除委任之背景工作節點的 IP 位址：

1. 透過 SSH 連線到邊緣節點。

2. 執行 `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`。

3. 尋找 [Uris] 區段，並新增背景工作節點的 IP 和連接埠詳細資料。

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>後續步驟

* [管理 HDInsight 上的 R Server 叢集](r-server-hdinsight-manage.md)
* [適用於 HDInsight 上 R Server 叢集的計算內容選項](r-server-compute-contexts.md)
* [適用於 HDInsight 上 R Server 叢集的 Azure 儲存體選項](r-server-storage.md)