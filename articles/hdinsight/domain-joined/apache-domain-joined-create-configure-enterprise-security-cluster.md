---
title: 建立並在 Azure HDInsight 中設定企業安全性套件的叢集
description: 了解如何建立和設定 Azure HDInsight 中的企業安全性套件的叢集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 98bd222212d616a5d2c608779c607bb431d184b9
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657318"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>建立並在 Azure HDInsight 中設定企業安全性套件的叢集

Azure HDInsight 的企業安全性套件可讓您存取 Active Directory 為基礎的驗證、 多使用者支援和角色型存取控制您 Azure 中的 Apache Hadoop 叢集。 ESP HDInsight 叢集啟用遵守嚴格的公司安全性原則，以安全地處理敏感性資料的組織。

本指南的目標是要正確地設定所需的資源，以便在內部部署使用者可以登入 ESP 啟用 HDInsight 叢集。 這篇文章會逐步建立啟用企業安全性套件在 Azure HDInsight 叢集所需的步驟。 這些步驟會包含網域名稱服務 (DNS) 啟用與 Active Directory 中建立 Windows IaaS VM。 此伺服器做為取代您**實際**在內部部署環境，並可讓您進行安裝和設定步驟，以便您可以將它們重複之後，在您自己的環境。 本指南也會協助您建立混合式身分識別環境與 Azure Active Directory 中使用密碼雜湊同步處理。

本指南旨在補足[在 HDInsight 中使用企業安全性套件](apache-domain-joined-architecture.md)

之前在您自己的環境中使用此程序，設定 Active Directory 和網域名稱服務 (DNS)。 此外，啟用 Azure Active Directory 和內部同步處理至 Azure Active Directory 的使用者帳戶。

![架構圖表](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>建立內部部署環境

概觀：在本節中，您將使用 Azure 快速部署範本，若要建立新的 Vm，請設定網域名稱服務 (DNS) 和新的 AD 樹系。

1. 移至[建立 Azure VM 與新的 AD 樹系](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)，若要檢視的快速部署範本。

1. 按一下 **部署至 Azure**。
1. 登入您的 Azure 訂用帳戶。
1. 在 **建立 Azure VM 與新的 AD 樹系**畫面上，完成下列步驟：
    1. 選取您想要從部署的資源的訂用帳戶**訂用帳戶**下拉式清單。
    1. 選取 **新建**旁**資源群組**，然後輸入名稱**OnPremADVRG**
    1. 其餘的範本欄位中輸入下列詳細資料：

        * **位置**：美國中部
        * **管理使用者名稱**：HDIFabrikamAdmin
        * **系統管理員密碼**: < YOUR_PASSWORD >
        * **網域**:HDIFabrikam.com
        * **Dns 前置詞**: hdifabrikam

        ![範本建立 Azure VM 和 AD 樹系](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. 按一下 **購買**
    1. 監視並等候部署完成。
    1. 確認正確的資源群組下建立資源，則`OnPremADVRG`。

## <a name="configure-users-and-groups-for-cluster-access"></a>設定叢集存取權的使用者和群組

概觀：在本節中，您將建立會有本指南結束時的 HDInsight 叢集的存取權的使用者。

1. 連接到使用遠端桌面的網域控制站。
    1. 如果您使用範本開頭所述，網域控制站是 VM，稱為**adVM**在`OnPremADVRG`資源群組。
    1. 移至 Azure 入口網站 >**資源群組** > **OnPremADVRG** > **adVM** > **Connect**.
    1. 按一下  **RDP**索引標籤，然後按一下**下載 RDP 檔案**。
    1. 將檔案儲存到您的電腦，然後開啟它。
    1. 當系統提示輸入認證，使用`HDIFabrikam\HDIFabrikamAdmin`作為使用者名稱，然後輸入您選擇的系統管理員帳戶的密碼。

1. 一旦您的遠端桌面工作階段隨即開啟網域控制站 VM 上，啟動**Active Directory 使用者和電腦**從**伺服器管理員**儀表板。 按一下 **工具**右上角，然後**Active Directory 使用者和電腦**從下拉式清單。

    ![伺服器管理員開啟 Active Directory 管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 建立兩個新的使用者**HDIAdmin**， **HDIUser**。 這兩名使用者將用以登入 HDInsight 叢集。

    1. 在  **Active Directory 使用者和電腦**畫面上，按一下**動作** > **新增** > **使用者**。

        ![建立新的 Active Directory 使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. 在 **新增物件-使用者**畫面上，輸入`HDIUser`作為**使用者登入名稱**，按一下 **下一步**。

        ![建立第一個管理使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. 在出現的快顯視窗，輸入新的帳戶所需的密碼。 核取方塊，指出**密碼永久有效**。 HDIClick**確定**。
    1. 按一下 **完成**建立新的帳戶。
    1. 建立另一位使用者`HDIAdmin`。

        ![建立第二個管理使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. 在  **Active Directory 使用者和電腦**畫面上，按一下**動作** > **新增** > **群組**。 建立`HDIUserGroup`做為新的群組。

    ![建立新的 Active Directory 群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![建立新的群組 2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. 新增**HDIUser**在上一個步驟中建立**HDIUserGroup**做為成員。

    1. 以滑鼠右鍵按一下**HDIUserGroup**然後按一下**屬性**。
    1. 移至**成員**索引標籤，然後按一下**新增**。
    1. Enter`HDIUser`標籤的方塊**輸入物件名稱來選取**然後按一下 **[確定]** 。
    1. 重複上述步驟，其他帳戶 `HDIAdmin`

        ![將成員加入群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

您現在已建立您的 Active Directory 環境，以及兩位使用者和使用者群組來存取 HDInsight 叢集。

這些使用者將會與 Azure AD 同步。

### <a name="create-a-new-azure-active-directory"></a>建立新的 Azure Active Directory

1. 登入 Azure 入口網站。
1. 按一下 **建立資源**並輸入**directory**。 選取  **Azure Active Directory** > **建立**。
1. 請輸入**HDIFabrikam**下方**組織名稱**。
1. 請輸入**HDIFabrikamoutlook**下方**初始網域名稱**。
1. 按一下 [建立]  。
1. 在 Azure 入口網站中左側，按一下**Azure Active Directory**。
1. 如果有必要，請按一下**切換目錄**變更為您建立的新目錄**HDIFabrikamoutlook**。
1. 底下**管理**按一下 **自訂網域名稱** > **新增自訂網域**。
1. 請輸入**HDIFabrikam.com**下方**自訂網域名稱**，按一下 **新增網域**。

![建立新的 azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![建立新的自訂網域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>設定 Azure AD 租用戶

概觀：現在您將設定 Azure AD 租用戶，以便您可以同步處理使用者和群組從內部部署 AD 至雲端。

1. 建立的 AD 租用戶系統管理員。
    1. 登入 Azure 入口網站，然後選取 Azure AD 租用戶**HDIFabrikam**
    1. 選取 **使用者**下方**管理**，然後**新使用者**。
    1. 新的使用者，請輸入下列詳細資料：

        * 名稱： fabrikamazureadmin
        * 使用者名稱： fabrikamazureadmin@hdifabrikam.com
        * 密碼： 安全您選擇的密碼

    1. 按一下 **群組**區段中，搜尋**AAD DC 系統管理員**，然後按一下**選取**。

        ![群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. 按一下 **目錄角色**區段，然後選取**全域管理員**右側。 按一下 [確定]  。

        ![目錄角色](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. 輸入使用者的密碼。 按一下 [建立]  。

1. 如果您想要變更新建的使用者的密碼<fabrikamazureadmin@hdifabrikam.com>。 您可以登入 Azure 入口網站使用的身分識別，然後將變更密碼的提示。

## <a name="sync-on-premises-users-to-azure-ad"></a>同步處理內部部署至 Azure AD 的使用者

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>下載並安裝 Microsoft Azure Active Directory connect

1. [下載 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 安裝 Microsoft Azure Active Directory 連線到網域控制站。
    1. 在上一個步驟中開啟您下載的可執行檔，並同意授權條款。 按一下 [ **繼續**]。

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. 按一下 **使用快速設定**並完成安裝。

        ![使用快速設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>設定與內部部署網域控制站的同步處理

1. 在 **連線到 Azure AD**畫面上，輸入 Azure AD 的使用者名稱和密碼的全域管理員。 按一下 [ **下一步]** 。 這是使用者名稱`fabrikamazureadmin@hdifabrikam.com`時設定您的 AD 租用戶。
    ![連接至 Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. 在 **連接到 Active Directory 網域服務**畫面上，輸入企業系統管理員帳戶的使用者名稱和密碼。 按一下 [ **下一步]** 。 這是使用者名稱`HDIFabrikam\HDIFabrikamAdmin`及其您稍早建立的相符密碼。

   ![連接到 Active Directory 網域服務](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. 在 [ **Azure AD 登入組態**頁面上，按一下**下一步]** 。
    ![Azure AD 登入設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. 在 [準備好設定] 畫面中，按一下 **安裝**。
    ![安裝](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. 當**設定完成**顯示畫面中，按一下**結束**。
    ![設定完成](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. 同步處理完成之後請確認您在 IAAS Active Directory 建立的使用者會同步處理至 Azure Active Directory。
    1. 登入 Azure 入口網站。
    1. 選取  **Azure Active Directory** > **HDIFabrikam** > **使用者**。

### <a name="create-an-user-assigned-managed-identity"></a>建立使用者指派的受控身分識別

建立使用者指派給受控身分識別是用來設定 Azure Active Directory Domain Services (Azure AD DS)。 如需有關如何建立使用者指派的受控身分識別的詳細資訊，請參閱 <<c0> [ 建立、 清單、 刪除或指派使用者指派給受控身分識別使用 Azure 入口網站角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登入 Azure 入口網站。
1. 按一下 **建立資源**並輸入**受控身分識別**。 選取 **指派給受控身分識別的使用者** > **建立**。
1. 請輸入**HDIFabrikamManagedIdentity**作為**資源名稱**。
1. 選取您的訂用帳戶。
1. 底下**資源群組**按一下 **新建**，然後輸入**HDIFabrikam CentralUS**。
1. 選取 **美國中部**下方**位置**。
1. 按一下 [建立]  。

![建立新的使用者指派給受控身分識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>啟用 Azure Active Directory Domain Services

如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

1. 建立虛擬網路到 Azure AD DS 的主機。 執行下列 powershell 程式碼。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登入 Azure 入口網站。
1. 按一下 **建立資源**，輸入**網域服務**，然後選取**Azure AD Domain Services**。
1. 在 **基本概念**螢幕完成下列步驟：
    1. 底下**目錄名**選取為本文中，建立 Azure Active Directory **HDIFabrikam**。
    1. 請輸入**DNS 網域名稱**的**HDIFabrikam.com**。
    1. 選取您的訂用帳戶。
    1. 指定的資源群組**HDIFabrikam CentralUS**並**位置**的**美國中部**。

        ![azure ad ds 的基本詳細資料](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. 在上**網路**完成畫面上，選取 網路 (**HDIFabrikam VNET**) 和子網路 (**AADDS 子網路**) 使用上述的 powershell 指令碼建立的。 或者您可以使用**新建**立即建立虛擬網路的選項。

    ![選取網路](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. 在上**系統管理員群組**畫面中，您應該會看到一則通知，群組命名為**AAD DC 系統管理員**來管理此群組已經存在。 您可以選擇性地修改此群組的成員資格，但它並不需要這篇文章的步驟。 按一下 [確定]  。

    ![檢視系統管理員群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. 在 **同步處理**畫面上，選取以啟用完整的同步處理**所有**，然後按一下 **確定**。

    ![啟用同步處理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. 在 **摘要**畫面上，確認詳細資料的 Azure AD DS 並按一下 **確定**。

    ![確認詳細資料](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. 啟用 Azure AD-DS 之後，本機網域名稱服務 (DNS) 伺服器會在 AD 虛擬機器 (VM) 上執行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>設定 Azure AD DS 虛擬網路

在本節中的步驟將協助您設定您的 Azure AD DS 虛擬網路 (**HDIFabrikam AADDSVNET**) 若要使用您的自訂 DNS 伺服器。

1. 找出您的自訂 DNS 伺服器的 IP 位址。 按一下**HDIFabrikam.com** AD DS 資源中，按一下 **屬性**下**管理** 並查看底下列出的 IP 位址 **IP虛擬網路上的地址**。

    ![找到自訂的 DNS IP 位址的 Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. 設定**HDIFabrikam AADDSVNET**自訂 ip`10.0.0.4`和`10.0.0.5`。

    1. 選取  **DNS 伺服器** 之下 **設定** 類別目錄。 然後按一下 [選項] 按鈕旁 **自訂**，在文字方塊中，輸入第一個 IP 位址 (10.0.0.4)，然後按一下 **儲存**。
    1. 新增額外的 IP 位址 (10.0.0.5) 使用相同的步驟。

1. 我們的案例中 Azure AD DS 已設定為使用 AADDS VNet 上的 IP 位址 10.0.0.4 和 10.0.0.5，設定相同的 IP 位址，在下圖所示。

    ![檢視自訂的 dns 伺服器](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>保護流量安全 LDAP

輕量型目錄存取通訊協定 (LDAP) 用來讀取和寫入至 Active Directory。 您可以使 LDAP 流量更具機密性及安全性使用安全通訊端層 (SSL) / 傳輸層安全性 (TLS) 技術。 您可以啟用 LDAP over SSL (LDAPS) 所安裝的正確格式的憑證。

如需有關安全 LDAP 的詳細資訊，請參閱 <<c0> [ 設定安全 LDAP (LDAPS)，Azure AD Domain services 受控網域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)。

在本節中，您建立自我簽署的憑證，下載憑證，並設定安全 LDAP (LDAPS) 的**hdifabrikam** Azure AD DS 受控網域。

下列指令碼會建立 hdifabrikam 的憑證。 憑證會儲存在路徑"LocalMachine"下。

> [!Note] 
> 任何公用程式或應用程式，建立有效的 PKCS \#10 要求可以用來形成 SSL 憑證要求。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

確認憑證已安裝在電腦中\'s 個人存放區。 完成下列步驟：

1. 啟動 Microsoft Management Console (MMC)。
1. 新增憑證嵌入式管理單元可管理本機電腦上的憑證。
1. 依序展開 **Certificates (Local Computer)** ，展開 **個人**，然後展開 **憑證**。 新的憑證應存在於 [個人] 存放區。 此憑證被發給完整的主機名稱。

    ![確認 建立憑證](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. 在右窗格中，以滑鼠右鍵按一下您在上一個步驟中，建立的憑證，指向 **所有工作**，然後按一下 **匯出**。

1. 在  **匯出私密金鑰** 頁面上，按一下 **是，匯出私密金鑰，** 。 要讀取從電腦將匯入金鑰加密的訊息需要私密金鑰。

    ![匯出私密金鑰](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. 在  **匯出檔案格式** 頁面上，保留預設設定，然後按一下  **下一步**。 
1. 在上 **密碼** 頁面上，輸入密碼的私密金鑰，選取**TripleDES SHA1**如**加密**，按一下 **下一步**.
1. 在 [ **要匯出的檔案** 頁面上，輸入路徑和匯出的憑證檔的名稱，然後按一下 **下一步]** 。
1. 必須是.pfx 副檔名的檔案名稱，此檔案已在 Azure 入口網站來建立安全連線。
1. 啟用安全 LDAP (LDAPS) 的 Azure AD Domain Services 受控網域。
    1. 選取的定義域**HDIFabrikam.com**從 Azure 入口網站。
    1. 按一下 **安全 LDAP**下方**管理**。
    1. 在 **安全 LDAP**畫面上，按一下**啟用**之下**安全 LDAP**。
    1. 瀏覽您匯出您的電腦的.pfx 憑證檔案。
    1. 輸入憑證密碼。

    ![啟用安全 ldap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. 既然您已啟用安全 LDAP，確認它可以連線到啟用連接埠 636。
    1. 按一下 網路安全性群組**AADDS HDIFabrikam.com NSG**中**HDIFabrikam CentralUS**資源群組。
    1. 底下**設定**按一下 **輸入安全性規則** > **新增**。
    1. 在 **新增輸入的安全性規則**畫面上，輸入下列內容並按**新增**:

        | 屬性 | 值 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | 目的地 | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | 動作 | 允許 |
        | 優先順序 | \<所需的數目\> |
        | 名稱 | Port_LDAP_636 |

    ![輸入的安全性規則](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` 為使用者指派的受控身分識別，則 「 HDInsight 網域服務參與者 」 角色已啟用受管理身分識別，可讓這個識別来讀取、 建立、 修改及刪除網域服務作業。

    ![建立使用者指派給受控身分識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>建立企業安全性套件啟用 HDInsight 叢集

此步驟需要下列必要條件：

1. 建立新的資源群組`HDIFabrikam-WestUS`位置中`West US`。
1. 建立虛擬網路，以架設 ESP 啟用 HDInsight 叢集。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 建立對等之間的關聯性裝載 AADDS 虛擬網路 (`HDIFabrikam-AADDSVNET`)，而且將裝載 ESP 虛擬網路啟用 HDInsight 叢集 (`HDIFabrikam-HDIVNet`)。 您可以使用下列 powershell 程式碼，這些兩個虛擬網路對等互連。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 建立新的 Azure Data Lake 儲存體 Gen2 帳號**Hdigen2store**，也就是使用受管理的使用者身分識別設定**HDIFabrikamManagedIdentity**。 如需有關建立 Data Lake 儲存體 Gen2 帳戶啟用與使用者管理的身分識別，請參閱[使用 Azure Data Lake 儲存體 Gen2 與 Azure HDInsight 叢集](../hdinsight-hadoop-use-data-lake-storage-gen2.md)。

1. 在 設定自訂 DNS **HDIFabrikam AADDSVNET**虛擬網路。
    1. 移至 Azure 入口網站 >**資源群組** > **OnPremADVRG** > **HDIFabrikam AADDSVNET**  >  **DNS 伺服器**。
    1. 選取 **自訂**，然後輸入`10.0.0.4`和`10.0.0.5`。
    1. 按一下 [儲存]  。

        ![儲存自訂的 dns 設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. 建立新的 ESP 啟用 HDInsight Spark 叢集。
    1. 按一下 **自訂 （大小、 設定、 應用程式）** 。
    2. 輸入所需的詳細資料區段 1**基本概念**。 請確認**叢集類型**是**Spark 2.3 (HDI 3.6)** 並**資源群組**是**HDIFabrikam CentralUS**

    1. 在第 2 節**安全性 + 網路**，完成下列步驟：
        1. 按一下 **啟用**下方**企業安全性套件**。
        1. 按一下 **叢集系統管理員使用者**，然後選取**HDIAdmin**您稍早建立內部部署系統管理員身分的帳戶。 按一下 [選取]  。

        1. 按一下 **叢集的存取群組**，然後選取**HDIUserGroup**。 在未來加入此群組的任何使用者將能夠存取 HDInsight 叢集。

            ![選取叢集的存取群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. 完成叢集設定的其他步驟，並在確認詳細資料**叢集摘要**。 按一下 [建立]  。

1. 登入新建立的叢集在 Ambari UI`https://CLUSTERNAME.azurehdinsight.net`使用您的系統管理員使用者名稱`hdiadmin@hdifabrikam.com`和密碼。

    ![登入 Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. 按一下 **角色**叢集儀表板。
1. 在上**角色**頁面上，輸入群組**hdiusergroup**若要將它指派給**叢集系統管理員**角色**指派角色給這些**。

    ![將叢集系統管理員角色指派給 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. 開啟您的 SSH 用戶端和叢集使用的登入**hdiuser**您先前建立的內部部署 Active Directory 中。

    ![登入使用 SSH 的叢集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

如果您能夠使用此帳戶登入，然後您已設定 ESP 叢集正確地與您的內部部署 active directory 同步處理。

## <a name="next-steps"></a>後續步驟

* [使用企業安全性套件維護 Apache Hadoop 安全性的簡介](apache-domain-joined-introduction.md)
