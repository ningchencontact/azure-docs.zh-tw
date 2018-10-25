---
title: 使用 Azure AD-DS 設定具有企業安全性套件的 HDInsight 叢集
description: 了解如何使用 Azure Active Directory Domain Services 來設定 HDInsight 企業安全性套件叢集。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/9/2018
ms.openlocfilehash: 851fa7c6a970d725a52bc84d7d057472e09c3ee9
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388335"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集

企業安全性套件 (ESP) 叢集可在 Azure HDInsight 叢集上提供多使用者存取。 具有 ESP 的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。 

在本文中，您將了解如何使用 Azure Active Directory Domain Services (Azure AD-DS) 來設定具有 ESP 的 HDInsight 叢集。

>[!NOTE]
>ESP 在 HDI 3.6 中是適用於 Spark、互動式和 Hadoop 的正式運作版。 HBase 和 Kafka 叢集類型的 ESP 處於預覽狀態。

## <a name="enable-azure-ad-ds"></a>啟用 Azure AD-DS

> [!NOTE]
> 只有租用戶的系統管理員具有建立 Azure AD-DS 執行個體的權限。 如果叢集儲存體是 Azure Data Lake Store (ADLS) Gen1 或 Gen2，請僅對將會存取叢集的使用者停用多重要素驗證 (MFA)。 如果叢集儲存體是 Azure Blob 儲存體 (WASB)，請勿停用 MFA。

啟用 Azure AD-DS 是必要條件，如此您才能建立具有 ESP 的 HDInsight 叢集。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

Azure AD-DS 啟用時，所有使用者和物件依預設都會開始從 Azure Active Directory 同步處理至 Azure AD-DS。 同步作業的所需時間取決於 Azure AD 中的物件數目。 數十萬個物件的同步可能需要幾天的時間。 

客戶可以選擇僅同步需要存取 HDInsight 叢集的群組。 這個僅同步特定群組的選項，稱為*特定範圍同步處理*。 請參閱[設定從 Azure AD 到受控網域的特定範圍同步處理](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization)，以取得指示。

啟用 Azure AD-DS 之後，本機網域名稱服務 (DNS) 伺服器會在 AD 虛擬機器 (VM) 上執行。 將 Azure AD-DS 虛擬網路 (VNET) 設定為使用這些自訂的 DNS 伺服器。 若要找出正確的 IP 位址，請選取 [管理] 類別下的 [屬性]，然後查看 [虛擬網路上的 IP 位址] 下方列出的 IP 位址。

![找出本機 DNS 伺服器的 IP 位址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

選取 [設定] 類別下的 [DNS 伺服器]，將 Azure AD-DS VNET 中的 DNS 伺服器設定變更為使用這些自訂的 IP。 接著，按一下 [自訂] 旁的選項按鈕，在文字方塊中輸入第一個 IP 位址，然後按一下 [儲存]。 使用相同的步驟新增其他 IP 位址。

![更新 VNET DNS 設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)



在啟用安全 LDAP 時，請在憑證的主體名稱或主體替代名稱中放入網域名稱。 例如，如果您的網域名稱是 *contoso.com*，請確定相同的名稱存在於您的憑證主體名稱或主體替代名稱中。 如需詳細資訊，請參閱[針對 Azure AD-DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。


## <a name="check-azure-ad-ds-health-status"></a>檢查 Azure AD-DS 健康狀態
選取 [管理] 類別下方的 [健康情況]，以檢視您的 Azure Active Directory Domain Services 的健康狀態。 請確定 Azure AD-DS 的狀態是綠色的 (執行中)，且同步處理已完成。

![Azure Active Directory Domain Services 健康情況](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

如果 AAD-DS 受到網路安全性群組的保護，您應確定所有[必要連接埠](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)均已列入 AAD-DS 子網路 NSG 規則的白名單中。 

## <a name="create-and-authorize-a-managed-identity"></a>建立和授權受控識別
> [!NOTE]
> 只有 AAD-DS 管理員有權執行此受控識別的授權。

**使用者指派的受控識別**可用來簡化網域服務作業。 當您將受控識別指派給 HDInsight 網域服務參與者角色時，該角色將可讀取、建立、修改和刪除網域服務作業。 HDInsight 企業安全性套件需要特定的網域服務作業，例如建立 OU 和服務主體。 受控識別可建立在任何訂用帳戶中。 如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

若要設定用於 HDInsight ESP 叢集的受控識別，請建立使用者指派的受控識別 (如果您還沒有)。 請參閱[使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)，以取得指示。 接著，請將受控識別指派給 Azure AD-DS 存取控制中的 **HDInsight 網域服務參與者**角色 (必須具有 AAD-DS 管理員權限才能執行此角色指派)。

![Azure Active Directory Domain Services 存取控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

將受控識別指派給 **HDInsight 網域服務參與者**角色，可確保身分識別具有適當的存取權可對 AAD-DS 網域執行特定的網域服務作業。

在建立受控識別並為其指定正確的角色後，AAD-DS 管理員可設定能夠使用此受控識別的人員。 若要設定受控識別的使用者，管理員應在入口網站中選取受控識別，然後按一下 [概觀] 下方的 [存取控制 (IAM)]。 然後，在右側將「受控識別操作員」角色指派給要建立 ESP HDInsight 叢集的使用者或群組。 例如，AAD-DS 管理員可針對 "sjmsi" 受控識別將此角色指派給 "MarketingTeam" 群組，如下圖所示。

![HDInsight 受控識別操作員角色指派](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)


## <a name="create-a-hdinsight-cluster-with-esp"></a>建立具有 ESP 的 HDInsight 叢集

下一個步驟是使用 Azure AD-DS 建立已啟用 ESP 的 HDInsight 叢集。

將 Azure AD-DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 若將它們放在不同的虛擬網路中，您必須將這些虛擬網路對等互連，讓 HDInsight VM 會對網域控制站顯示，並且可新增至網域。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。 

VNET 對等互連後，請將 HDInsight VNET 設定為使用自訂的 DNS 伺服器，並輸入 Azure AD-DS 的私人 IP 作為 DNS 伺服器位址。 當兩個 VNET 使用相同的 DNS 伺服器時，您的自訂網域名稱將解析為正確的 IP，並且可從 HDInsight 存取。 例如，如果您的網域名稱是 "contoso.com"，則在完成此步驟後偵測 "contoso.com" 時，應會解析正確的為 Azure AD-DS IP。 若要測試對等互連是否正確完成，請將 Windows VM 加入至 HDInsight VNET/子網路，並偵測網域名稱或執行 **ldp.exe** 以存取 Azure AD-DS 網域。 然後，將此 Windows VM 加入至網域，以確認用戶端與伺服器之間的所有必要 RPC 呼叫都會成功。

![為對等互連的 VNET 設定自訂 DNS 伺服器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

當您建立 HDInsight 叢集時，您可以在自訂索引標籤中啟用企業安全性套件。

![Azure HDInsight 的安全性和網路功能](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

在您啟用 ESP 後，系統會自動偵測及驗證與 Azure AD-DS 有關的常見錯誤設定。

![Azure HDInsight 企業安全性套件網域驗證](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

及早偵測可讓您在建立叢集之前修正錯誤，以節省時間。

![Azure HDInsight 企業安全性套件失敗的網域驗證](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

建立具有 ESP 的 HDInsight 叢集時，必須提供下列參數：

- **叢集管理員使用者**：為已同步 Azure AD-DS 中的叢集選擇管理員。 此帳戶必須已同步，而且可在 Azure AD-DS 中使用。

- **叢集存取群組**︰您要將其使用者同步至叢集的安全性群組應為 Azure AD-DS 中的可用項目。 例如，HiveUsers 群組。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：例如 ldaps://contoso.com:636。

下列螢幕擷取畫面顯示 Azure 入口網站中的成功設定：

![Azure HDInsight ESP Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

您所建立的受控識別，可在建立新的叢集時從 [使用者指派的受控識別] 下拉式清單中選擇。

![Azure HDInsight ESP Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[使用 ESP 針對 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到具有 ESP 的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
