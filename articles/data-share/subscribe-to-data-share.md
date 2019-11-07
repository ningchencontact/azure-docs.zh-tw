---
title: 教學課程：接受和接收資料 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 接受和接收資料
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499341"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>教學課程：使用 Azure Data Share 接受和接收資料  

在此教學課程中，您將了解如何使用 Azure Data Share 接受資料共用邀請。 您會了解如何接收您所能共用的資料，以及如何啟用定期重新整理間隔以確保您永遠會有所能共用資料的最新快照集。 

> [!div class="checklist"]
> * 如何接受 Azure Data Share 邀請
> * 建立 Azure Data Share 帳戶
> * 指定資料的目的地
> * 建立資料共用訂閱以獲得排定的重新整理

## <a name="prerequisites"></a>必要條件
您必須先佈建一些 Azure 資源 (下面會列出) 才可以接受資料共用邀請。 

請先確定您已完成所有必要條件再接受資料共用邀請。 

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 資料共用邀請：Microsoft Azure 所送來的邀請，主旨標題為「來自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀請」。

### <a name="receive-data-into-a-storage-account"></a>將資料接收到儲存體帳戶中： 

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* 將角色指派新增至儲存體帳戶的權限，其存在於 Microsoft.Authorization/role assignments/write  權限中。 此權限存在於擁有者角色中。 
* Microsoft.DataShare 的資源提供者註冊。 如需有關如何執行此作業的詳細資訊，請參閱 [Azure 資源提供者](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)文件。 

> [!IMPORTANT]
> 若要接受並接收 Azure Data Share，您必須先註冊 Microsoft.DataShare 資源提供者，而且您必須擁有用來接受資料的儲存體帳戶。 請遵循[針對 Azure Data Share 進行疑難排解](data-share-troubleshoot.md)中所述的指示，註冊資料共用資源提供者，並將您自己新增為儲存體帳戶的擁有者。 

### <a name="receive-data-into-a-sql-based-source"></a>將資料接收到 SQL 型來源：

* 資料共用 MSI 用來存取 Azure SQL Database 或 Azure SQL 資料倉儲的權限。 這可以透過下列步驟完成： 
    1. 將自己設定為伺服器的 Azure Active Directory 系統管理員。
    1. 使用 Azure Active Directory 連線到 Azure SQL Database/資料倉儲。
    1. 使用查詢編輯器 (預覽) 來執行下列指令碼，將 Data Share MSI 新增為 db_owner。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
請注意， *<share_acc_name>* 是您 Data Share 帳戶的名稱。 如果您尚未建立 Data Share 帳戶，您可以稍後再回到此先決條件。         

* 用戶端 IP SQL Server 防火牆存取：這可以透過下列步驟完成：1. 瀏覽至 [防火牆與虛擬網路]  。 按一下 [開啟]  切換開關，以允許存取 Azure 服務。 

這些先決條件完成後，您就可以開始將資料接收到您的 SQL Server。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="open-invitation"></a>開啟邀請

檢查收件匣中是否有來自資料提供者的邀請。 邀請會來自 Microsoft Azure，標題為**來自 <yourdataprovider@domain.com> 的 Azure Data Share 邀請**。 記下共用名稱，以確保您可以在有多個邀請的情況下接受正確的共用。 

選取 [檢視邀請]  以查看您在 Azure 中的邀請。 這會帶您前往 [已接收的共用] 檢視。

![邀請](./media/invitations.png "邀請清單") 

選取您想要檢視的共用。 

## <a name="accept-invitation"></a>接受邀請
請確定您已檢閱過所有欄位，包括 [使用規定]  。 如果您同意使用規定，則必須核取該方塊以表示您同意。 

![使用規定](./media/terms-of-use.png "使用規定") 

在 [目標 Data Share 帳戶]  底下，選取要作為 Data Share 部署目的地的訂用帳戶和資源群組。 

針對 [Data Share 帳戶]  欄位，如果您沒有現有的 Data Share 帳戶，請選取 [新建]  。 否則，請選取您想要用來接受資料共用的現有 Data Share 帳戶。 

針對 [已收到的共用名稱]  欄位，您可以保留資料提供者所指定的預設值，也可以為已接收的共用指定新名稱。 

![目標 Data Share 帳戶](./media/target-data-share.png "目標 Data Share 帳戶") 

同意使用規定並指定了共用的位置後，請選取 [接受並設定]  。 如果您選擇此選項，系統會建立共用訂用帳戶，下一個畫面則會要求您選取目標儲存體帳戶以供作為資料的複製目的地。 

![接受選項](./media/accept-options.png "接受選項") 

如果您想要立即接受邀請，再於稍後設定儲存體，請選取 [接受並稍後再設定]  。 此選項可讓您稍後再設定目標儲存體帳戶。 若要稍候再繼續設定儲存體，請參閱[如何設定儲存體帳戶](how-to-configure-mapping.md)頁面，以取得如何繼續設定資料共用的詳細步驟。 

如果您不想要接受邀請，請選取 [拒絕]  。 

## <a name="configure-storage"></a>設定儲存體
在 [目標儲存體設定]  底下，選取您想要用來接收資料的訂用帳戶、資源群組和儲存體帳戶。 

![目標儲存體設定](./media/target-storage-settings.png "目標儲存體") 

若要接收定期重新整理的資料，請務必啟用快照集設定。 請注意，您只會看到一個快照集設定排程 (如果資料提供者已將該設定納入資料共用)。 

![快照集設定](./media/snapshot-settings.png "快照集設定") 

選取 [儲存]  。 

> [!IMPORTANT]
> 如果您要接收 SQL 型資料，而且想要將該資料接收到 SQL 型來源，請瀏覽我們的[設定資料集對應](how-to-configure-mapping.md)操作指南，以了解如何將 SQL Server 設定為資料集的目的地。 

## <a name="trigger-a-snapshot"></a>觸發快照集

您可以在 [已接收的共用] -> [詳細資料] 索引標籤中，藉由選取 [觸發快照集]  來觸發快照集。 在這裡，您可以觸發資料的完整或累加快照集。 如果您是第一次接收來自資料提供者的資料，請選取 [完整複本]。 

![觸發快照集](./media/trigger-snapshot.png "觸發快照集") 

當最後一次執行的狀態為「成功」  時，請開啟儲存體帳戶來檢視已接收的資料。 

若要檢查您所使用的儲存體帳戶，請選取 [資料集]  。 

![取用者資料集](./media/consumer-datasets.png "取用者資料集對應") 

## <a name="view-history"></a>檢視歷程記錄
若要檢視快照集的歷程記錄，請瀏覽至 [已接收的共用] -> [歷程記錄]。 您會在這裡找到過去 60 天所有產生的快照集歷程記錄。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何接受和接收 Azure Data Share。 若要深入了解 Azure Data Share 概念，請繼續閱讀[概念：Azure Data Share 術語](terminology.md)。