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
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: b-juche
ms.openlocfilehash: 657bacc153b5721d5a9f34792eaf4796cb477755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808875"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>建立適用於 Azure NetApp Files 的磁碟區

每個容量集區最多可以有 500 個磁碟區。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 Azure 的 NetApp 檔案支援 NFS 和 SMBv3 磁碟區。 

## <a name="before-you-begin"></a>開始之前 
您必須已經設定容量集區。   
[設定容量集區](azure-netapp-files-set-up-capacity-pool.md)   
子網路必須委派至 Azure NetApp Files。  
[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>建立 NFS 磁碟區

1.  按一下 **磁碟區**刀鋒視窗中的容量集區刀鋒視窗中。 

    ![瀏覽至 磁碟區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  按一下 [+ 新增磁碟區]  以建立磁碟區。  
    建立磁碟區 視窗隨即出現。

3.  在 [建立磁碟區] 視窗中，按一下**建立**，並提供下列欄位的資訊：   
    * **磁碟區名稱**      
        為您要建立的磁碟區指定名稱。   

        磁碟區名稱必須是每個容量集區中的唯一的。 長度至少必須有三個字元。 您可以使用任何英數字元。

    * **容量集區**  
        指定您想要建立的磁碟區容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額]  欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (Vnet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您也可以從您的內部部署網路透過 Expressroute 存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]  。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes]  以委派 Azure NetApp Files 的子網路。 在每個 Vnet 中，只有一個子網路，可以委派給 Azure NetApp 檔案中。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 按一下 [通訊協定]  ，然後選取 **NFS** 作為磁碟區的通訊協定類型。   
    * 指定**file-path** ，將用來建立新的磁碟區的匯出路徑。 匯出路徑會用來掛接和存取磁碟區。

        檔案路徑名稱只能包含字母、數字和連字號 ("-")。 長度必須介於 16 到 40 個字元之間。 

        檔案路徑必須是每個訂用帳戶和每個區域內唯一的。 

    * （選擇性）[設定 NFS 磁碟區匯出原則](azure-netapp-files-configure-export-policy.md)

    ![指定 NFS 通訊協定](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 按一下 **檢閱 + 建立**檢閱磁碟區詳細資料。  然後按一下**建立**建立 NFS 磁碟區。

    您所建立的磁碟區會出現在 [磁碟區] 頁面。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="create-an-smb-volume"></a>建立 SMB 磁碟區

Azure 的 NetApp 檔案支援 SMBv3 磁碟區。 您要新增的 SMB 磁碟區之前建立 Active Directory 連線。 

### <a name="requirements-for-active-directory-connections"></a>Active Directory 連線的需求

 Active Directory 連接的需求如下所示： 

* 您使用的系統管理員帳戶必須能夠在組織單位 (OU) 指定的路徑，您將建立電腦帳戶。  

* 您必須在適用的 Windows Active Directory (AD) 伺服器上開啟適當的連接埠。  
    必要的連接埠如下所示： 

    |     服務           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web 服務    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    「 回應回覆    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名稱       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    安全的 LDAP        |    636       |    TCP           |
    |    安全的 LDAP        |    3269      |    TCP           |
    |    w32time            |    123       |    UDP           |

### <a name="create-an-active-directory-connection"></a>建立 Active Directory 連線

1. 從 NetApp 帳戶中，按一下**Active Directory 連線**，然後按一下**聯結**。  

    ![Active Directory 連線](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在 [加入 Active Directory] 視窗中，提供下列資訊：

    * **主要 DNS**  
        這是 Active Directory 網域聯結和 SMB 驗證作業所需的 DNS。 
    * **次要 DNS**   
        這是確保備援名稱服務的次要 DNS 伺服器。 
    * **網域**  
        這是您想要加入您 Active Directory 網域服務的網域名稱。
    * **SMB 伺服器 （電腦帳戶） 前置詞**  
        這是 Azure NetApp 檔案會用來建立新帳戶的 Active Directory 電腦帳戶的命名前置詞。

        比方說，如果您的組織使用檔案伺服器的命名標準 NAS-01、...、 NAS-02 NAS 045，則您會輸入"NAS"前置詞。 

        服務會視需要建立額外的電腦帳戶在 Active Directory 中。

    * **組織單位的路徑**  
        這是組織單位 (OU)，將會在當中建立 SMB 伺服器機器帳戶的 LDAP 路徑。 也就是說，OU = 第二個層級，OU = 第一個層級。 
    * 認證，包括您**使用者名稱**和**密碼**

    ![Join Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 按一下 [ **加入**]。  

    您建立的 Active Directory 連線會出現。

    ![Active Directory 連線](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>新增 SMB 磁碟區

1. 按一下 **磁碟區**刀鋒視窗中的容量集區刀鋒視窗中。 

    ![瀏覽至 磁碟區](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 按一下 [+ 新增磁碟區]  以建立磁碟區。  
    建立磁碟區 視窗隨即出現。

3. 在 [建立磁碟區] 視窗中，按一下**建立**，並提供下列欄位的資訊：   
    * **磁碟區名稱**      
        為您要建立的磁碟區指定名稱。   

        磁碟區名稱必須是每個容量集區中的唯一的。 長度至少必須有三個字元。 您可以使用任何英數字元。

    * **容量集區**  
        指定您想要建立的磁碟區容量集區。

    * **配額**  
        指定配置給磁碟區的邏輯儲存體大小。  

        [可用配額]  欄位會顯示所選容量集區中可用來建立新磁碟區的未使用空間量。 新磁碟區的大小不可超過可用配額。  

    * **虛擬網路**  
        指定您要從中存取磁碟區的 Azure 虛擬網路 (Vnet)。  

        您指定的 Vnet 必須有委派給 Azure NetApp Files 的子網路。 Azure NetApp Files 服務只能從相同的 Vnet 存取，或透過 Vnet 對等互連與磁碟區位於相同區域的 Vnet 存取。 您也可以從您的內部部署網路透過 Expressroute 存取磁碟區。   

    * **子網路**  
        指定要用於磁碟區的子網路。  
        您指定的子網路必須委派給 Azure NetApp Files。 
        
        如果您尚未委派子網路，您可以按一下 [建立磁碟區] 頁面上的 [新建]  。 在 [建立子網路] 頁面上指定子網路資訊，然後選取 [Microsoft.NetApp/volumes]  以委派 Azure NetApp Files 的子網路。 在每個 Vnet 中，只有一個子網路，可以委派給 Azure NetApp 檔案中。   
 
        ![建立磁碟區](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![建立子網路](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 按一下 **通訊協定**並完成下列資訊：  
    * 選取  **SMB**為磁碟區的通訊協定類型。 
    * 選取您**Active Directory**從下拉式清單中的連接。
    * 指定的名稱中的共用磁碟區**共用名稱**。

    ![指定 SMB 通訊協定](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 按一下 **檢閱 + 建立**檢閱磁碟區詳細資料。  然後按一下**建立**建立 SMB 磁碟區。

    您所建立的磁碟區會出現在 [磁碟區] 頁面。 
 
    磁碟區會從其容量集區繼承訂用帳戶、資源群組、位置屬性。 若要監視磁碟區部署狀態，您可以使用 [通知] 索引標籤。

## <a name="next-steps"></a>後續步驟  

* [掛接或取消掛接 Windows 或 Linux 虛擬機器的磁碟區](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [設定 NFS 磁碟區的匯出原則](azure-netapp-files-configure-export-policy.md)
* [了解 Azure 服務的虛擬網路整合](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
