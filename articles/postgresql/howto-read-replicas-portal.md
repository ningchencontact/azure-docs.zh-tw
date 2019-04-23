---
title: 從 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫讀取複本
description: 了解如何從 Azure 入口網站管理適用於 PostgreSQL 的 Azure 資料庫讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787658"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>從 Azure 入口網站建立及管理讀取複本

在本文中，您將了解如何透過 Azure 入口網站，在「適用於 PostgreSQL 的 Azure 資料庫」中建立與管理讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。


## <a name="prerequisites"></a>必要條件
使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)作為主要伺服器。

## <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。 主要伺服器已準備好進行複寫，藉由設定 azure.replication_support 參數。 變更複寫參數時，重新啟動伺服器時需要，變更才會生效。 在 Azure 入口網站中，這兩個步驟由單一按鈕，封裝**啟用複寫支援**。

1. 在 Azure 入口網站中，選取要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

2. 伺服器資訊看板上底下**設定**，選取**複寫**。

3. 選取 **啟用複寫支援**。 

   ![啟用複寫支援](./media/howto-read-replicas-portal/enable-replication-support.png)

4. 確認您想要啟用複寫支援。 這項作業會重新啟動主要伺服器。 

   ![確認 啟用複寫支援](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. 作業完成後，您會收到兩個的 Azure 入口網站通知。 沒有一個通知來更新伺服器參數。 還有另一個通知之後立即重新啟動伺服器。

   ![成功的通知-可讓](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. 重新整理 Azure 入口網站頁面，並更新 [複寫] 工具列。 您現在可以建立此伺服器的唯讀的複本。

   ![已更新的工具列](./media/howto-read-replicas-portal/updated-toolbar.png)
   
啟用複寫支援為每一部主要伺服器一次作業。 A**停用複寫支援**按鈕可讓您方便參考。 除非您確定您將永遠不會建立這個主要伺服器上的複本，我們不建議停用複寫支援。 您的主要伺服器有現有的複本時，您無法停用複寫支援。


## <a name="create-a-read-replica"></a>建立讀取複本
若要建立讀取複本，請遵循下列步驟：

1. 選取要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」。 

2. 伺服器資訊看板上底下**設定**，選取**複寫**。

3. 選取 [新增複本]。

   ![新增複本](./media/howto-read-replicas-portal/add-replica.png)

4. 輸入讀取複本的名稱。 選取 [確定] 來確認建立複本。

   ![複本的名稱](./media/howto-read-replicas-portal/name-replica.png) 

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

建立讀取複本之後，可從 [複寫] 視窗檢視該複本：

![在 [複寫] 視窗中檢視新複本](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>停止複寫
您可以停止主要伺服器與讀取複本之間的複寫。

> [!IMPORTANT]
> 停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

若要從 Azure 入口網站停止主要與讀取複本之間的複寫，請遵循下列步驟：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 在伺服器功能表的 [設定] 下方，選取 [複寫]。

3. 選取要停止複寫的複本伺服器。

   ![選取複本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 選取 [停止複寫]。

   ![選取 [停止複寫]](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. 選取 [確定] 以停止複寫。

   ![確認停止複寫](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>刪除主要複本
若要刪除主要伺服器，所用步驟與刪除獨立的「適用於 PostgreSQL 的 Azure 資料庫」相同。 

> [!IMPORTANT]
> 刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

若要從 Azure 入口網站刪除伺服器，請遵循下列步驟：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 開啟該伺服器的**概觀**頁面。 選取 [刪除] 。

   ![在伺服器的 [概觀] 頁面中，選取要刪除主要伺服器](./media/howto-read-replicas-portal/delete-server.png)
 
3. 輸入要刪除的主要伺服器名稱。 選取 [刪除] 以確認刪除主要伺服器。

   ![確認刪除主要伺服器](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>刪除複本伺服器
刪除讀取複本的方式類似於刪除主要伺服器。

- 在 Azure 入口網站中，開啟讀取複本的 [概觀] 頁面。 選取 [刪除] 。

   ![在複本的概觀頁面上，選取要刪除複本](./media/howto-read-replicas-portal/delete-replica.png)
 
您也可以透過下列步驟，從 [複寫] 視窗中刪除讀取複本：

1. 在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2. 在伺服器功能表的 [設定] 下方，選取 [複寫]。

3. 選取要刪除的讀取複本。

   ![選取要刪除的複本](./media/howto-read-replicas-portal/select-replica.png)
 
4. 選取 [刪除複本]。

   ![選取 [刪除複本]](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. 輸入要刪除的複本名稱。 選取 [刪除] 以確認刪除複本。

   ![確認刪除複本](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>監視複本
有兩個計量可用來監視讀取複本。

### <a name="max-lag-across-replicas-metric"></a>「複本之間的最大延隔時間」計量
[複本之間的最大延隔時間] 計量會顯示主要伺服器和最大延隔複本之間的延隔 (位元組)。 

1.  在 Azure 入口網站中，選取「適用於 PostgreSQL 的 Azure 資料庫」的主要伺服器。

2.  選取 [計量]。 在 [計量] 視窗中，選取 [複本之間的最大延隔時間]。

    ![監視複本之間的最大延隔時間](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  選取 [最大值] 作為 [彙總]。


### <a name="replica-lag-metric"></a>「複本延隔時間」計量
**複本延隔時間**計量顯示在自最後一次重新執行複本上交易的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

1. 在 Azure 入口網站中，選取「適用於 PostgreSQL 的 Azure 資料庫」讀取複本。

2. 選取 [計量]。 在 [計量] 視窗中，選取 [複本延隔時間]。

   ![監視複本延隔時間](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. 選取 [最大值] 作為 [彙總]。 
 
## <a name="next-steps"></a>後續步驟
深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。