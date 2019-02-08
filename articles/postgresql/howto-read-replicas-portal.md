---
title: 在 Azure 入口網站中管理適用於 PostgreSQL 的 Azure 資料庫讀取複本
description: 本文會說明如何在 Azure 入口網站中管理適用於 PostgreSQL 的 Azure 資料庫讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: bf6e9947c21e5b07b2adc99de585c77444447c04
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902648"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>如何在 Azure 入口網站中建立及管理讀取複本

> [!IMPORTANT]
> 讀取複本功能目前為「公開預覽」版。


在本文中，您將了解如何使用 Azure 入口網站在「適用於 PostgreSQL 的 Azure 資料庫」服務中建立與管理讀取複本。 若要深入了解讀取複本，請[閱讀概念文件](concepts-read-replicas.md)。

## <a name="prerequisites"></a>必要條件
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，將會是主要伺服器。

## <a name="prepare-the-master-server"></a>準備主要伺服器
這個主要伺服器準備步驟僅適用於一般用途和記憶體最佳化的伺服器。

**Azure.replication_support** 參數必須在主要伺服器上設定為「複本」。 需要重新啟動伺服器，此參數的變更才會生效。

1. 在 Azure 入口網站中，選取您要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」伺服器。

2. 從左側功能表上選取 [伺服器參數]。

3. 搜尋 **azure.replication_support**。

   ![適用於 PostgreSQL 的 Azure 資料庫 - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. 將 **azure.replication_support** 設定為「複本」。 **儲存**變更。

   ![適用於 PostgreSQL 的 Azure 資料庫 -「複本」和儲存](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. 儲存完成後，您會收到通知。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 儲存通知](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. 重新啟動伺服器，以在儲存之後套用變更。 請參閱[重新啟動文件](howto-restart-server-portal.md)以了解如何重新啟動伺服器。


## <a name="create-a-read-replica"></a>建立讀取複本
您可以使用下列步驟建立讀取複本︰
1.  選取您要作為主要伺服器的現有「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 

2.  選取 [設定] 下方功能表中的 [複寫]。

   如果尚未在一般用途或記憶體最佳化主要伺服器上，將 **azure.replication_support** 設定為「複本」並重新啟動服伺服器，則會顯示一則訊息指示您這麼做。 建立之前先執行這項作業。

3.  選取 [新增複本]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 加入複本](./media/howto-read-replicas-portal/add-replica.png)

4.  輸入複本伺服器的名稱，然後選取 [確定] 確認建立複本。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 命名複本](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 建立複本之後，定價層 (無法變更為基本或從基本變更為其他定價層)、計算世代、vCore、儲存體以及備份保留可以獨立於主要伺服器之外來進行變更。

> [!IMPORTANT]
> 將主要伺服器設定更新為新值之前，複本的設定應該更新為等於或大於的值。 嘗試這麼做，否則會造成錯誤。 這可確保複本伺服器能夠跟上對主要伺服器所做的變更。 


建立複本伺服器後，可從 [複寫] 視窗檢視該伺服器。

![適用於 PostgreSQL 的 Azure 資料庫 - 新增複本](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>停止複寫

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦停止主要與複本伺服器之間進行複寫，便無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

若要從 Azure 入口網站停止主要與複本伺服器之間的複寫，請使用下列步驟：
1.  在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2.  選取 [設定] 下方功能表中的 [複寫]。

3.  選取您想要停止複寫的複本伺服器。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 選取複本](./media/howto-read-replicas-portal/select-replica.png)
 
4.  選取 [停止複寫]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 選取停止複寫](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  按一下 [確定] 確認您要停止複寫。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 確認停止複寫](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>刪除主要伺服器

> [!IMPORTANT]
> 刪除主要伺服器會對對所有複本伺服器停止複寫。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。
刪除主要伺服器的步驟與刪除獨立的適用於 PostgreSQL 的 Azure 資料庫的步驟相同。 若要從 Azure 入口網站刪除伺服器，請執行下列作業：

1.  在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2.  從 [概觀] 中選取 [刪除]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 刪除伺服器](./media/howto-read-replicas-portal/delete-server.png)
 
3.  輸入主要伺服器的名稱，然後選取 [刪除] 以確認刪除主要伺服器。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 確認刪除](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>刪除複本伺服器
若要刪除讀取複本，可以遵循與刪除上述主要伺服器的相同步驟。 首先，開啟複本的 [概觀] 頁面，然後選取 [刪除]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 刪除複本](./media/howto-read-replicas-portal/delete-replica.png)
 
或者，您可以從 [複寫] 視窗加以刪除。
1.  在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的主要伺服器。

2.  選取 [設定] 下方功能表中的 [複寫]。

3.  選取您想要刪除的複本伺服器。 

   ![適用於 PostgreSQL 的 Azure 資料庫 - 選取複本](./media/howto-read-replicas-portal/select-replica.png)
 
4.  選取 [刪除複本]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 選取刪除複本](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  輸入複本名稱，然後選取 [刪除] 以確認刪除複本。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 確認刪除複本](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>監視複本
### <a name="max-lag-across-replicas"></a>複本之間的最大延隔時間
**複本之間的最大延隔時間**計量會顯示主要伺服器和最大延隔複本之間的延隔時間。 

1.  在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的**主要**伺服器。

2.  選取 [計量]。 在 [計量] 視窗中，選取 [複本之間的最大延隔時間]。

    ![適用於 PostgreSQL 的 Azure 資料庫 - 複本之間的最大延隔遲時間](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  選取 [最大值] 作為彙總。 

### <a name="replica-lag"></a>複本延隔時間
**複本延隔時間**計量顯示在自最後一次重新執行此複本上交易的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

1.  在 Azure 入口網站中，選取適用於 PostgreSQL 的 Azure 資料庫的**複本**伺服器。

2.  選取 [計量]。 在 [計量] 視窗中，選取 [複本延隔時間]。

   ![適用於 PostgreSQL 的 Azure 資料庫 - 監控複本延遲](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  選取 [最大值] 作為彙總。 
 
## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。