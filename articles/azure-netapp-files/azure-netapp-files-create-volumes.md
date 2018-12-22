---
title: 建立適用於 Azure NetApp Files 的磁碟區 | Microsoft Docs
description: 說明如何建立適用於 Azure NetApp Files 的磁碟區。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: 8fc76c33055f7131444a073d2f8560e136c0701d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413236"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的磁碟區

磁碟區的容量耗用量是根據其集區的佈建容量進行計算。  您可以在容量集區中建立多個磁碟區，但是磁碟區的總容量耗用量不能超過集區大小。 

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>步驟 
1.  從 [管理容量集區] 刀鋒視窗按一下 [磁碟區] 刀鋒視窗。 
2.  按一下 [+ 新增磁碟區] 以建立磁碟區。  
    [新增磁碟區] 視窗隨即出現。

3.  在 [新增磁碟區] 視窗中，按一下 [建立]，並提供下列欄位的資訊：   
    * **名稱**      
        為您要建立的磁碟區指定名稱。   

        名稱在資源群組內必須是唯一的。 長度至少必須有三個字元。  可以使用任何英數字元。

    * **檔案路徑**  
        指定要用於建立新磁碟區匯出路徑的檔案路徑。 匯出路徑會用來掛接和存取磁碟區。   
     
        檔案路徑名稱只能包含字母、數字和連字號 ("-")。 長度必須介於 16 到 40 個字元之間。  

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額] 欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (Vnet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您也可以透過 Express Route 從內部部署網路存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes] 以委派 Azure NetApp Files 的子網路。    
 
        ![新增磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  按一下 [確定]。 
 
磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="next-steps"></a>後續步驟  
* [設定磁碟區的匯出原則 (選用)](azure-netapp-files-configure-export-policy.md)
* [了解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

