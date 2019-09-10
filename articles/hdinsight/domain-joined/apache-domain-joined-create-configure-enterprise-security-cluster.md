---
title: 在 Azure HDInsight 中建立和設定企業安全性套件叢集
description: 瞭解如何在 Azure HDInsight 中建立和設定企業安全性套件叢集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: bb6a3cff46c975ae6b59f0c6f97e37037f638620
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845920"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中建立和設定企業安全性套件叢集

Azure HDInsight 的企業安全性套件可讓您針對 Azure 中的 Apache Hadoop 叢集，存取以 Active Directory 為基礎的驗證、多使用者支援和以角色為基礎的存取控制。 HDInsight ESP 叢集可讓符合嚴格公司安全性原則的組織安全地處理敏感性資料。

本指南的目標是正確設定必要的資源，讓內部部署使用者可以登入已啟用 ESP 的 HDInsight 叢集。 本文會逐步解說建立企業安全性套件啟用 Azure HDInsight 叢集所需的步驟。 這些步驟將涵蓋如何建立已啟用 Active Directory & 功能變數名稱服務（DNS）的 Windows IaaS VM。 這部伺服器將做為**實際**內部部署環境的替代方案，並可讓您繼續進行安裝和設定步驟，讓您可以在稍後於自己的環境中重複執行。 本指南也將協助您使用密碼雜湊同步處理搭配 Azure Active Directory 來建立混合式身分識別環境。

本指南旨在補充[HDInsight 中的使用企業安全性套件](apache-domain-joined-architecture.md)

在您自己的環境中使用此程式之前，請先安裝 Active Directory 和功能變數名稱服務（DNS）。 此外，請啟用 Azure Active Directory 並同步處理內部部署使用者帳戶，以 Azure Active Directory。

![架構圖表](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>建立內部部署環境

概觀：在本節中，您將使用 Azure 快速部署範本來建立新的 Vm、設定功能變數名稱服務（DNS）和新的 AD 樹系。

1. 移至[使用新的 AD 樹系建立 AZURE VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)，以查看快速部署範本。

1. 按一下 [**部署至 Azure**]。
1. 登入您的 Azure 訂用帳戶。
1. 在 [**使用新的 AD 樹系建立 AZURE VM** ] 畫面上，完成下列步驟：
    1. 從 [**訂**用帳戶] 下拉式清單中選取您想要部署資源的訂用帳戶。
    1. 選取 [**資源群組**] 旁邊的 **[新建]** ，然後輸入名稱**OnPremADVRG**
    1. 針對其餘的範本欄位輸入下列詳細資料：

        * **位置**：美國中部
        * **管理使用者名稱**：HDIFabrikamAdmin
        * **管理員密碼**： < YOUR_PASSWORD >
        * **網域**：HDIFabrikam.com
        * **Dns 首碼**： hdifabrikam

        ![範本建立 Azure VM 和 AD 樹系](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 按一下 [**購買**]
    1. 監視並等候部署完成。
    1. 確認資源是在正確的資源群組`OnPremADVRG`下建立的。

## <a name="configure-users-and-groups-for-cluster-access"></a>設定使用者和群組以進行叢集存取

概觀：在本節中，您將建立可在本指南結束時存取 HDInsight 叢集的使用者。

1. 使用遠端桌面連線到網域控制站。
    1. 如果您使用一開始所提到的範本，則網域控制站是`OnPremADVRG`資源群組中名為**adVM**的 VM。
    1. 移至 Azure 入口網站 >**資源群組** > **OnPremADVRG**  >  **adVM**  >  **Connect**。
    1. 按一下 [ **RDP** ] 索引標籤，然後按一下 [**下載 RDP**檔案]。
    1. 將檔案儲存到您的電腦並加以開啟。
    1. 當系統提示您輸入認證`HDIFabrikam\HDIFabrikamAdmin`時，請使用做為使用者名稱，然後輸入您為系統管理員帳戶所選擇的密碼。

1. 當您的遠端桌面會話在網域控制站 VM 上開啟之後，請從 **伺服器管理員** 儀表板啟動**Active Directory 使用者和電腦**。 按一下右上方的 [**工具**]，然後從下拉式清單中**Active Directory [使用者和電腦**]。

    ![伺服器管理員開啟 Active Directory 管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 建立兩個新的使用者： **HDIAdmin**、 **HDIUser**。 這兩個使用者將用來登入 HDInsight 叢集。

    1. 在 [ **Active Directory 使用者和電腦**] 畫面上，按一下 [**動作** > ] [**新增** > **使用者**]。

        ![建立新的 Active Directory 使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. 在 [**新增物件-使用者**] 畫面上`HDIUser` ，輸入做為**使用者登入名稱**，然後按 **[下一步]** 。

        ![建立第一個系統管理員使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在出現的快顯視窗中，輸入新帳戶所需的密碼。 核取 [**密碼永久**有效] 核取方塊。 HDIClick **[確定]** 。
    1. 按一下 **[完成]** 以建立新的帳戶。
    1. 建立另一`HDIAdmin`位使用者。

        ![建立第二個管理使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 在 [ **Active Directory 使用者和電腦**] 畫面中，按一下 [**動作** > ] [**新增** > **群組**]。 建立`HDIUserGroup`為新群組。

    ![建立新的 Active Directory 群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![建立新的 group2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 將在上一個步驟中建立的**HDIUser**新增至**HDIUserGroup**做為成員。

    1. 以滑鼠右鍵按一下**HDIUserGroup** ，然後按一下 [**屬性**]。
    1. 移至 [**成員**] 索引標籤，然後按一下 [**新增**]。
    1. 在`HDIUser`標示為 **[輸入物件名稱來選取**] 的方塊中輸入，然後按一下 **[確定]** 。
    1. 針對另一個帳戶重複上述步驟`HDIAdmin`

        ![將成員新增至群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

您現在已建立您的 Active Directory 環境，以及兩個使用者和一個用於存取 HDInsight 叢集的使用者群組。

這些使用者會與 Azure AD 同步處理。

### <a name="create-a-new-azure-active-directory"></a>建立新的 Azure Active Directory

1. 登入 Azure 入口網站。
1. 按一下 [**建立資源**] 並輸入**目錄**。 選取 [ **Azure Active Directory**  > **建立**]。
1. 在 [**組織名稱**] 下輸入**HDIFabrikam** 。
1. 在 [**初始功能變數名稱**] 下，輸入**HDIFabrikamoutlook** 。
1. 按一下 [建立]。
1. 在 Azure 入口網站的左側，按一下 [ **Azure Active Directory**]。
1. 如有需要，請按一下 [**切換目錄**]，變更至您所建立的新目錄**HDIFabrikamoutlook**。
1. 在 [**管理**] 下 **，按一下** > [**新增自訂網域**]。
1. 在 [**自訂功能變數名稱**] 底下輸入**HDIFabrikam.com** ，然後按一下 [**新增網域**]。

![建立新的 azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![建立新的自訂網域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>設定您的 Azure AD 租使用者

概觀：現在您將設定 Azure AD 租使用者，讓您可以將來自內部部署 AD 的使用者和群組同步至雲端。

1. 建立 AD 租使用者系統管理員。
    1. 登入 Azure 入口網站並選取您的 Azure AD 租使用者**HDIFabrikam**
    1. 選取 [**管理**] 下的 [**使用者**]，然後選取 [**新增使用者**]。
    1. 輸入新使用者的下列詳細資料：

        * 名稱： fabrikamazureadmin
        * 使用者名稱：fabrikamazureadmin@hdifabrikam.com
        * 密碼：您選擇的安全密碼

    1. 按一下 [**群組**] 區段，搜尋 [ **AAD DC 系統管理員**]，然後按一下 [**選取**]。

        ![群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. 按一下 [**目錄角色**] 區段，然後選取右側的 [**全域管理員**]。 按一下 [確定]。

        ![目錄角色](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. 輸入使用者的密碼。 按一下 [建立]。

1. 如果您想要變更新建立之使用者<fabrikamazureadmin@hdifabrikam.com>的密碼。 使用身分識別登入 Azure 入口網站，然後系統會提示您變更密碼。

## <a name="sync-on-premises-users-to-azure-ad"></a>將內部部署使用者同步至 Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>下載並安裝 Microsoft Azure Active Directory connect

1. [下載 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 在網域控制站上安裝 Microsoft Azure Active Directory connect。
    1. 開啟您在上一個步驟下載的可執行檔，並同意授權條款。 按一下 [ **繼續**]。

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. 按一下 [**使用快速設定**]，並完成安裝。

        ![使用快速設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>設定與內部部署網域控制站的同步處理

1. 在 [**連接到 Azure AD]** 畫面上，輸入 Azure AD 全域管理員的使用者名稱和密碼。 按 **[下一步]** 。 這是您在`fabrikamazureadmin@hdifabrikam.com`設定 AD 租使用者時所建立的使用者名稱。
    ![連接至 Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. 在 [**連接到 Active Directory Domain Services]** 畫面上，輸入企業系統管理員帳戶的使用者名稱和密碼。 按 **[下一步]** 。 這是您稍`HDIFabrikam\HDIFabrikamAdmin`早建立的使用者名稱及其相符的密碼。

   ![連接到 Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在 [ **Azure AD 登入**設定] 頁面上，按 **[下一步]** 。
    ![Azure AD 登入設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. 在 [準備好設定] 畫面上，按一下 [ **安裝**]。
    ![安裝](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. 顯示 [設定完成] 畫面時 **，按一下 [** **結束**]。
    ![設定完成](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 同步完成後，確認您在 IAAS Active Directory 上建立的使用者是否已同步處理至 Azure Active Directory。
    1. 登入 Azure 入口網站。
    1. 選取 [ **Azure Active Directory**  >  **HDIFabrikam**  > **使用者**]。

### <a name="create-an-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立使用者指派的受控識別，將用來設定 Azure Active Directory Domain Services （Azure AD-DS）。 如需建立使用者指派的受控識別的詳細資訊，請參閱[使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登入 Azure 入口網站。
1. 按一下 [**建立資源**]，然後輸入**受控識別**。 選取 [**使用者指派的受控識別** > ] [**建立**]。
1. 輸入**HDIFabrikamManagedIdentity**做為**資源名稱**。
1. 選取您的訂用帳戶。
1. 在 [**資源群組**] 下，按一下 [**建立新**的]，然後輸入**HDIFabrikam-CentralUS**。
1. 選取 [**位置**] 底下的 [**美國中部**]。
1. 按一下 [建立]。

![建立使用者指派的新受控識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>啟用 Azure Active Directory Domain Services

如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

1. 建立要裝載 Azure AD DS 的虛擬網路。 執行下列 powershell 程式碼。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登入 Azure 入口網站。
1. 按一下 [**建立資源**]，輸入**網域服務**，然後選取 [ **Azure AD Domain Services**]。
1. 在 [**基本**] 畫面上，完成下列步驟：
    1. 在 [**目錄名稱**] 底下，選取針對此文章**HDIFabrikam**建立的 Azure Active Directory。
    1. 輸入**HDIFabrikam.com**的**DNS 功能變數名稱**。
    1. 選取您的訂用帳戶。
    1. 指定資源群組**HDIFabrikam-CentralUS**和「**美國中部**」的**位置**。

        ![azure ad ds 基本詳細資料](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. 在 [**網路**] 畫面上，選取您使用先前的 powershell 腳本建立的網路（**HDIFabrikam-VNET**）和子網（**AADDS 子網**）。 或者，您可以使用 [**建立新**的] 選項立即建立虛擬網路。

    ![選取網路](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在 [**系統管理員群組**] 畫面上，您應該會看到一則通知，指出已建立名為**AAD DC 系統管理員**的群組來管理此群組。 您可以選擇性地修改此群組的成員資格，但本文的步驟並不需要這麼做。 按一下 [確定]。

    ![view administrator 群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在 [**同步**處理] 畫面上，選取 [**全部**]，然後按一下 **[確定]** ，以啟用完整同步處理。

    ![啟用同步處理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. 在 [**摘要**] 畫面上，確認 AZURE AD-DS 的詳細資料，然後按一下 **[確定]** 。

    ![驗證詳細資料](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. 啟用 Azure AD-DS 之後，本機網域名稱服務 (DNS) 伺服器會在 AD 虛擬機器 (VM) 上執行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>設定您的 Azure AD DS 虛擬網路

本節中的步驟可協助您設定您的 Azure AD DS 虛擬網路（**HDIFabrikam-AADDSVNET**），以使用您的自訂 DNS 伺服器。

1. 找出自訂 DNS 伺服器的 IP 位址。 按一下 [ **HDIFabrikam.com** ] [AD-DS] 資源，按一下 **[管理**  ] 底下的 [ **屬性**]，然後查看虛擬網路上的 [ **ip 位址**] 底下列出的 ip 位址。

    ![找出 Azure AD-DS 的自訂 DNS IP 位址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. 將**HDIFabrikam AADDSVNET**設定為 [自`10.0.0.4`定義`10.0.0.5`ip 和]。

    1. 選取 [  **設定** ]類別下的[DNS伺服器 ]。 然後按一下 [ **自訂**] 旁的選項按鈕，輸入下方文字方塊中的第一個 IP 位址（10.0.0.4），然後按一下 [ **儲存**]。
    1. 使用相同的步驟新增額外的 IP 位址（10.0.0.5）。

1. 在我們的案例中 Azure AD-DS 已設定為使用 IP 位址10.0.0.4 和10.0.0.5，請在 AADDS VNet 上設定相同的 IP 位址，如下圖所示。

    ![查看自訂 dns 伺服器](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>保護 LDAP 流量

輕量型目錄存取協定（LDAP）是用來讀取和寫入 Active Directory。 您可以使用安全通訊端層（SSL）/傳輸層安全性（TLS）技術，將 LDAP 流量設為機密和安全。 您可以藉由安裝正確格式的憑證來啟用 LDAP over SSL （LDAPS）。

如需安全 LDAP 的詳細資訊，請參閱[為 Azure AD Domain Services 受控網域設定安全 ldap （LDAPS）](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本節中，您會建立自我簽署憑證、下載憑證，以及為**hdifabrikam** Azure AD DS 受控網域設定安全的 LDAP （LDAPS）。

下列腳本會建立 hdifabrikam 的憑證。 憑證會儲存在 "LocalMachine" 路徑之下。

> [!Note] 
> 任何建立有效 PKCS \#10 要求的公用程式或應用程式，都可以用來形成 SSL 憑證要求。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

確認憑證已安裝在電腦\'的個人存放區中。 完成下列步驟：

1. 啟動 Microsoft Management Console （MMC）。
1. 新增 [憑證] 嵌入式管理單元，以管理本機電腦上的憑證。
1. 依序展開 **[憑證（本機電腦）** ] 和 [ **個人**]，然後展開 [ **憑證**]。 「個人」存放區中應該會有新的憑證。 此憑證會發行至完整主機名稱。

    ![確認憑證建立](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右窗格中，以滑鼠右鍵按一下您在上一個步驟中建立的憑證，指向 [ **所有**工作]，然後按一下 [ **匯出**]。

1. 在 [ **匯出私密金鑰** ] 頁面上，按一下 [ **是，匯出私用金鑰]** 。 要從匯入金鑰的電腦讀取加密訊息時，必須要有私密金鑰。

    ![匯出私密金鑰](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. 在 [ **匯出檔案格式** ] 頁面上，保留預設設定，然後按 **[下一步]** 。 
1. 在 [ **密碼** ] 頁面上，輸入私密金鑰的密碼，選取 [ **TripleDES-SHA1**進行**加密**]，然後按 **[下一步]** 。
1. 在 [ **要匯出** 的檔案] 頁面上，輸入所匯出憑證檔案的路徑和名稱，然後按 **[下一步]** 。
1. 檔案名必須是 .pfx 副檔名，此檔案是在 Azure 入口網站上設定，以建立安全連線。
1. 為 Azure AD Domain Services 受控網域啟用安全的 LDAP （LDAPS）。
    1. 從 Azure 入口網站中選取 網域 **HDIFabrikam.com** 。
    1. 按一下 [**管理**] 底下的 [**安全 LDAP** ]。
    1. 在 **安全 LDAP**  畫面上，按一下 **安全 LDAP**下的 **啟用**。
    1. 流覽您在電腦上匯出的 .pfx 憑證檔案。
    1. 輸入憑證密碼。

    ![啟用安全 ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 現在您已啟用安全 LDAP，請啟用埠636以確定可連線。
    1. 按一下**HDIFabrikam-CentralUS**資源群組中的網路安全性群組 [ **AADDS-HDIFabrikam.com-NSG** ]。
    1. 在 [**設定**] 下按一下 [**輸入安全性規則** > ] [**新增**]。
    1. 在 [**新增輸入安全性規則**] 畫面上，輸入下列屬性，然後按一下 [**新增**]：

        | 屬性 | 值 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | 目的地 | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | 允許 |
        | Priority | \<所需的數位\> |
        | Name | Port_LDAP_636 |

    ![輸入安全性規則](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`是使用者指派的受控識別，會啟用 HDInsight 網域服務參與者角色給受控識別，讓此身分識別能夠讀取、建立、修改和刪除網域服務作業。

    ![建立使用者指派的受控識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>建立已啟用企業安全性套件的 HDInsight 叢集

此步驟需要下列先決條件：

1. 在位置`HDIFabrikam-WestUS` `West US`中建立新的資源群組。
1. 建立將裝載已啟用 ESP 的 HDInsight 叢集的虛擬網路。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在裝載 AADDS （`HDIFabrikam-AADDSVNET`）的虛擬網路和將裝載已啟用 ESP 的 HDInsight 叢集（`HDIFabrikam-HDIVNet`）的虛擬網路之間建立對等關聯性。 使用下列 powershell 程式碼，將這兩個虛擬網路對等互連。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 建立以使用者受控識別**HDIFabrikamManagedIdentity**設定的新 Azure Data Lake Storage Gen2 帳戶**Hdigen2store**。 如需建立使用使用者受控識別啟用 Data Lake Storage Gen2 帳戶的詳細資訊，請參閱[搭配使用 Azure Data Lake Storage Gen2 與 Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)叢集。

1. 在**HDIFabrikam-AADDSVNET**虛擬網路上設定自訂 DNS。
    1. 移至 Azure 入口網站 >**資源群組** >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS 伺服器**。
    1. 選取 [**自訂**] `10.0.0.5`，然後輸入`10.0.0.4`和。
    1. 按一下 [儲存]。

        ![儲存自訂 dns 設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 建立已啟用 ESP 的新 HDInsight Spark 叢集。
    1. 按一下 **[自訂（大小、設定、應用程式）** ]。
    2. 輸入第1節**基本概念**所需的詳細資料。 確定叢集**類型**為**SPARK 2.3 （HDI 3.6）** ，而**資源群組**為**HDIFabrikam-CentralUS**

    1. 在第2節的**安全性 + 網路**底下，完成下列步驟：
        1. 按一下 [**企業安全性套件**] 底下的 [**啟用**]。
        1. 按一下 [叢集**管理使用者**]，然後選取您稍早建立的**HDIAdmin**帳戶作為內部部署系統管理員使用者。 按一下 [選取]。

        1. 按一下 [叢集**存取群組**]，然後選取 [ **HDIUserGroup**]。 您未來新增至此群組的任何使用者都將能夠存取 HDInsight 叢集。

            ![選取叢集存取群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成叢集設定的其他步驟，並確認叢集**摘要**的詳細資料。 按一下 [建立]。

1. 使用您的系統管理員使用者名稱`https://CLUSTERNAME.azurehdinsight.net` `hdiadmin@hdifabrikam.com`和密碼，登入新建立之叢集的 Ambari UI。

    ![登入 Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. 按一下 [叢集儀表板] 中的 [**角色**]。
1. 在 [**角色**] 頁面上，輸入群組**hdiusergroup** ，將其指派給 [**指派角色給這些**] 底下的 [叢集**系統管理員**] 角色。

    ![將叢集系統管理員角色指派給 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 開啟您的 SSH 用戶端，並使用您先前在內部部署 Active Directory 中建立的**hdiuser**登入叢集。

    ![使用 SSH 登入叢集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果您可以使用此帳戶登入，則表示您已正確地將 ESP 叢集設定為與您的內部部署 active directory 同步。

## <a name="next-steps"></a>後續步驟

* [使用企業安全性套件維護 Apache Hadoop 安全性的簡介](hdinsight-security-overview.md)
