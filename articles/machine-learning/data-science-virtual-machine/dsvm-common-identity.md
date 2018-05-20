---
title: 設定資料科學虛擬機器的通用身分識別 - Azure | Microsoft Docs
description: 在企業團隊的 DSVM 環境中設定通用身分識別。
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process, 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 團隊資料科學流程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>在資料科學 VM 上設定通用身分識別

根據預設，在包括資料科學 VM (DSVM) 的 Azure VM 上，將在佈建 VM 的同時建立本機使用者帳戶，而使用者會使用這些認證向 VM 進行驗證。 如果您有多個需要存取的 VM，這種方式很快會變得難以管理認證。 使用標準型身分識別提供者的通用使用者帳戶和管理，可讓您使用一組認證來存取 Azure 上的多個資源，包括多個 DSVM。 

Active Directory (AD) 是一種熱門的身分識別提供者，支援同時在 Azure 上作為服務及內部部署。 您可以利用 AD 或 Azure AD，在獨立資料科學 VM (DSVM) 或是 Azure 虛擬機器擴展集的 DSVM 叢集上驗證使用者。 此作業可透過將 DSVM 執行個體加入 AD 網域 來完成。 如果您已具備用來管理身分識別的 Active Directory，則可以使用它作為通用身分識別提供者。 如果沒有 AD，您可以透過名為 [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) 的服務在 Azure 上執行受控 AD。 

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) 的文件提供受控 Active Directory 的詳細[指示](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity)，包括將 Azure AD 連線到您的內部部署目錄 (如果有的話)。 

本文的其餘部分說明下列步驟：使用 Azure AD DS 在 Azure 中設定完全受控的 AD 網域服務，以及將您 DSVM 加入受控 AD 網域，讓使用者能夠使用通用的使用者帳戶和認證存取 DSVM 的集區 (和其他 Azure 資源)。 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>在 Azure 上設定完全受控的 Active Directory 網域

Azure AD DS 可透過在 Azure 上提供完全受控的服務，讓您輕鬆地管理您的身分識別。 在這個 Active Directory 網域上，使用者和群組皆受控。  在您的目錄中設定 Azure 裝載的 AD 網域和使用者帳戶的步驟如下：

1. 在入口網站上將使用者新增至 Active Directory 

    a. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
    
    b. 選取 [Azure Active Directory]，然後選取 [使用者和群組]。
    
    c. 在 [使用者和群組] 上，選取 [所有使用者]，然後選取 [新增使用者]。
   
   ![選取 [新增] 命令](./media/add-user.png)
    
    d. 輸入使用者的詳細資料，例如「名稱」和「使用者名稱」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[網域名稱].onmicrosoft.com"，或是已驗證的非同盟[自訂網域名稱](../../active-directory/add-custom-domain.md)，例如"contoso.com"。
    
    e. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
    
    f. (選擇性) 您可以開啟使用者的 [設定檔]、[群組] 或 [目錄角色]，然後在其中填入資訊。 
    
    g. 在 [使用者] 中，選取 [建立]。
    
    h. 將產生的密碼安全地散發給新使用者，以便讓使用者可以登入。

2.  建立 Azure AD Domain Services

    若要建立 Azure ADDS，請遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)一文中的指示 (工作 1 到工作 5)。 請務必更新 Active Directory 中現有的使用者密碼，讓 Azure AD DS 中的密碼同步。 也務必將 DNS 新增至 Azure AD DS，如上文的工作 #4 所示。 

3.  在先前步驟的工作 #2 中建立的虛擬網路中建立個別的 DSVM 子網路
4.  在 DSVM 子網路中建立一或多個資料科學 VM 執行個體 
5.  遵循[指示](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )將 DSVM 新增至 AD。 
6.  接下來掛接共用的 Azure 檔案來裝載您的首頁或筆記本目錄，以便在任何電腦上掛接您的工作區。 (如果您需要嚴格的檔案層級權限，則需要在一或多個 VM 上執行 NFS)

    a. [建立 Azure 檔案共用](../../storage/files/storage-how-to-create-file-share.md)
    
    b. 在 Linux DSVM 上掛接它。 當您在 Azure 入口網站上的儲存體帳戶中針對 Azure 檔案按一下 [連線] 按鈕時，將會顯示要在 Linux DSVM 的 bash 殼層中執行的命令。 該命令如下所示：
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  例如，您已在 /data/workspace 中掛接 Azure 檔案。 現在，為共用中的每個使用者建立目錄。 /data/workspace/user1、/data/workspace/user2，依此類推。 在每個使用者的工作區中建立 ```notebooks``` 目錄。 
8. 在 ```$HOME/userx/notebooks/remote``` 中建立 ```notebooks``` 的符號連結。   

此時，您在 Azure 上裝載的 Active Directory 中具有使用者，因此能夠使用 AD 認證登入已加入 Azure AD DS 的任何 DSVM (同時包含 SSH、Jupyterhub)。 由於使用者工作區位在共用的 Azure 檔案上，因此使用者在使用 Jupyterhub 時，可以從任何 DSVM 存取其筆記本和其他工作。 

對於自動調整，您可以使用虛擬機器擴展集來建立 VM 的集區，這些 VM 都是以這種方式加入網域，而且已掛接共用磁碟。 使用者可以登入虛擬機器擴展集中的任何可用電腦，而且擁有儲存其筆記本之共用磁碟的存取權。 

## <a name="next-steps"></a>後續步驟

* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)



