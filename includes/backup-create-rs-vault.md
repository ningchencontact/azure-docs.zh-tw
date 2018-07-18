---
title: 包含檔案
description: 包含檔案
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730493"
---
## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫
復原服務保存庫是一個實體，會儲存歷來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。
2. 在左側功能表中，選取 [所有服務]。

    ![在主功能表中選擇 [所有服務] 選項](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. 在 [所有服務] 對話方塊中，鍵入「復原服務」。 當您開始輸入時，您的輸入會篩選資源清單。 當您看到 [復原服務保存庫] 時，請選取該選項。

    ![在 [所有服務] 對話方塊中，輸入 Recovery Services](./media/backup-create-rs-vault/all-services.png) <br/>

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。
4. 在 [復原服務保存庫] 功能表上，選取 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    [復原服務保存庫] 功能表隨即開啟。 該窗格會提示您提供 [名稱]、[訂用帳戶]、[資源群組] 及 [位置] 的資訊。

    ![[復原服務保存庫] 窗格](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. 在 [名稱] 中，輸入易記名稱來識別保存庫。 該名稱必須是 Azure 訂用帳戶中唯一的名稱。 輸入的名稱至少需包含兩個字元，但不能超過 50 個字元。 名稱必須以字母開頭，且只能包含字母、數字和連字號。
6. 針對 [訂用帳戶]，請選擇您要使用的訂用帳戶。 如果您僅為一個訂用帳戶的成員，就會出現該名稱。 如果您不確定要使用哪個訂用帳戶，則請使用預設 (或建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
7. 針對 [資源群組]，您可以使用現有的資源群組，或建立一個新的群組。 若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]﹐然後按下拉式功能表。 若要建立新的資源群組，請選取 [新建]，並指定名稱。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/resource-group-overview.md)。
8. 針對 [位置]，選取保存庫的地理區域。 如果您要建立保存庫來保護虛擬機器，則保存庫「必須」與虛擬機器位於相同區域。

   > [!IMPORTANT]
   > 如果您不確定 VM 的所在位置，請關閉保存庫建立對話方塊，並移至入口網站中的虛擬機器清單。 如果您在多個區域中有虛擬機器，請在每個區域中建立復原服務保存庫。 請先在第一個位置建立保存庫，再進入下一個位置。 不需要指定用來儲存備份資料的儲存體帳戶。 復原服務保存庫和 Azure 備份服務會自動處理該事宜。
   >
   >

9. 當您準備好建立復原服務保存庫時，請按一下 [建立]。

    ![備份保存庫的清單](./media/backup-create-rs-vault/click-create-button.png)

    要等復原服務保存庫建立好，可能需要一些時間。 請監視 [通知] 區段中的狀態通知 (入口網站右上方區域)。 保存庫建立之後，就會出現在 [復原服務保存庫] 的清單中。 如果您還是沒有看到保存庫，請按一下 [重新整理]。

     ![備份保存庫的清單](./media/backup-create-rs-vault/refresh-button.png)
