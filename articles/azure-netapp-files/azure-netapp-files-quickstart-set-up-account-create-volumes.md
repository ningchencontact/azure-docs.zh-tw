---
title: 設定 Azure NetApp 檔案並建立磁碟區 |Microsoft Docs
description: 說明如何快速設定 Azure NetApp 檔案，並建立磁碟區。
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
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 363589116cc7b936358ce0b16cbaba9a42c66234
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078069"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>設定 Azure NetApp Files 並建立磁碟區 

本文說明如何快速設定 Azure NetApp 檔案，並建立磁碟區。 

## <a name="before-you-begin"></a>開始之前 

您必須加入「公開預覽計畫」，並列入可存取 Microsoft.NetApp Azure 資源提供者的允許清單中。 如需有關加入「公開預覽」方案的詳細資訊，請參閱 [Azure NetApp Files 公開預覽註冊頁面](https://aka.ms/nfspublicpreview)。 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>註冊 Azure 的 NetApp 檔案和 NetApp 資源提供者

1. 從 Azure 入口網站中，按一下右上角的 Azure Cloud Shell 圖示。

      ![Azure Cloud Shell 圖示](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 指定訂用帳戶已列入允許清單適用於 Azure NetApp 檔案：
    
        az account set --subscription <subscriptionId>

3. 註冊 Azure 資源提供者： 
    
        az provider register --namespace Microsoft.NetApp --wait  

    註冊程序可能需要一些時間才能完成。

## <a name="create-a-netapp-account"></a>建立 NetApp 帳戶

1. 在 Azure 入口網站的 [搜尋] 方塊中輸入**Azure NetApp 檔案**，然後選取**Azure NetApp 檔案 （預覽）** 從出現的清單。

      ![選取 Azure NetApp 檔案](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. 按一下 [+ 新增] 以建立新的 NetApp 帳戶。

     ![建立新的 NetApp 帳戶](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. 在 [新的 NetApp 帳戶] 視窗中，提供下列資訊： 
   1. 請輸入**myaccount1**帳戶名稱。 
   2. 選取您的訂用帳戶。
   3. 選取 **新建**來建立新的資源群組。 請輸入**myRG1**做為資源群組名稱。 按一下 [確定]。 
   4. 選取您的帳戶位置。  

      ![新的 NetApp 帳戶視窗](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![資源群組 視窗](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. 按一下 **建立**建立新的 NetApp 帳戶。

## <a name="set-up-a-capacity-pool"></a>設定容量集區

1. 從 Azure NetApp 檔案管理] 刀鋒視窗中，選取 [NetApp 帳戶 (**myaccount1**)。

    ![選取 NetApp 帳戶](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. 從您的 NetApp 帳戶的 Azure NetApp 檔案管理刀鋒視窗，按一下**容量集區**。

    ![按一下 容量集區](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. 按一下  **+ 新增集區**。 

    ![按一下 新增集區](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. 提供的容量集區的資訊： 
    1. 請輸入**mypool1**與集區名稱。
    2. 選取  **Premium**服務等級。 
    3. 指定**4 (TiB)** 為集區大小。 

5. 按一下 [確定]。

## <a name="create-a-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的磁碟區

1. 從您的 NetApp 帳戶的 Azure NetApp 檔案管理刀鋒視窗，按一下**磁碟區**。

    ![按一下 磁碟區](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. 按一下 [+ 新增磁碟區]。

    ![按一下 新增磁碟區](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. 在 [建立磁碟區] 視窗中，提供磁碟區的資訊： 
   1. 請輸入**myvol1**做為磁碟區名稱。 
   2. 請輸入**myfilepath1**作為檔案路徑是用來建立磁碟區匯出路徑。
   3. 選取您的容量集區 (**mypool1**)。
   4. 使用預設值的配額。 
   5. 在 虛擬網路，按一下**新建**來建立新的 Azure 虛擬網路 (Vnet)。  然後填入下列資訊：
       * 請輸入**myvnet1**做為 Vnet 名稱。
       * 指定的位址空間，例如 10.7.0.0/16。
       * 請輸入**myANFsubnet**做為子網路名稱。
       * 指定子網路位址範圍，例如 10.7.0.0/24。
       * 選取  **Microsoft.NetApp/volumes**子網路的委派。
       * 按一下 **確定**來建立 Vnet。
   6. 在 子網路，選取新建立的 Vnet (**myvnet1**) 委派的子網路。

      ![建立磁碟區 視窗](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![建立虛擬網路視窗](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. 按一下 [檢閱 + 建立]。

    ![檢閱並建立視窗](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. 檢閱資訊的磁碟區，然後按一下 **建立**。  
    建立的磁碟區會出現在 [磁碟區] 刀鋒視窗中。

    ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>後續步驟  

* [了解 Azure NetApp Files 的儲存體階層](azure-netapp-files-understand-storage-hierarchy.md)
* [使用 Azure NetApp 檔案來管理磁碟區](azure-netapp-files-manage-volumes.md) 
